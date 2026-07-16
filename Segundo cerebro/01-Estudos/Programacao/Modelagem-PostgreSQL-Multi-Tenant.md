# Modelagem de Banco de Dados Multi-Tenant no PostgreSQL

## Introdução
Ao projetar sistemas SaaS (Software as a Service) multi-tenant, um dos maiores desafios de arquitetura é o isolamento dos dados. O objetivo é garantir com segurança absoluta que dados de diferentes empresas/escritórios (tenants) nunca se misturem, ao mesmo tempo em que otimizamos o uso de recursos de banco de dados e mantemos a simplicidade de manutenção da estrutura.

Existem três abordagens clássicas para este problema:
1.  **Banco de dados por Tenant (Database-per-Tenant):** Oferece o isolamento físico mais forte, mas o custo de manutenção e provisionamento escala de forma impraticável.
2.  **Schema por Tenant (Schema-per-Tenant):** Múltiplos schemas lógicos na mesma instância PostgreSQL. Isolamento bom, mas dificulta migrações em lote quando o número de clientes cresce.
3.  **Tabela Compartilhada com Isolamento Lógico (Shared Database):** Todos os dados compartilham o mesmo banco e tabelas, diferenciados por uma coluna identificadora (ex: `tenant_id`). É o modelo mais barato, escalável e fácil de manter, porém exige maior disciplina de desenvolvimento para prevenir vazamentos de dados.

Neste artigo, detalho a minha implementação do **Shared Database** utilizando **PostgreSQL** e **Prisma ORM**.

---

## Estrutura da Modelagem Lógica

Na abordagem escolhida, a chave estrangeira `tenantId` é inserida em todas as entidades que necessitam de barreira de isolamento. Veja o diagrama lógico básico:

```text
      ┌──────────────┐
      │    Tenant    │
      └──────┬───────┘
             │ 1
             │
             │ N
      ┌──────▼───────┐
      │   Cliente    │
      └──────┬───────┘
             │ 1
             │
             │ N
      ┌──────▼───────┐
      │  Documento   │
      └──────────────┘
```

Abaixo está a declaração conceitual dos schemas de tabelas:

```sql
CREATE TABLE tenants (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE clientes (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    doc VARCHAR(20),
    email VARCHAR(255),
    tenant_id INTEGER REFERENCES tenants(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Índices de Performance
Para garantir que as queries filtradas por tenant sejam executadas em tempo sub-milissegundo, configurei índices compostos:

```sql
CREATE INDEX idx_clientes_tenant_id ON clientes(tenant_id);
CREATE INDEX idx_clientes_nome_tenant_id ON clientes(tenant_id, nome);
```
O índice composto garante que as buscas textuais de autocompletar e ordenações por nome já ocorram ordenadamente dentro da fatia de dados de cada tenant.

---

## Mecanismos de Isolamento na Aplicação

### 1. Interceptação via Middleware (JWT)
O cliente autenticado envia um token contendo o `tenantId` encriptado no payload. O middleware do backend Hono valida o token e armazena o identificador no contexto de execução local da requisição:

```typescript
// Middleware de Tenant
export const tenantMiddleware = async (c: Context, next: Next) => {
  const user = c.get('user');
  if (!user || !user.tenantId) {
    return c.json({ error: 'Tenant inválido ou não autenticado' }, 401);
  }
  await next();
};
```

### 2. Filtro Automático de Consultas (Prisma Client)
Para eliminar a chance de vazamento de dados por esquecimento de um desenvolvedor ao escrever queries SQL manuais, podemos encapsular a instância do Prisma Client injetando extensões de consulta.

A extensão intercepta chamadas de leitura (`findMany`, `findFirst`, etc.) e anexa automaticamente o parâmetro `tenantId` nos argumentos:

```typescript
import { PrismaClient } from '@prisma/client';

export const getExtendedPrisma = (tenantId: number) => {
  const prisma = new PrismaClient();
  return prisma.$extends({
    query: {
      $allModels: {
        async $allOperations({ model, operation, args, query }) {
          // Injeta automaticamente o filtro nas operações de busca
          if (['findMany', 'findFirst', 'findUnique', 'count'].includes(operation)) {
            args.where = {
              ...args.where,
              tenantId: tenantId
            };
          }
          // Injeta automaticamente o valor nas operações de inserção
          if (['create', 'update'].includes(operation)) {
            if (args.data) {
              args.data.tenantId = tenantId;
            }
          }
          return query(args);
        }
      }
    }
  });
};
```

Com essa camada de abstração ativa, qualquer chamada feita na rota já está filtrada por padrão, garantindo isolamento total contra vazamento de dados (Zero Trust).

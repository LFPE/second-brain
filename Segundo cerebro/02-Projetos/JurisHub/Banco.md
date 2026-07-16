# 🗄️ Modelagem de Banco de Dados & Multi-Tenancy — JurisHub

O isolamento e a segurança de dados no JurisHub são modelados nativamente no banco de dados e controlados automaticamente através de interceptores na aplicação.

---

## Estrutura do Schema (Prisma ORM)

Todas as entidades de dados sensíveis possuem a relação de isolamento `tenantId`.

```prisma
model Tenant {
  id        Int       @id @default(autoincrement())
  nome      String
  slug      String    @unique
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt
  usuarios  Usuario[]
  clientes  Cliente[]
}

model Usuario {
  id        Int      @id @default(autoincrement())
  nome      String
  email     String   @unique
  senha     String
  role      String   @default("user")
  tenantId  Int
  tenant    Tenant   @relation(fields: [tenantId], references: [id])
  createdAt DateTime @default(now())
}

model Cliente {
  id          Int          @id @default(autoincrement())
  nome        String
  doc         String?
  email       String?
  telefone    String?
  workflow    String       @default("novo")
  tenantId    Int
  tenant      Tenant       @relation(fields: [tenantId], references: [id])
  documentos  Documento[]
}
```

---

## Mecanismo de Isolamento de Queries

Para evitar a falha humana de esquecer filtros de segurança, implementei um middleware de interceptação de rotas:
1.  **Sessão JWT:** O cabeçalho de autenticação do usuário decodifica o token JWT contendo seu `tenantId`.
2.  **Contexto da Requisição:** O `tenantId` é injetado no contexto de execução do Hono.
3.  **Filtro do Provedor de Dados:** Todas as consultas no banco de dados utilizam essa informação contextualmente:
    ```typescript
    const tenantId = c.get('user').tenantId;
    const clientes = await prisma.cliente.findMany({
      where: { tenantId }
    });
    ```

---

## Estrutura do Provedor de Storage (Google Drive API)

Quando um novo cliente é cadastrado, o sistema cria automaticamente uma subpasta sob a pasta raiz do escritório no Drive com a seguinte padronização:

```text
Pasta Raiz JurisHub/
└── Cliente - [Nome do Cliente]/
    ├── Contrato.pdf
    └── Comprovante.pdf
```
O ID de pasta gerado é persistido na tabela do banco de dados, permitindo que downloads e uploads subsequentes acessem a pasta correspondente no Drive sem varreduras lineares lentas.

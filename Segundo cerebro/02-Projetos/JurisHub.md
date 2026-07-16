# Estudo de Caso de Arquitetura: JurisHub

Este documento descreve detalhadamente a arquitetura de software, decisões de design, modelagem de banco de dados e estratégias de implantação que utilizei para construir o **JurisHub**, um ecossistema SaaS (Software as a Service) multi-tenant voltado para a gestão e automação operacional de escritórios de advocacia.

---

## 🏛️ 1. Filosofia de Design e Arquitetura Geral

O sistema foi concebido sobre duas premissas principais:
1.  **Custo Operacional Otimizado (Free-Tier Ready):** Toda a infraestrutura foi projetada para operar dentro de limites gratuitos de serviços gerenciados em nuvem, sem comprometer a performance ou a segurança.
2.  **Garantia de Isolamento de Dados (Zero Trust Multi-Tenancy):** Por se tratar de dados jurídicos sensíveis, o isolamento dos dados entre diferentes escritórios (tenants) é tratado no nível mais baixo da aplicação (banco de dados e middlewares do servidor).

```text
                                  ┌────────────────────────┐
                                  │      Client (SPA)      │
                                  │   Vite + React (Edge)  │
                                  └───────────┬────────────┘
                                              │ HTTPS (Direct API calls)
                                              ▼
                                  ┌────────────────────────┐
                                  │   Hono API Gateway     │
                                  │      Node.js Runtime   │
                                  └─────┬────────────┬─────┘
                                        │            │
                  ┌─────────────────────┘            └─────────────────────┐
                  ▼                                                        ▼
      ┌───────────────────────┐                                ┌───────────────────────┐
      │   Prisma ORM Client   │                                │  Google Drive Engine  │
      │   Multi-tenant Query  │                                │  Service Account Auth │
      └───────────┬───────────┘                                └───────────┬───────────┘
                  │                                                        │
                  ▼                                                        ▼
      ┌───────────────────────┐                                ┌───────────────────────┐
      │     PostgreSQL DB     │                                │  Dynamic Client Fold  │
      │  (Row-Level Filters)  │                                │   Storage Container   │
      └───────────────────────┘                                └───────────────────────┘
```

---

## 🗄️ 2. Engenharia e Modelagem de Banco de Dados

### Escolha da Engine: PostgreSQL vs. SQLite
Durante a fase de design, decidi migrar o banco de dados de desenvolvimento (SQLite) para o **PostgreSQL** para a implantação em produção. A escolha baseou-se nos seguintes critérios:
*   **Concorrência Real:** O SQLite bloqueia o arquivo em operações de escrita (escrita sequencial), o que é inviável para uma plataforma multi-tenant com múltiplos usuários salvando dados simultaneamente.
*   **Recursos Avançados de Busca:** O PostgreSQL oferece suporte nativo a buscas textuais complexas e extensões como `pgvector`, necessárias para armazenar embeddings vetoriais usados no nosso motor de Inteligência Artificial com RAG.

### Modelo de Dados Multi-Tenant
Utilizei uma estratégia de **compartilhamento de banco de dados com isolamento lógico**. Cada tabela importante possui um identificador único de escritório (`tenantId`):

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
  role      String   @default("user") // admin, advogado
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
  workflow    String       @default("novo") // novo, reuniao, contrato, ativo
  tenantId    Int
  tenant      Tenant       @relation(fields: [tenantId], references: [id])
  documentos  Documento[]
  timeline    Timeline[]
}
```

### Isolamento Automático via Query Middleware
Para mitigar o risco de vazamento de dados por erro humano (esquecer um filtro `where: { tenantId }` em consultas SQL), configurei uma camada interceptora de requisições:
*   Desenvolvi um `tenantMiddleware` no servidor Hono.
*   Esse middleware extrai o token JWT enviado no cabeçalho `Authorization` do cliente, decodifica o payload e obtém o `tenantId` da sessão ativa.
*   O ID do tenant é acoplado ao contexto de execução da requisição (`c.set('user', decodificado)`).
*   Todas as rotas de banco de dados recuperam esse valor diretamente do contexto para realizar buscas seguras:
    ```typescript
    const tenantId = c.get('user').tenantId;
    const clientes = await prisma.cliente.findMany({
      where: { tenantId }
    });
    ```

---

## ⚙️ 3. Desenvolvimento do Servidor API (Backend)

Optei pelo framework **Hono** rodando em Node.js com TypeScript pela sua performance bruta próxima ao metal, roteamento extremamente veloz baseado em RegEx e excelente portabilidade para runtimes de borda (Edge).

### Estrutura de Módulos (Rotas)
O servidor foi decomposto em módulos independentes na pasta `src/server/modules/`:
*   `auth.routes.ts`: Gerencia o fluxo de login de usuários, decodificação de senhas com `bcryptjs` e emissão de tokens JWT com expiração de 1 dia.
*   `clientes.routes.ts`: CRUD completo de clientes, incluindo o histórico contextual para o painel lateral rápido.
*   `dashboard.routes.ts`: Agrega estatísticas financeiras de faturamento, andamento de processos e distribuição de leads por estágio no Kanban para exibição nos cards métricos de topo.
*   `documentos.routes.ts`: Gerencia o upload de arquivos e sincronização com provedores externos de armazenamento.

### Segurança e Sanitização contra Vulnerabilidades
Seguindo práticas avançadas de programação segura, implementei medidas rigorosas contra ataques comuns descritos no OWASP Top 10:
*   **Prevenção de XSS (Cross-Site Scripting):** Criei um middleware utilitário de sanitização (`sanitizeText`) que limpa payloads de texto antes de salvá-los no banco de dados. Ele remove expressões contendo scripts maliciosos ou injeções de tags HTML.
*   **Controle de Taxa de Requisições (Rate Limiting):** Para impedir ataques de força bruta nas rotas de login e cadastro, configurei o `loginRateLimit` e o `registerRateLimit`. Usando um cache em memória indexado por IP de origem, bloqueamos requisições que excedam 5 tentativas por minuto.

---

## 📂 4. Provedor de Armazenamento Inteligente (Google Drive v3 API)

Para evitar custos com armazenamento de blobs (S3) na AWS ou no Supabase, criei um sistema de armazenamento automático de arquivos que usa a API do Google Drive (v3).

### Funcionamento do Fluxo do Adaptador (`storage.ts`):
1.  **Autenticação sem Interação Humana:** O backend usa uma **Service Account** do Google Cloud Platform. A autenticação é feita fornecendo as credenciais `client_email` e `private_key` (chave privada RSA PEM) em formato de variáveis de ambiente.
2.  **Criação de Pastas Dinâmicas:** Ao criar um novo cliente na plataforma, o backend gera uma estrutura lógica e chama o método `getOrCreateSubfolder`. Esse método consulta se já existe uma pasta para o cliente sob o ID de pasta raiz configurado no escritório.
3.  **Encapsulamento de Arquivos:** Se a pasta não existir, ela é gerada com o formato `Cliente - [NomeCliente]`. O ID da pasta recém-criada é indexado. Todos os uploads de PDFs e documentos subsequentes daquele cliente são carregados diretamente para esta subpasta específica via streams do Node.js, mantendo o Drive do escritório limpo, rastreável e sem necessidade de gerenciamento manual.

---

## 🎨 5. Interface Premium SaaS & Design System

A interface gráfica foi redesenhada sob o conceito de design profissional de ferramentas modernas do mercado SaaS.

### Arquitetura de Tokens de Design
A fundação de estilos está definida na pasta `src/client/design-system/`:
*   `colors.ts`: Configura a paleta de cores escura do sistema (Zinc 950 como fundo e Amber 500/400 como destaque de foco). Define cores e variações LED de status financeiro (verde para em dia, laranja para pendente e vermelho para inadimplente).
*   `motion.ts`: Centraliza tempos e easings padrões para o Framer Motion. Define os easings `easeOutExpo` (`[0.16, 1, 0.3, 1]`) com duração estrita de `180ms` (standard) e `240ms` (slow) para transições fluídas sem lag de renderização.
*   `radius.ts` & `shadow.ts`: Padronização de cantos (`rounded-2xl` para elementos estruturais) e sombras profundas (`shadow-2xl`).

### Componentização do Workspace CRM
Para otimizar o desempenho do navegador e evitar re-renderizações desnecessárias, limpei estilos inline e estruturei a tela principal em subcomponentes isolados:
*   `CRMFilters.tsx`: Painel lateral esquerdo que agrupa os filtros rápidos de carteira e inadimplência.
*   `CRMToolbar.tsx`: Controla as ações globais superiores, integrando a busca textual com um mecanismo de debounce de `300ms` (evita disparar requisições à API para cada caractere digitado).
*   `Drawer.tsx`: Modal lateral que utiliza Focus Trap (aprisionamento do foco do teclado) e fechamento dinâmico ao pressionar `Escape` ou clicar fora do painel, garantindo conformidade com regras de acessibilidade (A11y).
*   `DetailsPanel.tsx`: Painel deslizante à direita (slide-over) que busca e exibe a timeline de andamento do cliente, lista de processos relacionados e área de upload/download de documentos no Drive do cliente.
*   `LoadingState.tsx` & `EmptyState.tsx`: Skeletons animados desenhados sob medida para simular a interface em carregamento, eliminando telas brancas e aumentando a percepção de performance (UX).

---

## 🚀 6. Otimização de Produção e Estratégia de Deploy

A infraestrutura foi configurada para ser resiliente às limitações impostas pelos planos gratuitos convencionais das plataformas de deploy.

### Bypass de Limites na Vercel (Timeout de 10s)
Plataformas Serverless como a Vercel impõem um limite estrito de **10 segundos** para execução de requisições de API no plano gratuito. Como as operações baseadas em Inteligência Artificial e processamento de RAG excediam rotineiramente esse limite, apliquei uma otimização arquitetural:
*   **Divisão Física:** A interface (frontend estático compilado) roda na Vercel. O servidor (backend dinâmico baseado em Hono/Node) roda na Render.
*   **Chamadas Diretas:** Configurei a aplicação em produção para ignorar proxies da Vercel e injetar a URL real do backend da Render (`VITE_API_URL`) no frontend. Todas as requisições complexas de IA de longa duração são disparadas diretamente do navegador do usuário para o servidor da Render, contornando a barreira dos 10 segundos da Vercel.

### Configuração de Segurança no Acesso
*   **Remoção de Vercel Auth Wall:** Desabilitei as restrições e telas de autenticação automática de equipe da Vercel nas configurações do projeto para permitir acesso direto e limpo ao domínio customizado.
*   **Restrição de Cadastro Interno:** Configurei uma whitelist rígida no backend e removi o fluxo público de registro do frontend. Apenas os emails `admin@jurishub.com` e `yasmimpaloma.vs18@gmail.com` estão habilitados no servidor para autenticação.

### Script de Automação de Deploy Local
Para simplificar o fluxo de publicação contínua sem depender de builds lentas na nuvem, automatizei o deploy diretamente pela linha de comando:
1.  Geração da build estática otimizada do frontend: `npx vite build` (saída na pasta `dist/`).
2.  Cópia automática do arquivo de regras e redirecionamentos de rota `vercel.json` para dentro do diretório `dist/`.
3.  Criação dinâmica do link de projeto da Vercel apontando para o ID do projeto `jurishub` (`dist/.vercel/project.json`).
4.  Upload instantâneo e publicação em produção usando a Vercel CLI: `vercel --prod --yes` executado diretamente de dentro da pasta `dist/`.

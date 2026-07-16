# JurisHub

## Visão Geral do Projeto
O **JurisHub** é uma plataforma SaaS (Software as a Service) multi-tenant de alta performance desenvolvida para modernizar e centralizar a operação de escritórios de advocacia. O sistema integra inteligência de dados, gestão de clientes (CRM Kanban), timesheet dinâmico, liquidação de sentenças e um motor de Inteligência Artificial para redação de petições baseado em RAG (Retrieval-Augmented Generation).

Toda a arquitetura foi desenhada por mim com foco em escalabilidade horizontal, segurança de dados (Zero Trust) e custo operacional de infraestrutura otimizado.

---

## Stack Tecnológica Utilizada
- **Frontend:** React 18, TypeScript, TailwindCSS, Framer Motion, Lucide Icons, Vite.
- **Backend:** Node.js, TypeScript, Hono Framework (motor web leve e de baixíssima latência executado em runtime rápido).
- **Banco de Dados & ORM:** PostgreSQL hospedado no Supabase, gerenciado via Prisma ORM.
- **Armazenamento de Arquivos:** Integração direta com a API do Google Drive (Service Account).
- **Inteligência Artificial:** Integração com APIs LLM (Gemini Pro) e embeddings vetoriais locais para busca semântica em acervos de peças.
- **Hospedagem & CI/CD:** Frontend distribuído via Vercel Edge Network e backend rodando no Render.

---

## Arquitetura de Software e Engenharia

### 1. Modelo de Dados Multi-Tenant e Isolamento
Implementei uma arquitetura multi-tenant lógica com isolamento no nível de banco de dados para garantir que dados de escritórios diferentes jamais se cruzem:
*   Todas as tabelas críticas (`Cliente`, `Processo`, `Andamento`, `Financeiro`, `Usuario`) possuem a chave estrangeira `tenantId`.
*   Desenvolvi um `tenantMiddleware` no backend que intercepta as requisições autenticadas, extrai o escopo do usuário via JWT e injeta automaticamente os filtros do `tenantId` em todas as queries executadas pelo Prisma Client.

### 2. Automação de Anexos no Google Drive
Para contornar custos de armazenamento de arquivos de mídia (PDFs, comprovantes e fotos de perfil), criei um adaptador personalizado para a API do Google Drive v3 (`GoogleDriveStorageProvider`):
*   O sistema interage com o Drive usando autenticação por chave JSON privada de uma Service Account.
*   **Organização Estruturada:** Ao criar um cliente no banco de dados, o backend aciona o provedor de storage que cria automaticamente uma pasta exclusiva com o nome `Cliente - [Nome]` e armazena o ID no banco. Todos os uploads de documentos e anexos subsequentes são gravados de forma isolada dentro da pasta do respectivo cliente, mantendo a estrutura limpa e o consumo de espaço no Drive do escritório totalmente otimizado.

### 3. Motor RAG & Processamento de Peças em Background
Desenvolvi um sistema de busca semântica em acervos jurídicos para acelerar a redação de petições:
*   **Vetorização:** As peças enviadas pelo escritório são processadas, fragmentadas em chunks e enviadas para modelos de embeddings.
*   **Arquitetura Assíncrona:** Para evitar que o parsing e a geração de vetores de arquivos pesados travem o event loop do servidor API Hono, implementei uma fila de tarefas local em background (`aiWorker.ts` / `scheduler.ts`) que consome e indexa os arquivos em concorrência controlada.

### 4. Design System & CRM Kanban do Zero
Refatorei toda a interface do CRM do sistema para criar um painel de alta fidelidade visual (Premium SaaS UI/UX):
*   **Design System Dinâmico:** Criei um ecossistema de tokens em TypeScript (`colors.ts`, `spacing.ts`, `radius.ts`, `shadow.ts`, `typography.ts`, `motion.ts`) para padronizar cores semânticas, arredondamentos (rounded-2xl/3xl), sombras com elevação hover e tempos de transição (180ms/240ms).
*   **Componentização Estrita:** Fragmentei a tela de CRM em componentes modulares isolados e memoizados (`CRMFilters`, `CRMToolbar`, `Drawer`, `DetailsPanel`, `KanbanCard`, `Avatar`, `StatusBadge`, `EmptyState`, `LoadingState`).
*   **Acessibilidade e Usabilidade:** Implementei Focus Trap (trava de foco Tab) e suporte a tecla `ESC` para fechamento de painéis laterais. Usei estados de carregamento estruturados em Skeletons em vez de loaders convencionais.
*   **Ficha Contextual Rápida (Slide-Over):** Desenvolvi um painel deslizante que abre à direita ao clicar em qualquer card do Kanban, permitindo visualizar a timeline de andamento do cliente, consultar processos e fazer uploads diretamente para o Google Drive sem tirar o advogado do fluxo principal do CRM.

### 5. Segurança Avançada
*   **Whitelist de Login:** Restringi os acessos à plataforma configurando uma whitelist no servidor que autoriza a autenticação exclusivamente para os e-mails `admin@jurishub.com` e `yasmimpaloma.vs18@gmail.com`.
*   **Bloqueio de Cadastro Público:** Desativei a rota pública de registro de novos usuários no backend e ocultei o formulário de cadastro na tela de login. O sistema funciona estritamente em modo de convite/administração interna.
*   **Sanitização de Inputs:** Middleware de segurança de input (`sanitizeText`) que limpa tags HTML e scripts injetados nos campos de texto para barrar vulnerabilidades de XSS (Cross-Site Scripting).

---

## Estrutura de Implantação (Production Deploy)
Gerenciei e configurei o provisionamento completo de produção do ecossistema:
1.  **Vercel Routing Proxy Bypass:** Para contornar a restrição de timeout de 10 segundos para Serverless Functions na Vercel (que quebrava requisições complexas de IA), alterei a comunicação direta da aplicação. Adicionei variáveis de ambiente dinâmicas (`VITE_API_URL` apontando direto para a Render) para que chamadas longas de IA processem sem limites de tempo na borda.
2.  **Ajustes de CORS:** Adicionei regras de controle de cabeçalhos no backend Hono para validar as requisições de origem do domínio de produção e liberar credenciais de forma segura.
3.  **Vercel SSO Bypass:** Desabilitei a tela de segurança de autenticação de time/Vercel Auth Wall que bloqueava os acessos diretos.
4.  **CLI Deployment Script:** Criei um script para compilar o frontend estático localmente com `vite build`, reescrever dinamicamente o arquivo de redirecionamento `vercel.json` e as configurações de projeto do alias `jurishub-app.vercel.app` dentro da pasta `dist/` para realizar deploys rápidos diretamente da linha de comando com `vercel --prod --yes`.

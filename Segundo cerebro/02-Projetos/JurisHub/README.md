# Projeto: JurisHub (CRM & Plataforma SaaS Jurídica) ⚖️

O **JurisHub** é uma plataforma SaaS de alta performance desenvolvida para modernizar e centralizar a operação de escritórios de advocacia, integrando inteligência de dados, gestão de clientes, timesheet dinâmico e Inteligência Artificial jurídica.

---

## 🗂️ Wiki do Projeto — Índice de Notas

Para facilitar a consulta e a manutenção do conhecimento do JurisHub, a documentação está fragmentada nos seguintes módulos específicos:

### Engenharia de Software
*   [[Arquitetura\|💻 Arquitetura de Sistemas & Fluxos]]: Desenho técnico da comunicação cliente-servidor (CDN vs Servidor de aplicação) e diagramas Mermaid.
*   [[Banco\|🗄️ Modelagem de Banco de Dados & Multi-Tenancy]]: Schema Prisma, isolamento lógico de escritórios (`tenantId`) e otimização de consultas no PostgreSQL.
*   [[IA\|🤖 Motor de Inteligência Artificial & Pipeline RAG]]: Funcionamento da busca vetorial, cálculo de similaridade com pgvector e workers em background.

### Interfaces & Serviços
*   [[Frontend\|🎨 Design System & Workspace CRM]]: Componentização do quadro Kanban (React.memo), debounces de busca, estabilidade visual (CLS) e tokens de cores/motion.
*   [[Backend\|⚙️ Motor de API & Rotas do Servidor]]: Arquitetura Hono Framework, modularização em rotas e gerenciador de contexto de requisições.

### Governança & Segurança
*   [[Seguranca\|🛡️ Proteção & Segurança da Informação]]: Sanitização contra injeções (XSS), whitelist rígida de autenticação e middlewares de controle de taxa.
*   [[Decisoes\|✔️ Registro de Decisões de Engenharia (ADRs)]]: Links de atalho para os registros de decisões arquiteturais do projeto.

### Planejamento & Evolução
*   [[Roadmap\|📅 Planejamento de Lançamentos & Roadmaps]]: Controle de milestones e tarefas de desenvolvimento.
*   [[Ideias\|💡 Banco de Ideias & Melhorias Futuras]]: Funcionalidades de valor propostas para futuras iterações.
*   [[Problemas\|❌ Registro de Problemas Encontrados & Resoluções]]: Histórico de bugs de infraestrutura resolvidos.
*   [[Aprendizados\|🧠 Lições Aprendidas & Melhores Práticas]]: Conhecimento de engenharia consolidado durante o desenvolvimento.

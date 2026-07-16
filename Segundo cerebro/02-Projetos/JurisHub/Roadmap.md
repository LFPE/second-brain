# 📅 Planejamento de Lançamentos & Roadmaps — JurisHub

O ciclo de desenvolvimento e metas do JurisHub está estruturado em fases de entrega focadas em escalabilidade e usabilidade.

---

## Roadmap Geral

- [x] **Fase 1: Concepção & Modelagem de Banco**
  - [x] Criação de tabelas com isolamento `tenantId`.
  - [x] Configuração inicial de conexões Supabase com Prisma.
- [x] **Fase 2: Estrutura de Storage & IA Core**
  - [x] Desenvolvimento de adaptador para upload assíncrono no Google Drive API.
  - [x] Pipeline RAG inicial para busca semântica em acervos de peças.
- [x] **Fase 3: Refatoração Premium UI/UX & Segurança**
  - [x] Criação de subcomponentes modulares e memoizados no Kanban do CRM.
  - [x] Implementação de Whitelist rígida de login.
  - [x] Desativação de cadastro público e sanitização contra XSS.
- [x] **Fase 4: Implantação e Correções de Produção**
  - [x] Bypass de limites de timeout da Vercel apontando o frontend diretamente para a Render.
  - [x] Vinculação de domínio de produção em `https://jurishub-app.vercel.app`.
- [ ] **Fase 5: Expansão do Produto**
  - [ ] Implementação de exportação de dados de timesheet e faturamento para CSV/Excel.
  - [ ] Sistema de relatórios gráficos de produtividade integrado ao dashboard.
  - [ ] Integração com WhatsApp API para notificações automatizadas de prazos processuais.

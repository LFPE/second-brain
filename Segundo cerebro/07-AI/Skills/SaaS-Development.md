# 🛠️ Skill de IA: Desenvolvimento SaaS Avançado

Diretrizes e habilidades que a IA deve invocar para acelerar e otimizar o desenvolvimento de plataformas SaaS (Multi-tenant, APIs seguras).

---

## 1. Habilidade de Isolamento de Dados
Ao gerar rotas, models e queries para um sistema SaaS, a IA deve injetar nativamente o isolamento lógico (`tenantId`) em todas as entidades.

---

## 2. Habilidade de Otimização de Performance
*   Utilização de consultas indexadas no Postgres.
*   Memoização de componentes React com `useCallback` e `React.memo`.
*   Uso de bundlers leves para Edge (como Cloudflare Workers).

---

## 3. Checklist de Geração
- [ ] O modelo contém a coluna `tenantId`?
- [ ] As chaves estrangeiras estão mapeadas corretamente?
- [ ] O payload de entrada está sanitizado contra XSS?

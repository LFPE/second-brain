# ❌ Registro de Problemas Encontrados — System Tracker

Histórico de erros solucionados no System Tracker:

---

## Incidente: Travamento de Migrações do D1 local com Drizzle

*   **Problema:** Tentativas de migrar esquemas locais causavam erros de bloqueio de arquivo no Wrangler.
*   **Solução:** Limpeza e remoção do arquivo local de persistência `.wrangler/` e reinicialização forçada do Wrangler Dev Environment.
*   **Tempo gasto:** 1 hora.

---

## Incidente: Incompatibilidade de Tipos com Wrangler Typegen

*   **Problema:** Falta de suporte de tipagem do TypeScript nos bindings de banco de dados no Cloudflare Workers.
*   **Solução:** Configuração do script de geração automática de tipos `wrangler types` no pipeline de build.
*   **Tempo gasto:** 1 hora.

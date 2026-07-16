# ADR 0001: Uso de Cloudflare D1 e SQLite na Borda para o System Tracker

*   **Status:** Aprovado
*   **Data:** 2026-06-24
*   **Autor:** Felipe Pinho

---

## Contexto
O System Tracker precisa monitorar e salvar logs de qualidade e execução de processos. Por se tratar de um serviço de monitoramento leve de background, necessitamos de latência mínima e baixo custo de transação de banco de dados.

---

## Decisão
Decidi adotar o banco **Cloudflare D1** (SQLite relacional na borda) integrado ao Cloudflare Workers.

---

## Motivo
*   **Performance Borda (Edge):** O banco de dados e a API executam fisicamente no mesmo datacenter da Cloudflare na borda da rede, reduzindo o tempo de conexão.
*   **Custo Zero:** Enquadra-se no limite do plano gratuito do ecossistema Cloudflare.
*   **Simplicidade:** O SQLite atende perfeitamente à modelagem simples de auditoria de dados do Tracker.

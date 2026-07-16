# ADR 0001: Escolha de Cloudflare Pages para Hospedagem do Portfólio

*   **Status:** Aprovado
*   **Data:** 2026-06-24
*   **Autor:** Felipe Pinho

---

## Contexto
O portfólio profissional estático necessita de hospedagem simples, rápida, com suporte SSL e custos nulos, integrada diretamente a commits do Git.

---

## Decisão
Decidi hospedar o site no **Cloudflare Pages**.

---

## Motivo
*   **Performance Edge CDN:** Distribuição imediata do HTML/CSS globalmente.
*   **Continuous Deployment:** Integração nativa com o GitHub que realiza o deploy a cada commit na branch `main`.

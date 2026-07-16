# ADR 0002: Escolha de Drizzle ORM sobre o Prisma para o System Tracker

*   **Status:** Aprovado
*   **Data:** 2026-06-24
*   **Autor:** Felipe Pinho

---

## Contexto
O System Tracker roda em Cloudflare Workers (Edge runtime). O Prisma possui uma engine de compilação pesada que aumenta de forma indesejada o tamanho final do pacote do Worker (bundle size) e causa tempos de cold start.

---

## Decisão
Decidi adotar o **Drizzle ORM** para gerenciar consultas de banco de dados e migrações.

---

## Motivo
*   **Tamanho de Pacote Mínimo:** Drizzle é extremamente leve e baseado em código TypeScript puro, sem engines pesadas em Rust/Binary.
*   **Compatibilidade Nativa:** Integra-se perfeitamente aos Workers e Cloudflare D1.

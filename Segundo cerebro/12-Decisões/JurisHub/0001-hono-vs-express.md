# ADR 0001: Escolha do Hono Framework sobre o Express para API Server

*   **Status:** Aprovado
*   **Data:** 2026-07-13
*   **Autor:** Felipe Pinho

---

## Contexto
O JurisHub necessita de uma API de backend de altíssima performance para lidar com concorrência e processamentos em tempo real de timelines e andamentos de processos. Historicamente, o Express é a escolha padrão em Node.js, mas queríamos avaliar alternativas mais modernas e otimizadas.

---

## Decisão
Decidi adotar o **Hono Framework** rodando em Node.js como motor do servidor de API.

---

## Motivo
*   **Performance Bruta:** O Hono utiliza roteamento baseado em RegEx linear extremamente otimizado, entregando latência de processamento significativamente menor que o Express.
*   **Tipagem Nativa:** Hono é escrito com suporte total e nativo para TypeScript, fornecendo melhor inferência de tipos em middlewares de segurança.
*   **Compatibilidade com Edge:** O Hono foi projetado para rodar em runtimes web padrão (Cloudflare Workers, Deno, Node), facilitando migrações futuras para ambientes distribuídos na borda sem reescrever rotas.

---

## Alternativas Consideradas
*   **Express:** Comunidade gigantesca, mas pesado, síncrono por padrão e sem atualizações estruturais robustas de performance recentes.
*   **Fastify:** Excelente desempenho, mas possui uma curva de aprendizado e configuração de schemas JSON mais complexa do que a simplicidade minimalista do Hono.

---

## Trade-Offs
*   *Menor Comunidade:* O Hono tem uma biblioteca menor de plugins de terceiros se comparado ao Express, exigindo a escrita manual de middlewares utilitários simples (como sanitizadores).
*   *Maior Velocidade de Resposta:* Performance de processamento de rotas significativamente maior e inicializações frias (cold starts) inexistentes.

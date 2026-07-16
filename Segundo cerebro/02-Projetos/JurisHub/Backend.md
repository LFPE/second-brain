# ⚙️ Motor de API & Rotas do Servidor — JurisHub

O backend do JurisHub é estruturado sobre o **Hono Framework** rodando em Node.js com TypeScript. A escolha do Hono baseou-se em sua latência mínima e no ecossistema modular de rotas e middlewares.

---

## Estrutura do Roteador (API Router)

As rotas são modularizadas na pasta `src/server/modules/` e expostas sob o prefixo `/api`:

*   **Auth (`auth.routes.ts`):** Rota de login baseada em credenciais whitelisted, validação de hash Bcrypt e geração de tokens JWT.
*   **Clientes (`clientes.routes.ts`):** CRUD completo da carteira de clientes, incluindo histórico de timeline e logs de andamento.
*   **Dashboard (`dashboard.routes.ts`):** Agrega métricas e estatísticas para os cards superiores.
*   **Documentos (`documentos.routes.ts`):** Gerenciamento e uploads assíncronos no storage do Google Drive.

---

## Middlewares de Fluxo Globais

*   `authMiddleware`: Valida se o cabeçalho `Authorization` contém um token JWT válido e descriptografa o payload do usuário.
*   `tenantMiddleware`: Extrai o escopo do usuário autenticado e injeta o `tenantId` no contexto para segregar todas as operações de banco de dados.
*   `cors`: Controla as origens permitidas de produção e cabeçalhos autorizados para chamadas diretas cross-origin.

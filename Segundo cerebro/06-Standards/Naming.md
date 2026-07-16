# Naming Standards 🏷️

Padrões de nomenclatura para arquivos, diretórios, variáveis e tabelas de banco de dados.

---

## 1. Nomenclatura de Arquivos e Pastas
*   **Componentes React (Views/Components):** Utilizar PascalCase (ex: `KanbanCard.tsx`, `DetailsPanel.tsx`).
*   **Arquivos de Scripts e Código Auxiliar:** Utilizar camelCase ou kebab-case (ex: `storage.ts`, `ai-worker.ts`).
*   **Arquivos Markdown no Second Brain:** Utilizar CamelCase ou Kebab-Case com emojis opcionais nos títulos de topo para links de MOC fluídos.

---

## 2. Nomenclatura no Código (TS/JS)
*   **Variáveis e Funções:** Utilizar camelCase (ex: `const tenantId`, `function getClientes()`).
*   **Interfaces e Classes:** Utilizar PascalCase (ex: `interface UserSession`, `class StorageAdapter`).
*   **Variáveis de Ambiente:** Utilizar UPPER_SNAKE_CASE (ex: `VITE_API_URL`, `DATABASE_URL`).

---

## 3. Nomenclatura de Banco de Dados
*   **Modelos no Prisma ORM:** Utilizar PascalCase no singular (ex: `model Cliente`, `model TimelineEvent`).
*   **Campos de Tabelas:** Utilizar camelCase no Prisma e snake_case ao gerar as colunas SQL nativas se exigido pela infraestrutura legada.

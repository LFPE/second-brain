# 🗄️ Banco de Dados & Drizzle ORM — System Tracker

O banco de dados do System Tracker é relacional, distribuído de forma nativa e gerenciado via Drizzle ORM.

---

## Schema Declarativo (Drizzle)

```typescript
import { sqliteTable, text, integer } from 'drizzle-orm/sqlite-core';

export const tarefas = sqliteTable('tarefas', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  nome: text('nome').notNull(),
  operador: text('operador').notNull(),
  status: text('status').default('pendente'),
  timestamp: integer('timestamp', { mode: 'timestamp' }).default(new Date()),
});
```

---

## Estrutura do D1 SQL Database
As migrações SQL são declaradas localmente e enviadas à borda usando comandos do Wrangler CLI:

```bash
wrangler d1 migrations create tracker-db-migration
wrangler d1 migrations apply tracker-db-migration --local
```
As informações locais são persistidas na pasta `.wrangler/` durante a fase de testes e aplicadas em produção diretamente nos datacenters globais da Cloudflare.

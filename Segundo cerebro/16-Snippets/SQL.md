# Snippets: SQL Reutilizáveis 🗄️

Biblioteca de consultas SQL úteis para PostgreSQL e SQLite.

---

## 1. Similaridade de Cosseno (PostgreSQL pgvector)
Calcula a distância vetorial entre os chunks indexados e o vetor de busca enviado pela LLM:

```sql
SELECT id, conteudo, (embedding <=> '[0.015, -0.02, ...]'::vector) as distancia
FROM "DocumentChunk"
WHERE "tenantId" = 1
ORDER BY distancia ASC
LIMIT 3;
```

---

## 2. Inserção com UUID Seguro (PostgreSQL)
Gerador nativo de identificadores UUIDv4 seguro em tabelas PostgreSQL:

```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

INSERT INTO tenants (id, nome, slug)
VALUES (uuid_generate_v4(), 'Escritório Alfa', 'escritorio-alfa');
```

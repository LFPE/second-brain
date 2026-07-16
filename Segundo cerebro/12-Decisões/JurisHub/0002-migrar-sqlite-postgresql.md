# ADR 0002: Migração de SQLite para PostgreSQL (Supabase) em Produção

*   **Status:** Aprovado
*   **Data:** 2026-07-13
*   **Autor:** Felipe Pinho

---

## Contexto
O desenvolvimento inicial foi construído usando SQLite local (`dev.db`). No entanto, para escalar o JurisHub como uma plataforma SaaS multi-tenant com concorrência real e processamentos de dados mais pesados de IA, a persistência em arquivo único local do SQLite tornou-se um limitador.

---

## Decisão
Decidi migrar o banco de dados de produção para o **PostgreSQL** hospedado no Supabase.

---

## Motivo
*   **Concorrência Multiusuário:** O PostgreSQL resolve problemas de concorrência com bloqueios em nível de linha, impedindo erros de "Database Locked" comuns sob escrita concorrente no SQLite.
*   **Suporte Vetorial (pgvector):** Suporte nativo a extensões PostgreSQL para armazenamento de embeddings de IA.
*   **Recursos SaaS:** Suporte avançado a backups automatizados e replicação.

---

## Trade-Offs
*   *Complexidade de Infraestrutura:* Exige gerenciar conexões de rede seguras externas no Supabase em vez de ler um arquivo de disco local.
*   *Estabilidade Operacional:* Elimina gargalos de processamento concorrente de leads por múltiplos advogados.

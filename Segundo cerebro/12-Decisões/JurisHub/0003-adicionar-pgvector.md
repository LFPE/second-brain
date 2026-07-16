# ADR 0003: Uso de pgvector para Indexação e Busca Vetorial

*   **Status:** Aprovado
*   **Data:** 2026-07-13
*   **Autor:** Felipe Pinho

---

## Contexto
O motor de redação jurídica inteligente baseado em RAG do JurisHub requer a recuperação semântica ultrarrápida de trechos de petições anteriores e jurisprudências para enriquecer o contexto de geração de texto com LLMs. Precisávamos decidir onde persistir e pesquisar esses vetores gerados.

---

## Decisão
Adotei a extensão **`pgvector`** diretamente na instância do PostgreSQL para armazenar os blocos de embeddings vetoriais.

---

## Motivo
*   **Banco de Dados Unificado:** Armazenar os embeddings no mesmo banco que as tabelas de dados relacionais simplifica backups, integridade referencial por chaves estrangeiras e reduz o custo operacional de contratar um banco vetorial exclusivo (como Pinecone ou Milvus).
*   **Performance:** Operações de busca utilizando o operador de distância de cosseno do `pgvector` atendem perfeitamente aos requisitos de velocidade do JurisHub.

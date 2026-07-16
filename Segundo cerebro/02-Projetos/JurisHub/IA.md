# 🤖 Motor de Inteligência Artificial & Pipeline RAG — JurisHub

O sistema de geração de petições jurídicas e consultas inteligentes do JurisHub foi projetado sob a arquitetura **RAG (Retrieval-Augmented Generation)**. Isso previne alucinações e garante respostas baseadas unicamente no acervo documental interno do escritório.

---

## Fluxo do Pipeline RAG

```mermaid
sequenceDiagram
    participant User as Navegador do Usuário
    participant API as Servidor Hono
    participant DB as PostgreSQL (pgvector)
    participant LLM as Gemini Pro API

    User->>API: Envia Pergunta / Requisição
    API->>API: Vetoriza Pergunta (Embedding)
    API->>DB: Busca Chunks por Similaridade de Cosseno (<=>)
    DB-->>API: Retorna Textos Mais Relevantes
    API->>API: Injeta Textos no Prompt de Contexto
    API->>LLM: Envia Prompt Consolidado
    LLM-->>API: Retorna Resposta Gerada
    API-->>User: Entrega Resposta ao Usuário
```

---

## Componentes Técnicos do Pipeline

### 1. Chunking e Divisão de Documentos
Para evitar perdas de significado e extrapolações do limite de contexto das chamadas da LLM, os documentos (PDFs, petições históricas) passam por uma quebra em blocos de texto limitados a 1000 caracteres, com uma sobreposição deslizante (overlap) de 200 caracteres para preservar a continuidade de sentenças em divisões de blocos.

### 2. Busca Vetorial (pgvector)
Os blocos de texto (chunks) são processados e convertidos em embeddings vetoriais de 768 dimensões. Para consultas semânticas, a aplicação calcula a distância vetorial de cosseno no PostgreSQL:

```typescript
const chunks = await prisma.$queryRaw`
  SELECT id, conteudo, (embedding <=> ${queryVector}::vector) as distancia
  FROM "DocumentChunk"
  WHERE "tenantId" = ${tenantId}
  ORDER BY distancia ASC
  LIMIT 3;
`;
```

### 3. Fila de Processamento Assíncrono
Para que o parsing de PDFs pesados e a geração de embeddings não degradem o tempo de resposta da API principal de backend, implementei um gerenciador de fila assíncrono (`aiWorker.ts` / `scheduler.ts`) rodando em concorrência controlada no servidor Render.

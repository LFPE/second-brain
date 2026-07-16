# Arquitetura RAG: Recuperação de Contexto e Busca Vetorial com LLMs

## Introdução
Embora os Grandes Modelos de Linguagem (LLMs) demonstrem grande capacidade cognitiva, eles sofrem com dois limites sérios: **alucinação** (inventar fatos plausíveis) e a **falta de dados atualizados ou privados** (dados internos de uma empresa ou base jurídica proprietária).

Para resolver isso sem o custo proibitivo e a complexidade de realizar um Fine-Tuning (ajuste fino de pesos do modelo), utilizei a arquitetura **RAG (Retrieval-Augmented Generation)**. O RAG atua como um sistema de "livro aberto": antes de enviar a pergunta para a IA, fazemos uma busca semântica em nosso banco de dados, extraímos os trechos mais relevantes e os anexamos como contexto no prompt da LLM.

Neste artigo, detalho a minha implementação do pipeline RAG, cobrindo o processamento de arquivos, geração de embeddings vetoriais, busca de similaridade por cosseno e injeção contextual.

---

## O Pipeline RAG (Arquitetura)

```text
 1. Documento (PDF/TXT) ➜ 2. Chunking (Recortes de texto) ➜ 3. Embeddings Generator
                                                                       │
 5. LLM Prompt Context    4. Busca por Similaridade (Cosseno)    Vetor Guardado no DB
```

### 1. Processamento e Fragmentação de Texto (Chunking)
Para que a busca semântica seja precisa, não podemos vetorizar documentos inteiros de centenas de páginas em um único vetor, pois a média vetorial diluiria o significado das palavras.

Implementei uma estratégia de **sliding window (janela deslizante)** para fragmentar documentos em blocos (chunks) menores de 1000 caracteres, com uma sobreposição (overlap) de 200 caracteres para garantir que frases na divisa de blocos não percam o contexto:

```typescript
function splitTextIntoChunks(text: string, chunkSize = 1000, overlap = 200): string[] {
  const chunks: string[] = [];
  let i = 0;
  while (i < text.length) {
    chunks.push(text.slice(i, i + chunkSize));
    i += chunkSize - overlap;
  }
  return chunks;
}
```

### 2. Geração e Armazenamento de Embeddings Vetoriais
Cada bloco de texto é convertido em uma matriz de números decimais (geralmente com 768 ou 1536 dimensões) que representam a coordenada semântica do texto em um espaço multidimensional. 

Para persistir esses vetores e permitir consultas de alta performance, utilizei a extensão **pgvector** integrada ao PostgreSQL e ao Prisma:

```prisma
// Declaração conceitual da tabela de vetores (Prisma)
model DocumentChunk {
  id         Int      @id @default(autoincrement())
  conteudo   String   // O texto legível do chunk
  embedding  Unsupported("vector(768)") // Vetor de 768 dimensões
  documentId Int
  tenantId   Int
}
```

---

## Busca por Similaridade de Cosseno (Cosine Similarity)

Quando o usuário faz uma pergunta à Inteligência Artificial, executamos os seguintes passos:
1.  Vetorizamos a pergunta do usuário usando o mesmo modelo de embeddings.
2.  Efetuamos uma consulta SQL de **distância de cosseno** no banco de dados para encontrar os *N* blocos de texto mais próximos semanticamente da pergunta do usuário.

```typescript
import { prisma } from '../../shared/lib/prisma';

async function buscarContextoRelevante(pergunta: string, tenantId: number, limit = 3) {
  // 1. Gera o embedding da pergunta (via API ou modelo local)
  const queryVector = await gerarEmbedding(pergunta); 

  // 2. Executa query SQL nativa para calcular a distância vetorial no PostgreSQL
  const chunks: any[] = await prisma.$queryRaw`
    SELECT id, conteudo, (embedding <=> ${queryVector}::vector) as distancia
    FROM "DocumentChunk"
    WHERE "tenantId" = ${tenantId}
    ORDER BY distancia ASC
    LIMIT ${limit};
  `;

  return chunks.map(c => c.conteudo).join('\n\n');
}
```
*(Nota: O operador `<=>` no pgvector representa a distância de cosseno. Quanto menor a distância, maior a similaridade semântica do texto).*

---

## Injeção de Contexto no Prompt da LLM

Após recuperar as informações mais relevantes do banco de dados, montamos o prompt final instruindo a LLM a responder **exclusivamente com base nos fatos fornecidos**, eliminando a ocorrência de alucinações:

```typescript
async function responderPerguntaComRAG(pergunta: string, tenantId: number) {
  const contexto = await buscarContextoRelevante(pergunta, tenantId);

  const prompt = `
Você é um assistente de inteligência jurídica especializado.
Responda à pergunta do usuário utilizando estritamente as informações presentes no CONTEXTO abaixo.
Se a resposta não puder ser encontrada no contexto, diga apenas que não tem informações suficientes nos arquivos do escritório.

CONTEXTO RECUPERADO:
${contexto}

PERGUNTA DO USUÁRIO:
${pergunta}
  `;

  const resposta = await chamarLLM(prompt);
  return resposta;
}
```

Essa arquitetura garantiu que a IA do JurisHub crie peças processuais e responda consultas respeitando com precisão o acervo histórico de documentos reais e vencedores do próprio escritório.

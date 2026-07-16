# ✍️ Engenharia de Prompts (Prompt Engineering)

Guia de melhores práticas para projetar prompts estruturados para LLMs (como Gemini, Claude, e GPT).

---

## 1. Princípios de Estruturação
*   **Role (Papel):** Sempre defina o contexto e a persona da IA (ex: *"Você é um advogado sênior especialista em direito tributário"*).
*   **Context (Contexto):** Forneça o acervo necessário para a resposta (ex: chunks obtidos do RAG).
*   **Task (Tarefa):** Descreva a ação exata e esperada de forma direta.
*   **Output Format (Formatação):** Exija o formato final de retorno de forma restrita (ex: JSON estruturado, markdown limpo).

---

## 2. Padrões Úteis (Templates)

### Prompt do Assistente RAG do JurisHub:
```text
Você é o assistente virtual jurídico do JurisHub. Sua tarefa é responder à pergunta do advogado utilizando estritamente as peças jurídicas fornecidas no contexto abaixo.

<contexto>
{contexto_recuperado}
</contexto>

Pergunta: {pergunta_usuario}

Instruções:
- Se a resposta não puder ser deduzida do contexto, diga de forma limpa que não encontrou informações no acervo do escritório.
- Não alucine nem extrapole as informações do contexto.
```

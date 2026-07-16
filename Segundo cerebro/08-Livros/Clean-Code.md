# Resumo de Livro: Clean Code (Código Limpo) 📖

*Autor: Robert C. Martin*

---

## 💡 Principais Aprendizados e Regras de Ouro

### 1. Nomes Significativos
*   Use nomes de variáveis que revelem a intenção (ex: `int tempoDecorridoEmDias` em vez de `int d`).
*   Evite desvios de nomenclatura ou termos genéricos irrelevantes.

### 2. Funções Limpas
*   **Devem ser pequenas:** Funções raramente devem ter mais de 20 linhas de código.
*   **Fazer apenas uma coisa:** Se uma função realiza mais de uma tarefa conceitual, divida-a em subfunções.
*   **Poucos argumentos:** O número ideal de argumentos para uma função é zero (niládico), seguido por um (monádico) e dois (diádico). Evite três ou mais.

### 3. Comentários Úteis
*   O melhor comentário é o código limpo que explica a si mesmo.
*   Comentários de código obsoleto desatualizados são ruídos e devem ser excluídos.

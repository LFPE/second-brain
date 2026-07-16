# Git Standards 🐙

Diretrizes e fluxos de controle de versão (Git) a serem adotados para manter o histórico de commits rastreável e limpo.

---

## 1. Convenção de Commits (Conventional Commits)

Todas as mensagens de commit devem seguir a especificação padrão, contendo o tipo da alteração e uma descrição concisa em português:

```text
<tipo>(<escopo>): <descrição>
```

### Tipos Permitidos:
*   `feat`: Nova funcionalidade ou recurso para o usuário.
*   `fix`: Resolução de um bug ou erro operacional.
*   `docs`: Alterações estritas em arquivos de documentação (README, Wiki).
*   `style`: Alterações de formatação ou estilo de código (lint, prettier) sem alteração lógica.
*   `refactor`: Refatoração de código que não altera o comportamento final da aplicação.
*   `perf`: Otimizações de desempenho e velocidade.
*   `chore`: Atualizações de scripts de build, dependências ou ferramentas de infraestrutura.

---

## 2. Boas Práticas de Histórico
*   **Commits Pequenos:** Realize commits atômicos focados em resolver apenas uma tarefa por vez.
*   **Git Amend para Correções:** Se cometeu um erro de formatação ou digitação logo após um commit, use `git commit --amend` para ajustar a última mensagem ou arquivo sem criar commits intermediários redundantes.
*   **Bypass de Logs de IA:** Para atualizações estritas de documentação em projetos públicos, evite commits excessivos no GitHub usando a técnica de *force push* após reescrever o histórico localmente.

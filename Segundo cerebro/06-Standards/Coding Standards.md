# Coding Standards 💻

Regras de codificação limpa (Clean Code) e padrões de programação a serem seguidos em todos os projetos de desenvolvimento.

---

## 1. Princípios Gerais
*   **Simplicidade (KISS):** Escreva códigos diretos. Evite abstrações precoces e complexidade desnecessária.
*   **Responsabilidade Única (SRP):** Cada função, classe ou componente deve realizar apenas uma tarefa de forma clara.
*   **Código Autodocumentado:** Escolha nomes descritivos para variáveis e funções de forma que comentários explicativos sejam a exceção, e não a regra.

---

## 2. Padrões de TypeScript & JavaScript
*   **Tipagem Estrita:** Manter `strict: true` nos arquivos de configuração do compilador (`tsconfig.json`). Evitar o uso do tipo genérico `any` a menos que seja estritamente necessário; preferir tipos genéricos parametrizados (`<T>`) ou `unknown`.
*   **Imutabilidade:** Declarar variáveis com `const` por padrão; utilizar `let` apenas em laços e reatribuições explícitas.
*   **Funções Puras:** Favorecer funções puras (sem efeitos colaterais locais) para facilitar testes unitários.

---

## 3. Padrões de Organização de Arquivos
*   Utilizar um único export padrão ou múltiplos exports nomeados de forma explícita por arquivo.
*   Centralizar exportações de módulos através de arquivos de barril (`index.ts`) para limpar importações externas.

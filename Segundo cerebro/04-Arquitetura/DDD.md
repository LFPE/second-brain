# Domain-Driven Design (DDD) 🏛️

O **Domain-Driven Design (DDD)** é uma abordagem de modelagem de software que foca no domínio de negócios como elemento central de desenvolvimento, aproximando a linguagem técnica da linguagem real usada por especialistas do negócio (Linguagem Ubíqua).

---

## 1. Conceitos Estratégicos

### Bounded Contexts (Contextos Delimitados)
Divisão lógica do sistema onde determinados modelos e termos fazem sentido. Em uma plataforma SaaS, o contexto de **Faturamento** (onde "Cliente" representa o pagador de mensalidade) é separado do contexto de **Atendimento** (onde "Cliente" representa a pessoa física atendida por processos judiciais).

### Ubiquitous Language (Linguagem Ubíqua)
Uma linguagem comum compartilhada por desenvolvedores e especialistas de negócio. Todos os nomes de tabelas, classes de domínio e rotas de código devem refletir rigorosamente os termos reais do dia a dia da operação.

---

## 2. Conceitos Táticos

*   **Entities (Entidades):** Objetos que possuem uma identidade única e contínua através do tempo (ex: `Cliente`, `Usuario`). A identidade não muda, mesmo se suas propriedades mudarem.
*   **Value Objects (Objetos de Valor):** Elementos que não possuem identidade própria e são definidos unicamente pelos seus atributos (ex: `Endereco`, `Telefone`). Dois endereços com os mesmos dados são idênticos.
*   **Aggregates (Agregados):** Grupos de objetos associados que são tratados como uma unidade única de transição de dados. O controle de alteração do agregado é governado por uma entidade principal chamada **Aggregate Root (Raiz do Agregado)**.
*   **Repositories (Repositórios):** Interfaces de acesso ao banco de dados que encapsulam a persistência e fornecem uma visão de coleção em memória de agregados.

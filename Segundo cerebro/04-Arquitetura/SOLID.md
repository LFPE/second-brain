# Princípios SOLID 📐

Os princípios **SOLID** são diretrizes de design orientadas a objetos e design de software projetadas para tornar sistemas mais legíveis, flexíveis e fáceis de manter.

---

## Significado dos Princípios

1.  **S — Single Responsibility Principle (Princípio da Responsabilidade Única):**
    *   *Definição:* Uma classe/função deve ter apenas um motivo para mudar.
    *   *Exemplo:* Não misturar regras de validação de e-mail com persistência direta no banco de dados na mesma função.
2.  **O — Open/Closed Principle (Princípio Aberto/Fechado):**
    *   *Definição:* Componentes de software devem estar abertos para extensão, mas fechados para modificação.
    *   *Exemplo:* Utilizar herança ou composição para adicionar novos comportamentos em vez de reescrever lógica interna de classes antigas.
3.  **L — Liskov Substitution Principle (Princípio da Substituição de Liskov):**
    *   *Definição:* Classes filhas devem ser capazes de substituir suas classes mães sem quebrar o funcionamento do sistema.
4.  **I — Interface Segregation Principle (Princípio da Segregação de Interfaces):**
    *   *Definição:* Muitas interfaces específicas são melhores do que uma única interface genérica complexa.
5.  **D — Dependency Inversion Principle (Princípio da Inversão de Dependência):**
    *   *Definição:* Dependa de abstrações (interfaces), não de implementações concretas (classes).
    *   *Exemplo:* O controlador da API deve receber um `UserRepositoryInterface` injetado em vez de instanciar diretamente o model concreto do Prisma.

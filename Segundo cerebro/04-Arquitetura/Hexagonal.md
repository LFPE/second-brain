# Arquitetura Hexagonal (Ports and Adapters) 🧩

A **Arquitetura Hexagonal**, proposta por Alistair Cockburn, foca em criar limites rígidos entre o núcleo lógico do sistema (Core) e o mundo externo (infraestrutura, bancos de dados, interfaces e integrações).

---

## Estrutura Básica

```text
       Adaptadores de Entrada (Request)
                │
                ▼ (Porta de Entrada)
    ┌───────────────────────┐
    │     Núcleo Lógico     │ ◄─── Casos de Uso (Core)
    └───────────────────────┘
                │
                ▼ (Porta de Saída)
       Adaptadores de Saída (DB, Storage, APIs)
```

---

## Elementos Principais

### 1. Núcleo Lógico (Core)
Contém as entidades de negócio e a lógica de casos de uso da aplicação. Ele não conhece portas de rede, servidores HTTP ou drivers de banco de dados.

### 2. Ports (Portas)
São interfaces em código (declaradas pelo Core) que definem como agentes externos podem interagir com a aplicação (Portas de Entrada) ou como a aplicação interage com serviços de infraestrutura (Portas de Saída).

### 3. Adapters (Adaptadores)
São implementações concretas que realizam a ponte entre o Core e o mundo real:
*   *Adaptador de Entrada:* O servidor Hono ou um worker que intercepta requisições HTTP e as converte em chamadas para os Casos de Uso.
*   *Adaptador de Saída:* A classe `PrismaUserRepository` ou `GoogleDriveStorage` que implementa as interfaces definidas pelo Core para salvar dados de fato.

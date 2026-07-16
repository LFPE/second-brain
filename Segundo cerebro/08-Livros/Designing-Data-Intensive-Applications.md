# Resumo de Livro: Designing Data-Intensive Applications (DDIA) 📖

*Autor: Martin Kleppmann*

---

## 💡 Principais Aprendizados e Conceitos

### 1. Três Pilares dos Sistemas de Dados
*   **Confiabilidade (Reliability):** O sistema deve continuar funcionando corretamente mesmo em caso de falhas (de hardware, software ou humanas).
*   **Escalabilidade (Scalability):** O sistema deve ter estratégias de crescimento para lidar com aumentos de carga (ex: volume de dados, tráfego de rede).
*   **Manutenibilidade (Maintainability):** O código deve ser simples, extensível e operável para que novos desenvolvedores trabalhem sem fricção.

### 2. Armazenamento e Indexação
*   **B-Trees:** O índice padrão mais comum na maioria dos bancos relacionais (como PostgreSQL). Otimizado para consultas de leitura rápida em faixas de valores e gravações previsíveis.
*   **LSM-Trees:** Índices comuns em bancos de dados NoSQL otimizados para altíssima taxa de gravação (escrevem em logs em memória antes de persistir em disco de forma sequencial).

### 3. Modelos de Transação (ACID)
*   Garante a consistência de dados em operações concorrentes pesadas através de isolamentos de transações (Read Committed, Repeatable Read, Serializable).

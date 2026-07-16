# 🧠 Lições Aprendidas & Boas Práticas — System Tracker

*   **Sempre usar Drizzle ORM para projetos baseados em Workers:** A ausência de inicializações pesadas (diferente de Prisma) mantém a performance em milissegundos.
*   **Nunca subestimar a geração de tipos do Wrangler:** Usar o Wrangler SDK sem typegen gera inconsistências em desenvolvimento.
*   **Sempre estruturar logs locais para monitoramento:** O uso de sqlite relacional facilita a filtragem de relatórios sem custos adicionais de análise de dados.

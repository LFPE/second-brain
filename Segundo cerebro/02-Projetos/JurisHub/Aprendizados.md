# 🧠 Lições Aprendidas & Melhores Práticas — JurisHub

Principais aprendizados de engenharia técnica adquiridos durante a construção do JurisHub:

*   **Sempre usar Hono para APIs Edge/Runtimes leves:** O Hono reduziu o consumo de memória e a latência de início rápido (cold start) em comparação com o Express.
*   **Nunca realizar uploads síncronos de blobs:** O processamento em memória de arquivos pesados degrada o event loop. Usar fluxos streams com Service Accounts é essencial para a saúde operacional da aplicação de servidor.
*   **Sempre memoizar subcomponentes de listas interativas:** No React, re-renderizações em cascata de elementos complexos em drag and drop (como cards de CRM) arruínam a taxa de atualização (FPS) da interface. O uso correto de `React.memo` e `useCallback` é obrigatório.
*   **Centralizar decisões arquiteturais em ADRs:** Documentar alternativas e trade-offs poupa retrabalhos e discussões técnicas futuras conforme o código evolui.

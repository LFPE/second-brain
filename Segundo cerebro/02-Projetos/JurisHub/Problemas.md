# ❌ Registro de Problemas Encontrados & Resoluções — JurisHub

Histórico de incidentes e bugs de infraestrutura documentados para evitar retrabalho de análise:

---

## Incidente: Estoiro de Limite de Timeout (504 Gateway Timeout) na Vercel

*   **Problema:** Chamadas longas de IA (RAG e embeddings) levavam mais de 10s e eram cortadas pelo proxy serverless da Vercel.
*   **Solução:** Separação do deploy. Hospedagem estática do frontend na Vercel e o servidor API dinâmico Hono configurado na Render. Injeção direta da URL do backend (`VITE_API_URL`) no navegador do cliente para contornar o gateway da Vercel.
*   **Tempo gasto:** 4 horas.

---

## Incidente: Erro de CORS na Divisão de Hospedagem

*   **Problema:** O navegador do usuário bloqueava requisições disparadas de `jurishub-app.vercel.app` para a API na Render devido a regras de mesma origem.
*   **Solução:** Configuração do middleware `cors` do Hono para verificar e autorizar de forma explícita e restrita as origens de produção e de desenvolvimento local, liberando o tráfego com credenciais.
*   **Tempo gasto:** 2 horas.

---

## Incidente: Conflito de Portas Local (`EADDRINUSE`)

*   **Problema:** A porta `3000` e a porta `5173` apareciam ocupadas, fazendo com que a API não inicializasse e o Vite fizesse o fallback para a porta `5174`.
*   **Solução:** Identificação e encerramento de tarefas fantasmas locais do Node.js penduradas na memória que foram iniciadas pelo editor em background.
*   **Tempo gasto:** 30 minutos.

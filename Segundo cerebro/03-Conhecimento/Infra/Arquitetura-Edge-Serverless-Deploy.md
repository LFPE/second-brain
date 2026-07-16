# Arquitetura de Implantação: Edge, Serverless e Integração Multicloud

## Introdução
Escolher a estratégia de deploy correta é tão importante quanto escrever código limpo. Em aplicações modernas, o modelo de hospedagem unificado (onde o mesmo servidor Linux entrega o HTML, CSS e resolve as chamadas de banco de dados) foi substituído por arquiteturas multicloud distribuídas. 

Neste artigo, detalho a arquitetura de implantação que desenhei para o JurisHub, integrando **Vercel** (para entrega de ativos estáticos de alta velocidade na borda da rede/Edge) e **Render** (para execução persistente de microsserviços em servidores Node.js), contornando limitações de timeout e mitigando problemas de CORS.

---

## Desenho da Infraestrutura

```text
 [ Navegador do Usuário ]
    │
    ├───► (Static Assets: HTML/JS/CSS) ───► [ Vercel Edge Network (Global CDN) ]
    │
    └───► (API Requests / RAG / IA) ──────► [ Render Web Service (Node/Hono) ]
                                                    │
                                                    └───► [ Supabase (Postgres) ]
```

---

## 1. Otimização de Timeout na Vercel (Bypass de Serverless Limits)

No modelo padrão de deploy na Vercel, o backend e o frontend rodam sob funções serverless. O plano gratuito da Vercel impõe um limite rígido de **10 segundos** para a execução de qualquer Serverless Function.

### O Problema
Nosso módulo de Inteligência Artificial processa o parsing de documentos jurídicos grandes, gera embeddings e chama modelos do Gemini. Essas requisições levam rotineiramente de 12 a 25 segundos para concluir, o que resultava no erro `504 Gateway Timeout` da Vercel para o usuário final.

### A Solução
Separei a hospedagem de maneira híbrida:
*   **Frontend (Vercel):** Hospedado de forma 100% estática. Sem processamento de API serverless local. O código compilado do React é distribuído globalmente via CDN, abrindo quase instantaneamente.
*   **Backend (Render):** O servidor API Hono foi provisionado como um Web Service contínuo (não serverless). Ele roda em uma instância ativa e sem limite de tempo de requisição, permitindo que processamentos longos de IA executem até o fim.
*   **Chamadas Diretas:** Configurei a variável de ambiente `VITE_API_URL` nos arquivos de produção apontando diretamente para o endpoint da Render. Com isso, o navegador do usuário faz requisições diretas de API para a Render, contornando o gateway e o limite de 10s da Vercel.

---

## 2. Configuração de Cabeçalhos CORS Dinâmicos

A separação física do frontend (`jurishub-app.vercel.app`) e do backend (`jurishub-backend.onrender.com`) gera requisições de origens diferentes, ativando as travas de CORS (Cross-Origin Resource Sharing) nos navegadores.

### Solução no Hono
Configurei um middleware de CORS no backend para interceptar requisições e liberar dinamicamente o tráfego apenas da nossa origem confiável de produção, incluindo as credenciais e métodos necessários:

```typescript
import { Hono } from 'hono';
import { cors } from 'hono/cors';

const app = new Hono();

app.use('/api/*', cors({
  origin: (origin) => {
    // Permite conexões locais e do nosso domínio de produção na Vercel
    const allowedOrigins = [
      'http://localhost:5173',
      'http://localhost:5174',
      'https://jurishub-app.vercel.app'
    ];
    return allowedOrigins.includes(origin) ? origin : 'https://jurishub-app.vercel.app';
  },
  allowMethods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  allowHeaders: ['Content-Type', 'Authorization', 'Accept'],
  exposeHeaders: ['Content-Length', 'X-Kuma-Revision'],
  maxAge: 600, // Armazena a prévia da requisição CORS por 10 minutos para melhorar a performance
  credentials: true,
}));
```

---

## 3. Automação de Publicação via CLI (vercel.json)

Para manter o roteamento estático do Vite funcionando perfeitamente em Single Page Applications (SPAs) na Vercel (onde recarregar a página `/crm` direto no navegador causaria erro 404), incluí uma regra de redirecionamento nativa:

### Arquivo `vercel.json`
```json
{
  "cleanUrls": true,
  "rewrites": [
    { "source": "/api/(.*)", "destination": "https://jurishub-backend-yvak.onrender.com/api/$1" },
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}
```

O script de publicação local no meu terminal compila a build, copia o arquivo `vercel.json` e o token de link do projeto para dentro da pasta `/dist` e dispara o deploy para produção:
```bash
npx vite build
copy vercel.json dist\vercel.json
cd dist
npx vercel --prod --yes
```
Este pipeline garantiu que o deploy ocorra em menos de 15 segundos e a aplicação permaneça online, estável e rápida para qualquer usuário no globo.

# Práticas Avançadas de Segurança em APIs Node.js e Hono

## Introdução
Segurança em APIs não é uma etapa final de desenvolvimento, mas um pilar de arquitetura que deve ser desenhado desde a primeira linha de código. Ao expor endpoints públicos na web, o sistema fica sujeito a varreduras automatizadas, ataques de força bruta, tentativas de injeção de script (XSS), injeções SQL e negação de serviço (DoS).

Este artigo reúne os padrões de segurança e middlewares que implementei em APIs estruturadas com **Hono Framework** rodando em ambiente Node.js.

---

## 🛡️ 1. Proteção Contra Cross-Site Scripting (XSS)

Ataques de XSS ocorrem quando a aplicação recebe dados de entrada do usuário e os armazena ou renderiza sem a devida sanitização, permitindo que scripts maliciosos injetados sejam executados no navegador de outros usuários.

### Solução: Middleware de Sanitização Preventiva
Criei uma camada middleware que examina recursivamente todas as strings dentro do corpo (`body`) das requisições POST/PUT, limpando caracteres e tags indesejadas antes que a requisição chegue aos controladores da rota:

```typescript
import { Context, Next } from 'hono';

// Função utilitária para limpar injeções HTML
export function sanitizeText(text: string): string {
  if (typeof text !== 'string') return text;
  return text
    .replace(/<script[^>]*>([\s\S]*?)<\/script>/gi, '') // Remove blocos de script
    .replace(/<[^>]*>?/gm, '') // Limpa qualquer tag HTML
    .trim();
}

// Middleware recursivo de payload
export const sanitizeMiddleware = async (c: Context, next: Next) => {
  if (['POST', 'PUT', 'PATCH'].includes(c.req.method)) {
    try {
      const body = await c.req.json();
      const sanitizeObject = (obj: any): any => {
        if (typeof obj === 'string') {
          return sanitizeText(obj);
        } else if (Array.isArray(obj)) {
          return obj.map(sanitizeObject);
        } else if (obj !== null && typeof obj === 'object') {
          const cleanObj: any = {};
          for (const key in obj) {
            cleanObj[key] = sanitizeObject(obj[key]);
          }
          return cleanObj;
        }
        return obj;
      };
      
      // Sobrescreve o corpo parseado com os dados limpos
      c.set('parsedBody', sanitizeObject(body));
    } catch {
      // Ignora falhas de parse de JSON vazio
    }
  }
  await next();
};
```

---

## 🚦 2. Prevenção de Abuso via Rate Limiting

Ataques de força bruta a rotas de login ou tentativas de flooding em endpoints de escrita podem travar servidores e inflar custos de banco de dados.

### Solução: Rate Limiting Baseado em IP
Para APIs operando em servidores individuais, configurei um controle de limite de taxa utilizando cache local em memória (para ambientes serverless distribuídos, recomenda-se Redis):

```typescript
import { Context, Next } from 'hono';

const rateLimitCache = new Map<string, { requests: number; resetTime: number }>();

export const rateLimitMiddleware = (limit: number, windowMs: number) => {
  return async (c: Context, next: Next) => {
    const ip = c.req.header('x-forwarded-for') || 'ip-desconhecido';
    const now = Date.now();
    
    let record = rateLimitCache.get(ip);
    
    if (!record) {
      record = { requests: 1, resetTime: now + windowMs };
      rateLimitCache.set(ip, record);
    } else {
      if (now > record.resetTime) {
        record.requests = 1;
        record.resetTime = now + windowMs;
      } else {
        record.requests++;
      }
    }
    
    if (record.requests > limit) {
      return c.json({ error: 'Muitas requisições. Tente novamente mais tarde.' }, 429);
    }
    
    await next();
  };
};
```
Esta proteção foi aplicada nas rotas sensíveis como `/api/auth/login` e `/api/auth/register`, limitando tentativas de login consecutivas.

---

## 🔒 3. Controle de Acesso Baseado em Whitelist de Emails

Para ambientes restritos onde apenas usuários autorizados pelo administrador da empresa podem acessar as contas, apliquei uma lógica de segurança rígida em nível de middleware durante a autenticação.

Em vez de permitir a validação padrão de qualquer email existente, o validador compara o input com uma lista permitida (whitelist) antes mesmo de consultar a senha criptografada (`bcrypt`). Isso evita vazamento de hashes de senhas e tentativas de injeção em e-mails desconhecidos:

```typescript
const whitelist = ['admin@jurishub.com', 'yasmimpaloma.vs18@gmail.com'];

export const whitelistLoginCheck = (email: string): boolean => {
  return whitelist.includes(email.toLowerCase().trim());
};
```
Esta verificação impede que contas criadas de forma fraudulenta em bancos de dados comprometidos consigam gerar sessões JWT legítimas na aplicação de produção.

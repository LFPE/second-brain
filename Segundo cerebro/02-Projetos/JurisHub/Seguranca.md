# 🛡️ Proteção & Segurança da Informação — JurisHub

A arquitetura do JurisHub segue o modelo de segurança **Zero Trust**, mitigando ativamente ameaças em todas as camadas do sistema.

---

## 1. Prevenção de Cross-Site Scripting (XSS)
Implementação de um middleware de sanitização recursivo (`sanitizeText`) que varre as strings dos payloads POST/PUT/PATCH de todas as rotas e remove injeções maliciosas de tags HTML e scripts antes de gravar no PostgreSQL:

```typescript
export function sanitizeText(text: string): string {
  if (typeof text !== 'string') return text;
  return text
    .replace(/<script[^>]*>([\s\S]*?)<\/script>/gi, '')
    .replace(/<[^>]*>?/gm, '')
    .trim();
}
```

---

## 2. Controle de Autenticação Whitelist
A plataforma restringe o login exclusivamente para e-mails pré-aprovados pela administração. A rota `/register` foi desativada e a autenticação no backend valida os dados de entrada contra a whitelist interna:

```typescript
const whitelist = ['admin@jurishub.com', 'yasmimpaloma.vs18@gmail.com'];
```
Isso impede o acesso à plataforma por usuários não autorizados, mesmo em caso de brechas de auto-cadastro em formulários ocultos.

---

## 3. Limitação de Taxa (Rate Limiter)
Middlewares de controle de taxa localizados nas rotas críticas limitam o tráfego repetitivo a 5 requisições por minuto por endereço de IP de origem, evitando varreduras de força bruta na chave secreta.

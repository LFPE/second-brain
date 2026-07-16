# Security Checklist 🛡️

Checklist prático de segurança da informação com base nas diretrizes do OWASP Top 10 para validação antes de realizar deploys em produção.

---

## Checklist de Segurança

### 1. Autenticação e Autorização (A01:2021)
- [ ] Os tokens JWT possuem tempo de expiração razoável (ex: 24 horas)?
- [ ] A rota `/register` de cadastro público está desabilitada ou oculta?
- [ ] A whitelist de e-mails autorizados está ativa no middleware de login?
- [ ] As senhas estão criptografadas no banco utilizando Bcrypt com fator de custo apropriado?

### 2. Integridade dos Dados e Isolamento (A08:2021)
- [ ] O isolamento multi-tenant (`tenantId`) está ativamente injetado em todas as rotas de banco de dados?
- [ ] Testou se um usuário logado consegue fazer requisições para ler dados de outro tenant alterando parâmetros na URL? (O resultado deve ser `404` ou `403`).

### 3. Validação e Sanitização de Entrada (A03:2021)
- [ ] As strings de entrada vindas de payloads POST/PUT passam pelo middleware de sanitização contra XSS?
- [ ] Consultas cruas em SQL (`$queryRaw`) utilizam parametrização segura (Prepared Statements) para impedir injeções SQL?

### 4. Proteções de Rede e Cabeçalhos
- [ ] O cabeçalho CORS no backend está configurado exclusivamente para aceitar requisições das origens autorizadas (desenvolvimento local e domínios de produção)?
- [ ] A API possui limites de taxa (Rate Limiter) ativos nas rotas de login para impedir força bruta?

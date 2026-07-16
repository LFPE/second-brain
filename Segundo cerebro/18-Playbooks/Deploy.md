# Playbook: Deploy do JurisHub (Produção) 🚀

Procedimento operacional padrão (SOP) para realizar o build e deploy em produção da interface do JurisHub utilizando a linha de comando (Vercel CLI).

---

## Pré-requisitos
*   Node.js instalado localmente na versão LTS ativa (v20+).
*   Instalação global da Vercel CLI ou execução direta via `npx vercel`.
*   Link de projeto associado corretamente (armazenado em `dist/.vercel/project.json`).

---

## Passos Operacionais

### 1. Compilação do Frontend Estático
Gere os arquivos compilados e minificados para a pasta de distribuição estática `/dist` rodando:
```bash
npm run build
```
*(Nota: Este comando executa `tsc` para verificação estática de tipos do TypeScript e compila o bundle final usando o Vite).*

### 2. Sincronização de Configurações
Para garantir que os redirecionamentos de rotas do SPA funcionem na borda da Vercel, copie o arquivo `vercel.json` para dentro do diretório `/dist`:
```bash
copy vercel.json dist\vercel.json
```

### 3. Execução do Deploy em Produção
Navegue para a pasta `/dist` ou aponte o escopo do Vercel CLI diretamente para ela, disparando o envio de forma não-interativa e silenciosa:
```bash
npx vercel --prod --yes
```

### 4. Associação de Domínio (Alias)
Associe a hash única gerada pela Vercel no passo anterior ao domínio amigável oficial do escritório (`jurishub-app.vercel.app`):
```bash
npx vercel alias set [hash_gerada].vercel.app jurishub-app.vercel.app
```
*(Nota: O alias set finaliza o redirecionamento global. Certifique-se de testar o login de produção logo após a alteração).*

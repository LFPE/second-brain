# Snippets: PowerShell Úteis 🐚

Biblioteca de comandos utilitários para PowerShell no Windows.

---

## 1. Localizar e Encerrar Processos Ocupando Portas
Encontra qual ID de processo (PID) está ocupando uma porta de rede específica (ex: 3000) e o encerra imediatamente para liberar o servidor local:

```powershell
# Encontra a porta e seu PID correspondente
Get-NetTCPConnection -LocalPort 3000, 5173 -ErrorAction SilentlyContinue | Format-Table LocalPort, State, OwningProcess

# Encerra o processo ocupando a porta (ex: PID 1234)
Stop-Process -Id 1234 -Force
```

---

## 2. Limpeza em Lote de Arquivos Mortos / Cache
Remove pastas de cache locais de desenvolvimento recursivamente de forma limpa:

```powershell
Get-ChildItem -Path . -Filter "node_modules" -Directory -Recurse | Remove-Item -Recurse -Force
Get-ChildItem -Path . -Filter ".wrangler" -Directory -Recurse | Remove-Item -Recurse -Force
```

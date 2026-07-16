# Snippets: PowerShell e Registro do Windows (Registry) 🐚

Comandos úteis para ler, criar, atualizar e excluir chaves de registro do Windows utilizando PowerShell.

## 1. Criar e Alterar Chaves de Registro (HKCU)

Escreve nas chaves do Usuário Atual (não exige privilégios de administrador/UAC):

```powershell
$RegPath = "HKCU:\Software\Classes\Directory\shell\MinhaOpcao"

# Cria a chave se não existir
if (-not (Test-Path $RegPath)) {
    New-Item -Path $RegPath -Force | Out-Null
}

# Define o texto do menu
New-ItemProperty -Path $RegPath -Name "(Default)" -Value "Executar Ação" -PropertyType String -Force | Out-Null

# Define um ícone do sistema para o menu
New-ItemProperty -Path $RegPath -Name "Icon" -Value "imageres.dll,-112" -PropertyType String -Force | Out-Null
```

---

## 2. Definir Comando da Chave de Registro

Cria a subchave `command` e define qual programa e parâmetros serão chamados:

```powershell
$RegCmdPath = "$RegPath\command"
if (-not (Test-Path $RegCmdPath)) {
    New-Item -Path $RegCmdPath -Force | Out-Null
}

# Executa um script PowerShell oculto passando a pasta selecionada (%1)
$CmdValue = "powershell.exe -NoProfile -WindowStyle Hidden -File `"C:\Script.ps1`" -TargetFolder `"%1`""
New-ItemProperty -Path $RegCmdPath -Name "(Default)" -Value $CmdValue -PropertyType String -Force | Out-Null
```

---

## 3. Excluir Chaves de Registro Recursivamente

```powershell
$RegPath = "HKCU:\Software\Classes\Directory\shell\MinhaOpcao"
if (Test-Path $RegPath) {
    Remove-Item -Path $RegPath -Recurse -Force
}
```

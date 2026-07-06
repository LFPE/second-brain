# Projeto: Organizador de Arquivos (PowerShell Utility) 📁

Utilitário leve escrito em **PowerShell** projetado para organizar automaticamente diretórios bagunçados (como a pasta de Downloads) em subpastas categorizadas com base nas extensões dos arquivos. Conta com integração direta ao menu do Windows Explorer.

## 🛠️ Arquitetura & Stack

* **Script Base**: PowerShell Script (`.ps1`).
* **Instalação**: Script auto-instalável via escrita segura no Registro do Windows (`HKEY_CURRENT_USER`).
* **Compatibilidade**: Windows 10 / Windows 11.

```mermaid
flowchart TD
    A[Pasta Bagunçada] -->|Botão Direito -> Organizar esta Pasta| B(PowerShell Script Executando Oculto)
    B -->|Mapeamento de Extensão| C{Classificar Categorias}
    C -->|Documentos / Imagens / Codigos / Instaladores...| D[Mover Arquivo]
    D -->|Se houver arquivo idêntico| E[Renomeação Incremental: nome (1).ext]
```

---

## ⚙️ Funcionalidades Principais

- **Mapeamento Amplo de Categorias**: Classifica arquivos automaticamente em Documentos, Planilhas, Apresentações, Imagens, Vídeos, Áudios, Instaladores, Compactados e Códigos.
- **Tratamento Seguro de Conflitos**: Caso o arquivo destino já exista, o script gera um sufixo numerado incremental (ex: `relatorio (1).xlsx`), impedindo qualquer perda acidental de dados por sobreposição.
- **Instalação Sem Direitos de Administrador**: A chave de registro é salva em `HKEY_CURRENT_USER\Software\Classes\Directory\shell`, evitando requisição de permissões UAC de Administrador.

---

## 📋 Comandos do Script

* **Instalação no Menu de Contexto (Botão Direito)**:
  ```powershell
  powershell.exe -ExecutionPolicy Bypass -File .\OrganizarArquivos.ps1 -Install
  ```
* **Desinstalação e Remoção do Registro**:
  ```powershell
  powershell.exe -ExecutionPolicy Bypass -File .\OrganizarArquivos.ps1 -Uninstall
  ```

# ADR 0001: Escolha do PowerShell para Automação Local de Arquivos

*   **Status:** Aprovado
*   **Data:** 2026-06-24
*   **Autor:** Felipe Pinho

---

## Contexto
Necessidade de monitorar e organizar recursivamente arquivos na pasta de Downloads de um computador de trabalho Windows de forma rápida e automatizada.

---

## Decisão
Adotei scripts em **PowerShell** em vez do Python.

---

## Motivo
*   **Integração Nativa:** O PowerShell está pré-instalado em qualquer sistema operacional Windows de trabalho corporativo, eliminando a necessidade de instalar executáveis do Python ou configurar ambientes virtuais (`venv`).
*   **Performance do Sistema:** Menor tempo de carga de processos locais do sistema Windows.

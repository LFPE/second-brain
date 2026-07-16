# ADR 0001: Escolha do Pandas para Análise de Dados de Vendas

*   **Status:** Aprovado
*   **Data:** 2026-06-24
*   **Autor:** Felipe Pinho

---

## Contexto
O projeto Sales Data Analysis precisa processar planilhas de faturamento e gerar correlações estatísticas básicas.

---

## Decisão
Decidi adotar o **Pandas** no Python para realizar o processamento.

---

## Motivo
*   **Comunidade e Ecossistema:** O Pandas possui ampla documentação e integração direta com Seaborn e Matplotlib para plotagem gráfica rápida de matrizes de correlação.
*   **Volume de Dados:** O tamanho do faturamento mensal cabe perfeitamente na memória RAM, não justificando o uso de ferramentas de concorrência ou polars/spark.

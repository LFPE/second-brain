# 🧠 Lições Aprendidas — Sales Data Analysis

*   **Sempre remover outliers antes de modelar correlações:** Valores discrepantes distorcem a inclinação da reta de regressão e geram mapas de calor imprecisos.
*   **Pandas é otimizado para operações vetorizadas:** Evitar loops explícitos (`for`) no Python sobre linhas de dataframes; preferir o uso de `.apply()` ou funções nativas vetorizadas para garantir performance.
*   **Matplotlib precisa de controle de layout estrito:** Gráficos com muitos rótulos de eixos sobrepostos quebram a visualização. Usar `.autofmt_xdate()` é essencial.

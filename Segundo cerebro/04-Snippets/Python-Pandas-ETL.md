# Snippets: Python & Pandas para ETL 🐍

Mapeamento de funções e tratamentos rápidos e reutilizáveis em Pandas para engenharia de dados.

## 1. Tratamento de Datas Multiformato

Caso a coluna de data venha com formatos mistos (ex: `YYYY-MM-DD` e `DD/MM/YYYY` na mesma coluna), utilize o utilitário abaixo:

```python
import pandas as pd

def parse_date(date_str):
    try:
        if "/" in str(date_str):
            return pd.to_datetime(date_str, format="%d/%m/%Y")
        return pd.to_datetime(date_str, format="%Y-%m-%d")
    except Exception:
        return pd.NaT

df["Data"] = df["Data_Suja"].apply(parse_date)
# Preenchimento de vazios usando o último valor conhecido
df["Data"] = df["Data"].ffill()
```

---

## 2. Limpeza Numérica com Regex

Filtra caracteres não numéricos de strings (como símbolos monetários "R$" ou unidades "m²") e converte para número decimal:

```python
import re

# R$ 450.000,00 -> 450000.00
def clean_money(text):
    if not isinstance(text, str): return 0.0
    cleaned = re.sub(r"[^\d]", "", text) # Remove tudo que não for dígito
    return float(cleaned) / 100 if cleaned else 0.0

# 85 m² ou 85m2 -> 85.0
def clean_area(text):
    if not isinstance(text, str): return 0.0
    match = re.search(r"\d+", text)
    return float(match.group()) if match else 0.0
```

---

## 3. Detecção de Outliers (Método IQR)

Retorna a máscara de booleanos para registros que representam anomalias na distribuição:

```python
q1 = df["Coluna"].quantile(0.25)
q3 = df["Coluna"].quantile(0.75)
iqr = q3 - q1
limite_superior = q3 + 1.5 * iqr

df["Is_Outlier"] = df["Coluna"] > limite_superior
```

# 📊 Análise de Dados com TMDB 5000 Movies

Este repositório contém 15 blocos de código em Python (estilo notebooks) para praticar **Ciência de Dados** usando o dataset `tmdb_5000_movies.csv`.  
Ideal para alunos iniciantes acompanharem em sala de aula com orientação passo a passo.

---

## 🧠 Pré-requisitos

- Python 3.8+
- Pandas, Matplotlib, Seaborn
- Jupyter Notebook **ou** VS Code com Jupyter Extension
- Dataset `tmdb_5000_movies.csv` na mesma pasta dos arquivos

---

## 📂 Como Usar

1. Baixe o arquivo `tmdb_5000_movies.csv` e coloque na mesma pasta.
2. Copie os blocos de código abaixo para um **notebook** ou execute em células no VS Code.
3. Os códigos são **independentes** – você pode rodar qualquer um sem ordem rígida.

---

## 01_exploracao_inicial
```python
# 01 - Exploração Inicial do Dataset

import pandas as pd

df = pd.read_csv('tmdb_5000_movies.csv')

print(df.head())
print(df.info())
print(df.describe())
print(df.isnull().sum())

df['release_date'] = pd.to_datetime(df['release_date'], errors='coerce')
print(df.dtypes)

# Aula 02.3 · Transformação e agregação

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Transformar (criar colunas, calcular métricas) e agregar (resumir por grupos) são o coração de qualquer camada analítica. No Pandas, fazer isso de forma **vetorizada** é a diferença entre segundos e horas.

Ponto de partida (base limpa da [aula 02.2](02-limpeza.md)):
```python
import pandas as pd
df = pd.read_csv("dados/vendas_livraria.csv", sep=";", decimal=",", parse_dates=["data"]).drop_duplicates()
df["uf"] = df["uf"].str.strip().str.upper()
```

## 1. Criando colunas
```python
df["receita"] = df["qtd"] * df["preco"]
```
Com `assign`, que devolve um novo DataFrame e permite encadear operações:
```python
resultado = (
    df.assign(
        receita=lambda d: d["qtd"] * d["preco"],
        mes=lambda d: d["data"].dt.to_period("M"),
    )
    .query("receita > 50")
    .sort_values("receita", ascending=False)
)
```

### Condições
```python
import numpy as np

df["faixa"] = np.where(df["preco"] >= 100, "premium", "padrão")

df["porte"] = np.select(
    [df["qtd"] >= 3, df["qtd"] == 2],
    ["grande", "médio"],
    default="pequeno",
)
```

### `map` para trocar valores
```python
regioes = {"SP": "Sudeste", "RJ": "Sudeste", "MG": "Sudeste", "BA": "Nordeste"}
df["regiao"] = df["uf"].map(regioes)
```

## 2. Vetorização vs `apply`
```python
# ❌ lento: chama uma função Python para cada linha
df["receita"] = df.apply(lambda linha: linha["qtd"] * linha["preco"], axis=1)

# ✅ rápido: uma operação sobre a coluna inteira (executada em C)
df["receita"] = df["qtd"] * df["preco"]
```
Em 1 milhão de linhas a diferença costuma ser de **100x ou mais**. Use `apply` só quando não houver operação vetorizada equivalente.

## 3. `groupby`: dividir, aplicar, combinar
```python
df.groupby("categoria")["receita"].sum()
```
```
categoria
Literatura     114.3
Programação    676.8
```
Várias métricas de uma vez, com nomes claros (*named aggregation*):
```python
df.groupby("categoria").agg(
    receita_total=("receita", "sum"),
    vendas=("id_venda", "count"),
    ticket_medio=("receita", "mean"),
).round(2)
```
```
             receita_total  vendas  ticket_medio
categoria
Literatura           114.3       3         57.15
Programação          676.8       3        225.60
```
> 🔎 Repare: Literatura tem 3 vendas, mas o ticket médio é 114,3 / **2** = 57,15. A venda com preço nulo entrou na contagem de `id_venda` e ficou de fora da média. É exatamente o tipo de detalhe que a limpeza precisa resolver antes.

## 4. Pivot e melt
`pivot_table` transforma linhas em colunas (formato "largo"):
```python
df.assign(mes=df["data"].dt.to_period("M")).pivot_table(
    index="mes", columns="categoria", values="receita", aggfunc="sum", fill_value=0
)
```
```
categoria  Literatura  Programação
mes
2024-01          59.8        379.8
2024-02          54.5        297.0
```
`melt` faz o caminho inverso (formato "longo", que é o preferido para bancos e ferramentas de BI):
```python
largo = pd.DataFrame({"loja": ["A", "B"], "jan": [100, 80], "fev": [120, 90]})
largo.melt(id_vars="loja", var_name="mes", value_name="vendas")
```
```
  loja  mes  vendas
0    A  jan     100
1    B  jan      80
2    A  fev     120
3    B  fev      90
```

## 5. "Window functions" no Pandas
O que no SQL é `OVER (...)`, no Pandas costuma ser `shift`, `cumsum`, `rolling`, `rank` e `transform`:
```python
mensal = df.groupby(df["data"].dt.to_period("M"))["receita"].sum().to_frame()
mensal["mes_anterior"] = mensal["receita"].shift(1)     # LAG
mensal["acumulado"] = mensal["receita"].cumsum()        # SUM() OVER (ORDER BY ...)
```
```
         receita  mes_anterior  acumulado
data
2024-01    439.6           NaN      439.6
2024-02    351.5         439.6      791.1
```
`transform` devolve um valor por linha, mantendo o tamanho original (como uma window com `PARTITION BY`):
```python
df["receita_da_categoria"] = df.groupby("categoria")["receita"].transform("sum")
df["participacao"] = df["receita"] / df["receita_da_categoria"]
```

## 6. Tabela de equivalência com SQL
| SQL | Pandas |
|-----|--------|
| `GROUP BY a` + `SUM(b)` | `df.groupby("a")["b"].sum()` |
| `HAVING SUM(b) > 10` | `.groupby(...).sum().query("b > 10")` |
| `LAG(b) OVER (ORDER BY d)` | `df.sort_values("d")["b"].shift(1)` |
| `SUM(b) OVER (PARTITION BY a)` | `df.groupby("a")["b"].transform("sum")` |
| `RANK() OVER (PARTITION BY a ORDER BY b DESC)` | `df.groupby("a")["b"].rank(ascending=False)` |
| `CASE WHEN ... THEN ... END` | `np.where` / `np.select` |

## ❌ Armadilhas comuns
- `apply(axis=1)` por hábito.
- `groupby` em coluna com nulos: por padrão as linhas com chave nula **somem** do resultado. Use `dropna=False` quando quiser vê-las.
- Esquecer de ordenar antes de `shift`/`cumsum`.

## ✏️ Pratique
Exercícios **4**, **5**, **6**, **8** e **9** do [módulo 02](../README.md).

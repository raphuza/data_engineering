# Aula 02.1 · Fundamentos: DataFrame, leitura e seleção

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
O Pandas é a ferramenta padrão para manipular dados tabulares em Python quando eles cabem na memória de uma máquina. É usado em ingestões, validações, transformações pequenas e médias, e para explorar dados antes de escrever um pipeline maior.

Os exemplos desta e das próximas aulas usam o arquivo [`dados/vendas_livraria.csv`](dados/vendas_livraria.csv), uma base fictícia e propositalmente suja:

```csv
id_venda;data;cliente;uf;produto;categoria;qtd;preco
1;2024-01-05;Ana;SP;Python Fluente;Programação;1;189,90
2;2024-01-05;Bruno;rj;Dom Casmurro;Literatura;2;29,90
3;2024-01-20;Carla;SP;Python Fluente;Programação;1;189,90
4;2024-02-02;Ana;SP;O Hobbit;Literatura;1;54,50
5;2024-02-15;Diego;MG ;SQL para Análise;Programação;3;99,00
5;2024-02-15;Diego;MG ;SQL para Análise;Programação;3;99,00
6;2024-02-28;Elisa;;Dom Casmurro;Literatura;1;
```
Repare: separador `;`, decimal com vírgula, UF minúscula e com espaço, uma linha duplicada e valores vazios.

## 1. Series e DataFrame
- **Series**: uma coluna (valores + índice).
- **DataFrame**: uma tabela, isto é, várias Series que compartilham o mesmo índice.

```python
import pandas as pd

precos = pd.Series([10.0, 25.5, 3.2], name="preco")
tabela = pd.DataFrame({"produto": ["caneta", "caderno", "lápis"], "preco": [3.5, 25.0, 1.2]})
```

## 2. Lendo CSV do jeito certo
```python
df = pd.read_csv(
    "dados/vendas_livraria.csv",
    sep=";",              # separador
    decimal=",",          # vírgula como separador decimal
    parse_dates=["data"], # converte a coluna para datetime
    encoding="utf-8",
)
```
Outros parâmetros muito úteis:

| Parâmetro | Para que serve |
|-----------|----------------|
| `usecols=["a", "b"]` | ler só algumas colunas (economiza memória) |
| `dtype={"cep": "string"}` | forçar tipos (CEP com zero à esquerda!) |
| `nrows=1000` | ler uma amostra |
| `na_values=["-", "N/A"]` | o que deve ser considerado nulo |
| `thousands="."` | separador de milhar |

## 3. Primeira inspeção
Sempre olhe os dados antes de transformá-los:
```python
df.shape        # (7, 8) → linhas, colunas
df.head(3)      # primeiras linhas
df.dtypes       # tipo de cada coluna
df.info()       # tipos + contagem de não nulos + memória
df.describe()   # estatísticas das colunas numéricas
```
Saída de `df.dtypes`:
```
id_venda              int64
data         datetime64[ns]
cliente              object
uf                   object
produto              object
categoria            object
qtd                   int64
preco               float64
```
> 📌 No pandas 3 as colunas de texto aparecem como `str` em vez de `object`. O comportamento nos exemplos é o mesmo.

## 4. Selecionando dados
```python
df["cliente"]                       # uma coluna → Series
df[["cliente", "produto"]]          # várias colunas → DataFrame
```

### `loc`: por rótulo e condição
```python
df.loc[df["uf"] == "SP", ["cliente", "produto"]]
```
```
  cliente         produto
0     Ana  Python Fluente
2   Carla  Python Fluente
3     Ana        O Hobbit
```

### `iloc`: por posição
```python
df.iloc[0:2, 0:3]     # linhas 0 e 1, colunas 0 a 2
```

### Filtros combinados
Use `&` (e), `|` (ou) e `~` (não), **sempre com parênteses**:
```python
df[(df["qtd"] > 1) & (df["categoria"] == "Programação")]
```
Ou, mais legível, com `query`:
```python
df.query("qtd > 1 and categoria == 'Programação'")
```

## 5. Se você vem do SQL
| SQL | Pandas |
|-----|--------|
| `SELECT a, b FROM t` | `t[["a", "b"]]` |
| `WHERE x > 1` | `t[t["x"] > 1]` ou `t.query("x > 1")` |
| `ORDER BY x DESC` | `t.sort_values("x", ascending=False)` |
| `LIMIT 10` | `t.head(10)` |
| `SELECT DISTINCT a` | `t["a"].unique()` ou `t.drop_duplicates("a")` |
| `COUNT(*)` | `len(t)` |

## ❌ Armadilhas comuns
- Usar `and`/`or` em vez de `&`/`|` em filtros: dá `ValueError: The truth value of a Series is ambiguous`.
- Encadear seleção e atribuição (`df[df.x > 1]["y"] = 0`): gera o famoso `SettingWithCopyWarning` e pode não alterar nada. Use `df.loc[df.x > 1, "y"] = 0`.
- Não conferir `dtypes` depois de ler. Um número lido como texto quebra todas as contas seguintes.

## ✏️ Pratique
Exercício **1** do [módulo 02](../README.md).

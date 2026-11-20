# Aula 02.2 · Limpeza: nulos, duplicados, tipos, textos e datas

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Em projetos reais, limpar e padronizar dados toma boa parte do tempo. Cada decisão de limpeza (remover, preencher, corrigir) muda o resultado final, por isso deve ser **explícita e documentada**.

Base de exemplo: [`dados/vendas_livraria.csv`](dados/vendas_livraria.csv) (veja a [aula 02.1](01-fundamentos.md)).

```python
import pandas as pd
df = pd.read_csv("dados/vendas_livraria.csv", sep=";", decimal=",", parse_dates=["data"])
```

## 1. Nulos
```python
df.isna().sum()
```
```
id_venda     0
data         0
cliente      0
uf           1
produto      0
categoria    0
qtd          0
preco        1
```
O que fazer depende do significado do dado:

| Estratégia | Código | Quando faz sentido |
|-----------|--------|--------------------|
| Remover linhas | `df.dropna(subset=["preco"])` | o campo é essencial e não há como recuperar |
| Preencher com valor fixo | `df.fillna({"uf": "NI"})` | categoria "não informado" |
| Preencher por regra | `df["preco"].fillna(df["preco"].median())` | só com justificativa de negócio |
| Manter nulo | — | o nulo tem significado (ex.: "ainda não entregue") |

> ⚠️ Agregações ignoram nulos: `mean()` divide pelo número de valores **não nulos**. Uma venda com preço nulo some do ticket médio sem aviso.

## 2. Duplicados
```python
df.duplicated().sum()                          # 1 linha 100% igual a outra
df = df.drop_duplicates()                      # remove duplicatas exatas
df.duplicated(subset=["id_venda"]).sum()       # duplicatas pela chave
```
Duplicata pela chave com valores diferentes em outras colunas é um sinal de problema na origem: investigue antes de escolher qual manter (`keep="first"`, `keep="last"`).

## 3. Textos com `.str`
```python
df["uf"] = df["uf"].str.strip().str.upper()
df["uf"].tolist()
# ['SP', 'RJ', 'SP', 'SP', 'MG', nan]
```
Métodos úteis: `.str.lower()`, `.str.title()`, `.str.replace()`, `.str.contains()`, `.str.split()`, `.str.zfill(8)` (CEP com zeros à esquerda), `.str.normalize("NFKD")` (para remover acentos).

### Números em formato brasileiro escritos como texto
```python
valores = pd.Series(["1.234,56", "99,90"])
valores.str.replace(".", "", regex=False).str.replace(",", ".", regex=False).astype(float)
# [1234.56, 99.9]
```

## 4. Convertendo tipos com segurança
```python
pd.to_numeric(pd.Series(["10", "x", "3,5"]), errors="coerce")
# [10.0, NaN, NaN]   ← valores inválidos viram NaN em vez de quebrar

pd.to_datetime(pd.Series(["31/12/2024", "2024-13-01"]), format="%d/%m/%Y", errors="coerce")
# [Timestamp('2024-12-31'), NaT]
```
Depois de um `errors="coerce"`, **conte quantos valores viraram nulos**: é a medida de quanto dado ruim havia.

## 5. Datas com `.dt`
```python
df["data"].dt.year
df["data"].dt.month
df["data"].dt.day_name()          # 'Friday'...
df["data"].dt.to_period("M")      # 2024-01, 2024-02 (ótimo para agrupar por mês)
(df["data"].max() - df["data"].min()).days   # diferença em dias
```

## 6. Nomes de colunas padronizados
Padronizar para `snake_case` evita dor de cabeça com SQL e com código:
```python
df.columns = (
    df.columns.str.strip()
    .str.lower()
    .str.replace(" ", "_")
)
```

## 7. Registre suas decisões
Um log simples já ajuda muito:
```python
antes = len(df)
df = df.drop_duplicates()
print(f"duplicados removidos: {antes - len(df)}")
```
Nos projetos, troque o `print` por `logging` ([aula 01.3](../../01-python/aulas/03-erros-e-logging.md)).

## ❌ Armadilhas comuns
- Remover todos os nulos com `df.dropna()` sem olhar: pode jogar fora metade da base.
- Converter CEP, CPF ou códigos para número e perder os zeros à esquerda.
- Ignorar fuso horário em datas vindas de APIs (`tz_localize`, `tz_convert`).

## ✏️ Pratique
Exercícios **2** e **3** do [módulo 02](../README.md).

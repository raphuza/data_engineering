# Aula 02.5 · Formatos de arquivo e performance

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
CSV é universal, mas é lento, grande e não guarda tipos. Em engenharia de dados o padrão é o **Parquet**, que é usado pelo Spark, pelos data lakes, pelo Athena, pelo BigQuery e pelo DuckDB. Entender formatos e uso de memória é o que permite o Pandas lidar com bases maiores.

## 1. Linha vs coluna
- **CSV (orientado a linha)**: para somar uma coluna, é preciso ler o arquivo inteiro.
- **Parquet (orientado a coluna)**: os valores de cada coluna ficam juntos e comprimidos. Ler 2 de 50 colunas é ler só essas 2.

| | CSV | Parquet |
|---|---|---|
| Legível por humanos | sim | não |
| Guarda tipos (data, int...) | não | sim |
| Compressão | não | sim (snappy, zstd) |
| Ler só algumas colunas | não | sim |
| Tamanho típico | 100% | 10–30% |

## 2. Lendo e escrevendo Parquet
```bash
pip install pyarrow
```
```python
df.to_parquet("vendas.parquet", index=False)

df = pd.read_parquet("vendas.parquet")
df = pd.read_parquet("vendas.parquet", columns=["data", "receita"])   # só o necessário
```

### Particionando
```python
df.assign(ano=df["data"].dt.year).to_parquet("vendas/", partition_cols=["ano"])
```
Gera `vendas/ano=2023/...parquet`, `vendas/ano=2024/...parquet`. Quem lê filtrando por ano pula as outras pastas. Veremos isso a fundo no Spark e no data lake.

## 3. Medindo
```python
import time
from pathlib import Path

inicio = time.perf_counter()
pd.read_csv("arquivo.csv")
print(f"{time.perf_counter() - inicio:.2f}s")

Path("arquivo.csv").stat().st_size / 1024**2      # tamanho em MB
```

## 4. Economizando memória
```python
df.memory_usage(deep=True)          # bytes por coluna
df.info(memory_usage="deep")
```
Duas técnicas simples:

**`category` para textos repetidos** (UF, status, categoria):
```python
df["uf"] = df["uf"].astype("category")
```
Em vez de guardar "SP" um milhão de vezes, guarda um código pequeno e uma tabela de valores.

**Tipos numéricos menores:**
```python
df["qtd"] = pd.to_numeric(df["qtd"], downcast="integer")     # int64 → int8, se couber
```

## 5. Arquivos maiores que a memória: chunks
```python
totais = []
for pedaco in pd.read_csv("enorme.csv", chunksize=100_000):
    totais.append(pedaco.groupby("uf")["valor"].sum())

resultado = pd.concat(totais).groupby(level=0).sum()
```
Cada pedaço é processado e descartado. É o mesmo raciocínio dos geradores ([aula 01.4](../../01-python/aulas/04-geradores.md)).

## 6. Escrevendo em banco
```python
from sqlalchemy import create_engine
engine = create_engine("postgresql+psycopg://usuario:senha@localhost:5432/loja")
df.to_sql("vendas", engine, if_exists="replace", index=False, chunksize=10_000)
```
> Veremos no módulo 03 por que, para grandes volumes, o `COPY` do Postgres é muito mais rápido que o `to_sql`.

## 7. Quando o Pandas não basta
- **DuckDB / Polars**: mesmo computador, muito mais rápidos e com menos memória.
- **Spark** (módulo 08): quando os dados não cabem em uma máquina.

## ❌ Armadilhas comuns
- Salvar resultados intermediários em CSV e perder os tipos (datas voltam como texto).
- Usar `category` em coluna com valores quase todos diferentes (piora a memória).
- Escrever o índice sem querer (`index=False`).

## ✏️ Pratique
Exercício **7** do [módulo 02](../README.md) e o projeto do módulo.

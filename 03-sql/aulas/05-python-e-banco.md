# Aula 03.5 · Python e banco de dados (e um pouco de DuckDB)

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Quase todo pipeline em Python termina (ou começa) em um banco. Fazer essa ponte com segurança (sem SQL injection, sem senha no código) e com eficiência (cargas em lote) é obrigatório.

## 1. Instalação
```bash
pip install "psycopg[binary]" sqlalchemy python-dotenv
```
- **psycopg (v3)**: driver oficial do Postgres para Python.
- **SQLAlchemy**: camada de abstração, usada pelo Pandas (`to_sql`, `read_sql`) e por muitas ferramentas.

## 2. Credenciais fora do código
Crie um arquivo `.env` (que está no `.gitignore`!):
```ini
PG_HOST=localhost
PG_PORT=5432
PG_DB=loja
PG_USER=postgres
PG_PASSWORD=troque-me
```
```python
import os
from dotenv import load_dotenv

load_dotenv()
conninfo = (
    f"host={os.environ['PG_HOST']} port={os.environ['PG_PORT']} "
    f"dbname={os.environ['PG_DB']} user={os.environ['PG_USER']} password={os.environ['PG_PASSWORD']}"
)
```

## 3. Conectando e consultando
```python
import psycopg

with psycopg.connect(conninfo) as conn:          # commit automático ao sair do bloco sem erro
    with conn.cursor() as cur:
        cur.execute("select codigo, nome from loja where cidade = %s", ("Recife",))
        for codigo, nome in cur.fetchall():
            print(codigo, nome)
```
Se acontecer uma exceção dentro do `with`, o psycopg faz **rollback** automaticamente.

### ⚠️ Nunca monte SQL concatenando strings
```python
cidade = "Recife'; drop table loja; --"

# ❌ SQL injection: o texto vira parte do comando
cur.execute(f"select * from loja where cidade = '{cidade}'")

# ✅ parâmetro: o driver envia o valor separado do comando
cur.execute("select * from loja where cidade = %s", (cidade,))
```
Parâmetros só servem para **valores**. Para nomes de tabela/coluna dinâmicos use `psycopg.sql.Identifier`.

## 4. Inserindo várias linhas
```python
lojas = [(3, "Carla", "Natal"), (4, "Diego", "Recife")]
cur.executemany(
    "insert into loja (codigo, nome, cidade) values (%s, %s, %s) on conflict (codigo) do nothing",
    lojas,
)
```
Bom para centenas ou milhares de linhas. Para volumes maiores, use `COPY`.

## 5. `COPY`: carga em massa
O `COPY` é o jeito mais rápido de colocar dados no Postgres: o banco recebe um fluxo contínuo em vez de um comando por linha.

A partir de um arquivo CSV:
```python
with open("lojas.csv", encoding="utf-8") as f:
    with cur.copy("COPY loja (codigo, nome, cidade) FROM STDIN WITH (FORMAT csv, HEADER true, DELIMITER ';')") as copy:
        while bloco := f.read(64 * 1024):
            copy.write(bloco)
```
A partir de objetos Python (ex.: um gerador):
```python
with cur.copy("COPY loja (codigo, nome, cidade) FROM STDIN") as copy:
    for linha in gerar_lojas():            # tuplas (codigo, nome, cidade)
        copy.write_row(linha)
```
> 💡 Padrão clássico de carga: `COPY` para uma tabela de **staging** → `INSERT ... SELECT ... ON CONFLICT` da staging para a tabela final ([aula 03.3](03-cargas-idempotentes.md)).

## 6. Pandas + SQLAlchemy
```python
import pandas as pd
from sqlalchemy import create_engine

engine = create_engine("postgresql+psycopg://postgres:troque-me@localhost:5432/loja")

df = pd.read_sql("select * from loja", engine)
df.to_sql("loja_copia", engine, if_exists="replace", index=False)
```
Prático para volumes pequenos e médios. Para milhões de linhas, `COPY` é muito mais rápido.

## 7. DuckDB: SQL analítico sem servidor
O [DuckDB](https://duckdb.org/) é um banco **colunar** que roda dentro do seu processo Python, como uma biblioteca. Lê CSV e Parquet direto do disco com SQL, e é excelente para análises locais:

```bash
pip install duckdb
```
```python
import duckdb

duckdb.sql("""
    select cidade, count(*) as lojas
    from read_csv('lojas.csv', delim=';', header=true)
    group by cidade
    order by cidade
""").df()           # resultado como DataFrame do Pandas
```
```
   cidade  lojas
0   Natal      1
1  Recife      1
```
Também consulta Parquet (`from 'dados/*.parquet'`) e DataFrames que estão na memória pelo nome da variável.

## ❌ Armadilhas comuns
- Senha no código ou no Git.
- Abrir uma conexão nova para cada linha inserida.
- Esquecer o commit quando não usa o `with` (os dados "somem" ao fechar a conexão).

## ✏️ Pratique
Exercícios **1** e **8** do [módulo 03](../README.md).

# Módulo 03 · Modelagem de dados e SQL avançado

⏱️ ~10h · ⚡ módulo acelerado · [⬅ Voltar ao roadmap](../README.md)

> ⚡ Este módulo parte do princípio de que você já escreve SQL (joins, `GROUP BY`, CTEs, window functions). Se ainda não, faça antes o [SQLBolt](https://sqlbolt.com/) e o [Mode SQL Tutorial](https://mode.com/sql-tutorial/) (~10h a mais). O módulo foca no que muda quando o SQL vira parte de um pipeline: modelagem para analytics, cargas incrementais, idempotência e performance. Também é onde o Python passa a conversar com o banco.

## 🎯 Objetivos
- Modelar um data warehouse dimensional com decisões justificadas (granularidade, chaves, SCD).
- Escrever cargas idempotentes e incrementais em SQL.
- Diagnosticar e otimizar consultas e tabelas grandes no PostgreSQL.
- Integrar Python e banco de forma segura e eficiente.

## 📖 Aulas
Leia cada aula antes dos exercícios indicados no final dela. As aulas explicam os conceitos com exemplos executáveis, usando dados diferentes dos exercícios.

| # | Aula |
|---|------|
| 03.1 | [Modelagem dimensional](aulas/01-modelagem-dimensional.md) |
| 03.2 | [Dimensões que mudam (SCD)](aulas/02-scd.md) |
| 03.3 | [Cargas full, incrementais e idempotentes](aulas/03-cargas-idempotentes.md) |
| 03.4 | [Otimização no PostgreSQL](aulas/04-otimizacao.md) |
| 03.5 | [Python e banco de dados (e um pouco de DuckDB)](aulas/05-python-e-banco.md) |

## 📚 Tópicos do módulo
1. **Modelagem dimensional (Kimball)**: processo de negócio → granularidade → dimensões → fatos. Fatos transacionais, *snapshot* periódico e acumulativo.
2. **Chaves**: naturais vs. substitutas (*surrogate keys*), *hash keys*.
3. **SCD tipo 1 e tipo 2**: implementação com `MERGE` / `INSERT ... ON CONFLICT`.
4. **Cargas**: full, incremental por *watermark*, *delete + insert* por partição. Idempotência.
5. **Otimização**: `EXPLAIN (ANALYZE, BUFFERS)`, índices (B-tree, BRIN, parciais, compostos), particionamento declarativo, `VACUUM`/estatísticas.
6. **OLTP vs OLAP**: por que bancos colunares (BigQuery, Redshift, Snowflake, DuckDB) mudam as regras de otimização.
7. **Python ↔ banco**: `psycopg` / SQLAlchemy, carga em lote com `COPY`, parâmetros (nunca concatenar SQL).
8. Bônus: Data Vault e *One Big Table*, para saber quando cada abordagem aparece.

## ✏️ Exercícios
> Salve as consultas em `exercicios/NN_nome.sql` e o código Python em `exercicios/NN_nome.py`.

1. Carregue as tabelas brutas do Olist no Postgres usando `COPY` a partir do Python. Compare o tempo com `to_sql` do Pandas.
2. Defina por escrito a granularidade da `fato_vendas` e justifique a escolha (1 linha = item de pedido? pedido?).
3. Crie uma `dim_data` gerada por SQL (feriados nacionais incluídos).
4. Implemente `dim_cliente` como SCD tipo 2 e simule uma mudança de cidade de um cliente.
5. Escreva uma carga incremental idempotente da fato: rodar 2 vezes não pode duplicar nada.
6. Pegue a consulta mais lenta do seu data mart, analise o `EXPLAIN ANALYZE` e reduza o tempo com índices ou reescrita. Documente antes/depois.
7. Particione a tabela de pedidos por mês e mostre o *partition pruning* no plano de execução.
8. Rode as mesmas consultas analíticas no DuckDB sobre os Parquets do módulo 02 e compare com o Postgres.

## 🧪 Projeto: Data Mart de vendas
- Esquema `dw` com `fato_vendas`, `dim_cliente` (SCD2), `dim_produto`, `dim_vendedor` e `dim_data`.
- Carga em Python + SQL, incremental e idempotente.
- Relatório de otimização (antes/depois) no README.
- Diagrama do modelo ([dbdiagram.io](https://dbdiagram.io) ou Mermaid) e documento curto de decisões de modelagem.

Este data mart será reescrito em dbt no módulo 07, então capriche nas decisões de modelagem.

## ✅ Checklist
- [ ] Exercícios 1 a 8
- [ ] Data mart com SCD2 e carga idempotente
- [ ] Relatório de otimização
- [ ] Diagrama e decisões documentadas

## 🔗 Recursos
- *The Data Warehouse Toolkit*, Ralph Kimball (caps. 1 a 5)
- [Use The Index, Luke](https://use-the-index-luke.com/pt)
- [Documentação de particionamento do PostgreSQL](https://www.postgresql.org/docs/current/ddl-partitioning.html)
- [DuckDB](https://duckdb.org/docs/)

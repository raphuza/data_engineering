# Módulo 08 · Processamento distribuído com Spark

⏱️ ~25h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Entender quando o Pandas não basta e como o Spark distribui o trabalho.
- Escrever transformações em PySpark (DataFrame API e Spark SQL).
- Otimizar jobs: particionamento, *shuffle*, formatos colunares.

## 📚 Conteúdo
1. Arquitetura: driver, executors, *lazy evaluation*, DAG, *stages* e *tasks*.
2. PySpark local (pip ou Docker) e Spark UI.
3. DataFrame API: `select`, `filter`, `withColumn`, `groupBy`, `join`.
4. Spark SQL e views temporárias.
5. Particionamento, `repartition` vs `coalesce`, *partition pruning*.
6. Joins: *broadcast* vs *shuffle*; *skew*.
7. Parquet e uma introdução a formatos de tabela (Delta Lake / Iceberg).
8. UDFs (e por que evitá-las).

## 📦 Dados
[NYC Taxi Trip Records](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page): baixe alguns meses de *Yellow Taxi* em Parquet para `data/nyc_taxi/`.

## ✏️ Exercícios
1. Leia um mês de corridas e mostre o esquema, a contagem e uma amostra.
2. Calcule receita e número de corridas por dia e por hora.
3. Junte com a tabela de zonas (*taxi zone lookup*) e encontre as rotas mais comuns.
4. Grave o resultado particionado por ano/mês e compare a leitura com e sem filtro de partição.
5. Abra a Spark UI e explique os *stages* de um job com `groupBy`.
6. Compare *broadcast join* e *shuffle join* no exercício 3.

## 🧪 Projeto: pipeline batch NYC Taxi
Job PySpark parametrizado (mês de referência) que lê o bruto, limpa (corridas inválidas, valores negativos), enriquece com zonas e grava uma camada agregada particionada. Rodar via `spark-submit` e, opcionalmente, orquestrar no Airflow.

## ✅ Checklist
- [ ] Exercícios 1 a 6
- [ ] Job parametrizado e idempotente
- [ ] README com análise de performance

## 🔗 Recursos
- [Documentação PySpark](https://spark.apache.org/docs/latest/api/python/)
- *Learning Spark, 2ª ed.* (O'Reilly, disponível gratuitamente pela Databricks)

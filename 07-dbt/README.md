# Módulo 07 · Transformação com dbt

⏱️ ~15h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Aplicar engenharia de software ao SQL: versionamento, testes, documentação.
- Organizar transformações em camadas.
- Integrar dbt ao Airflow.

## 📚 Conteúdo
1. ELT vs ETL e o papel do dbt.
2. Projeto dbt: `dbt_project.yml`, `profiles.yml`, `sources`, `models`, `ref()`.
3. Camadas: `staging` → `intermediate` → `marts`.
4. Materializações: `view`, `table`, `incremental`, `ephemeral`.
5. Testes genéricos e singulares; `dbt build`.
6. Macros e Jinja; pacotes (`dbt_utils`).
7. Seeds e snapshots (SCD tipo 2).
8. Documentação e lineage (`dbt docs`).

## ✏️ Exercícios
1. Crie um projeto `dbt-postgres` apontando para o banco com os dados brutos do Olist.
2. Declare as fontes e crie um modelo `stg_` para cada tabela.
3. Adicione testes `unique`, `not_null`, `relationships` e `accepted_values`.
4. Crie um modelo incremental de pedidos.
5. Escreva uma macro para converter centavos ou padronizar textos.
6. Gere a documentação e analise o grafo de dependências.

## 🧪 Projeto: refazer o data mart do módulo 03 com dbt
Reimplemente o esquema estrela com dbt, com testes em todos os modelos de marts, documentação de colunas e execução orquestrada pelo Airflow (`BashOperator` ou Cosmos).

## ✅ Checklist
- [ ] Exercícios 1 a 6
- [ ] `dbt build` passando sem falhas
- [ ] dbt rodando a partir do Airflow

## 🔗 Recursos
- [dbt Fundamentals](https://learn.getdbt.com/courses/dbt-fundamentals) (gratuito)
- [Best practices de estrutura de projeto](https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview)

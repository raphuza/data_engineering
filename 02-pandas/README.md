# Módulo 02 · Pandas

⏱️ ~12h · ⚡ módulo acelerado · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Ler, limpar, transformar e agregar dados tabulares com Pandas.
- Entender tipos de dados, nulos, datas e performance básica.
- Conhecer o formato Parquet e por que ele é o padrão em engenharia de dados.

## 📖 Aulas
Leia cada aula antes dos exercícios indicados no final dela. As aulas explicam os conceitos com exemplos executáveis, usando dados diferentes dos exercícios.

| # | Aula |
|---|------|
| 02.1 | [Fundamentos: DataFrame, leitura e seleção](aulas/01-fundamentos.md) |
| 02.2 | [Limpeza: nulos, duplicados, tipos, textos e datas](aulas/02-limpeza.md) |
| 02.3 | [Transformação e agregação](aulas/03-transformacao-e-agregacao.md) |
| 02.4 | [Junções: merge e concat](aulas/04-juncoes.md) |
| 02.5 | [Formatos de arquivo e performance](aulas/05-formatos-e-performance.md) |

## 📚 Tópicos do módulo
> 💡 Se você já conhece SQL, pense em cada operação do Pandas como o equivalente de uma consulta (`merge` = `JOIN`, `groupby` = `GROUP BY`, `rank`/`shift` = window functions). O foco aqui é o que o SQL não resolve tão bem: limpeza de arquivos bagunçados, tipos, memória e formatos.

1. `Series` e `DataFrame`, `read_csv` com parâmetros (`sep`, `dtype`, `parse_dates`, `usecols`).
2. Seleção: `loc`, `iloc`, filtros booleanos, `query`.
3. Limpeza: nulos, duplicados, tipos, strings (`.str`), datas (`.dt`).
4. Transformação: `assign`, `apply` (e por que evitar), operações vetorizadas, `map`.
5. Agregações: `groupby`, `agg`, `pivot_table`, `melt`.
6. Junções: `merge`, `concat`, tipos de join.
7. Escrita em Parquet, CSV e banco de dados (`to_sql`).
8. Performance: `category`, leitura em *chunks*, uso de memória.

## 📦 Dados
Baixe o [dataset Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) e extraia em `data/olist/`.

## ✏️ Exercícios
1. Carregue `olist_orders_dataset.csv` com as colunas de data já convertidas. Quantos pedidos existem por status?
2. Encontre e trate nulos e duplicados na tabela de clientes. Documente cada decisão.
3. Calcule o tempo de entrega (compra → entrega) e o atraso em relação à data estimada.
4. Junte pedidos, itens e produtos. Qual a receita por categoria de produto?
5. Faturamento mensal e crescimento mês a mês (`pct_change`).
6. Top 10 estados por ticket médio.
7. Compare tamanho em disco e tempo de leitura entre CSV e Parquet para a tabela de itens.
8. Reproduza no Pandas uma consulta SQL com window function (ex.: ranking por categoria) e compare a legibilidade.
9. Reescreva com operações vetorizadas um trecho que você fez com `apply` e meça a diferença.

## 🧪 Projeto: limpeza e camada analítica do Olist
Um script (não notebook) `projeto/pipeline.py` que:
1. Lê os CSVs brutos de `data/olist/`.
2. Aplica limpeza e padronização (tipos, nomes de colunas em `snake_case`, nulos).
3. Gera tabelas analíticas (`fato_pedidos`, `dim_clientes`, `dim_produtos`) em Parquet.
4. Registra logs de quantas linhas entraram e saíram em cada etapa.

Use um notebook apenas para exploração. O código final fica em `.py`.

## ✅ Checklist
- [ ] Exercícios 1 a 9 resolvidos e revisados
- [ ] Pipeline em `.py` gerando arquivos Parquet
- [ ] README do projeto com as decisões de limpeza

## 🔗 Recursos
- [Documentação do Pandas](https://pandas.pydata.org/docs/user_guide/index.html)
- [Modern Pandas (Tom Augspurger)](https://tomaugspurger.net/posts/modern-1-intro/)

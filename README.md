# 🛠️ Trilha de Engenharia de Dados

Trilha prática de estudos em Engenharia de Dados, do **Python/Pandas** até **pipelines de dados em produção** com as ferramentas mais usadas no mercado.

Todo o código deste repositório é escrito por mim, módulo a módulo, seguindo um roteiro de exercícios e projetos. O objetivo é aprender fazendo e deixar um portfólio público da evolução.

---

## 🗺️ Roadmap

```
 Fundamentos            Armazenamento         Pipelines              Escala e Produção
┌──────────────┐      ┌──────────────┐      ┌──────────────┐       ┌──────────────────┐
│ 00 Setup     │      │ 03 SQL       │      │ 05 Ingestão  │       │ 08 Spark         │
│ 01 Python    │ ───▶ │ 04 Docker    │ ───▶ │ 06 Airflow   │ ───▶  │ 09 Cloud / Lake  │
│ 02 Pandas    │      │              │      │ 07 dbt       │       │ 10 Streaming     │
└──────────────┘      └──────────────┘      └──────────────┘       │ 11 Qualidade/CI  │
                                                                   └────────┬─────────┘
                                                                            ▼
                                                                  ┌──────────────────┐
                                                                  │ 12 Projeto Final │
                                                                  └──────────────────┘
```

| # | Módulo | Ferramentas | Projeto prático | Carga estimada | Status |
|---|--------|-------------|-----------------|----------------|--------|
| 00 | [Setup do ambiente](00-setup/) | Git, GitHub, VS Code, venv, terminal | Repositório publicado no GitHub | 3h | ⬜ |
| 01 | [Python para dados](01-python/) ⚡ | logging, typing, geradores, pytest, empacotamento | CLI que lê, valida e resume arquivos CSV | 8h | ⬜ |
| 02 | [Pandas](02-pandas/) ⚡ | Pandas, Parquet | Limpeza e camada analítica do Olist | 12h | ⬜ |
| 03 | [Modelagem e SQL avançado](03-sql/) ⚡ | PostgreSQL, modelagem dimensional, otimização | Data mart do Olist com SCD2 e tuning | 10h | ⬜ |
| 04 | [Docker](04-docker/) | Docker, Docker Compose | Postgres + pgAdmin + script Python em containers | 10h | ⬜ |
| 05 | [Ingestão de dados](05-ingestao/) | requests, APIs REST, Pydantic | Ingestão incremental da API do Banco Central | 15h | ⬜ |
| 06 | [Orquestração](06-airflow/) | Apache Airflow | DAG diária de ingestão + carga no Postgres | 20h | ⬜ |
| 07 | [Transformação](07-dbt/) | dbt Core | Camadas staging → intermediate → marts com testes | 15h | ⬜ |
| 08 | [Processamento distribuído](08-spark/) | PySpark | Pipeline batch em Spark com dados grandes (NYC Taxi) | 25h | ⬜ |
| 09 | [Cloud e Data Lake](09-cloud/) | AWS (S3, IAM, Glue, Athena, EMR Serverless), Terraform | Data lake bronze/prata/ouro na AWS | 25h | ⬜ |
| 10 | [Streaming](10-streaming/) | Apache Kafka | Produtor e consumidor de eventos em tempo real | 15h | ⬜ |
| 11 | [Qualidade, testes e CI/CD](11-qualidade-ci/) | pytest, Great Expectations / dbt tests, GitHub Actions | CI rodando testes e lint a cada push | 10h | ⬜ |
| 12 | [Projeto final](12-projeto-final/) | Tudo acima | Pipeline ponta a ponta documentado | 30h+ | ⬜ |

**Total estimado:** ~200 horas (≈ 8 meses a 6h/semana).

⚡ = módulo acelerado: as cargas consideram quem já tem base em Python e SQL. Quem está começando deve reservar mais tempo para eles, e as aulas de cada módulo explicam os conceitos do zero.

## 📅 Cronograma (6h/semana)

| Semanas | Módulos | Marco |
|---------|---------|-------|
| 1–4 | 00 · 01 · 02 | Repositório no ar, `csv-profiler` e pipeline Pandas do Olist |
| 5–8 | 03 · 04 | Data mart modelado e rodando em Docker |
| 9–10 | 05 | Coletor incremental da API do Banco Central |
| 11–13 | 06 | Pipeline orquestrado no Airflow |
| 14–16 | 07 | Data mart reescrito em dbt com testes |
| 17–20 | 08 | Job PySpark com o NYC Taxi |
| 21–24 | 09 | Data lake na AWS com Terraform |
| 25–28 | 10 · 11 | Streaming com Kafka e CI no GitHub Actions |
| 29–33 | 12 | Projeto final publicado |

Se uma semana render mais, sigo em frente; se travar, o cronograma é guia e não prazo.

## ☁️ Por que AWS

- **Mercado**: é a nuvem com maior participação mundial e a que mais aparece em vagas de engenharia de dados no Brasil e no exterior.
- **Ecossistema de dados completo**: S3 é o padrão de fato para data lakes, e Glue, Athena, EMR, Redshift e Kinesis cobrem todo o ciclo.
- **Transferível**: os conceitos (object storage, IAM, catálogo, compute serverless) se aplicam a GCP e Azure. No módulo 09 há um comparativo com GCP/BigQuery, que é forte em empresas de dados e startups.
- **Certificação** recomendada ao fim do módulo 09: *AWS Certified Data Engineer – Associate*.

Legenda de status: ⬜ não iniciado · 🟨 em andamento · ✅ concluído

---

## 🎯 Como esta trilha funciona

1. **Cada módulo tem um `README.md`** com objetivos, exercícios e o projeto prático, e uma pasta **`aulas/`** com explicações e exemplos de código para quem está começando.
2. **Os exercícios ficam em `exercicios/`** e o projeto em `projeto/`. Eu escrevo todo o código.
3. **Ciclo de estudo por módulo:**
   1. Ler as aulas do módulo e rodar os exemplos.
   2. Resolver os exercícios (sem copiar soluções prontas).
   3. Pedir revisão do código e refatorar.
   4. Construir o projeto do módulo.
   5. Marcar o checklist e atualizar o status no roadmap.
4. **Um commit por exercício**, com mensagens claras (ex.: `feat(01-python): exercício 03 - leitura de CSV`).

## 📦 Datasets usados

Os mesmos dados acompanham vários módulos, para que cada ferramenta nova seja aplicada a um problema que já conheço:

- **[Olist – E-commerce brasileiro](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)**: pedidos, clientes, produtos, pagamentos e avaliações (Pandas, SQL, dbt).
- **[API SGS do Banco Central](https://dadosabertos.bcb.gov.br/)**: séries temporais públicas como dólar, Selic e IPCA (ingestão e Airflow).
- **[NYC Taxi Trip Records](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)**: milhões de corridas em Parquet (Spark e data lake).

> Os dados brutos **não são versionados** (veja `.gitignore`). Cada módulo explica como baixá-los para a pasta `data/`.

## 🧱 Estrutura do repositório

```
data_engineering/
├── README.md              ← este roadmap
├── PROGRESSO.md           ← diário de estudos
├── .gitignore
├── data/                  ← dados locais (não versionados)
├── 00-setup/
├── 01-python/
│   ├── README.md          ← objetivos, exercícios, projeto
│   ├── aulas/             ← explicações e exemplos
│   ├── exercicios/
│   └── projeto/
├── 02-pandas/
├── ...
└── 12-projeto-final/
```

## 🧑‍💻 Sobre mim

<!-- Escreva aqui uma breve apresentação: quem você é, por que está estudando engenharia de dados e como falar com você (LinkedIn). -->

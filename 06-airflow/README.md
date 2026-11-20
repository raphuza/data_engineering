# Módulo 06 · Orquestração com Apache Airflow

⏱️ ~20h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Entender por que orquestrar (agendamento, dependências, retries, monitoramento).
- Escrever DAGs com boas práticas.
- Orquestrar o coletor do módulo 05.

## 📚 Conteúdo
1. Arquitetura: scheduler, webserver, executor, metadatabase.
2. Airflow via Docker Compose (imagem oficial) ou Astro CLI.
3. DAGs, tasks, operators, dependências. TaskFlow API (`@dag`, `@task`).
4. `logical_date`, intervalos, `catchup` e *backfill*.
5. Connections, Variables e segredos.
6. XComs (e por que não trafegar dados grandes por eles).
7. Sensores, *retries*, SLAs e alertas.
8. Idempotência de tasks e tasks atômicas.

## ✏️ Exercícios
1. Suba o Airflow localmente e rode uma DAG "hello world".
2. Crie uma DAG com 3 tasks em sequência e uma em paralelo.
3. Use `logical_date` para extrair dados de um dia específico e faça *backfill* de uma semana.
4. Configure uma Connection para o Postgres e use-a em uma task.
5. Faça uma task falhar e observe *retries* e logs.

## 🧪 Projeto: DAG de indicadores econômicos
DAG diária que extrai as séries do Banco Central, valida, carrega no Postgres e gera uma tabela agregada mensal. Deve suportar *backfill* sem duplicar dados.

## ✅ Checklist
- [ ] Exercícios 1 a 5
- [ ] DAG idempotente com *backfill* funcionando
- [ ] README com print da DAG e instruções para subir o ambiente

## 🔗 Recursos
- [Documentação do Airflow](https://airflow.apache.org/docs/)
- [Astronomer Academy](https://academy.astronomer.io/) (gratuito)

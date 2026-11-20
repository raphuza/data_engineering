# Módulo 04 · Docker

⏱️ ~10h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Entender imagens, containers, volumes e redes.
- Empacotar um script Python em uma imagem.
- Subir uma stack de serviços com Docker Compose (base para Airflow, Kafka e Spark).

## 📚 Conteúdo
1. Instalação do Docker Desktop com WSL2.
2. `docker run`, `ps`, `logs`, `exec`, `stop`, `rm`.
3. `Dockerfile`: `FROM`, `COPY`, `RUN`, `CMD`, cache de camadas, `.dockerignore`.
4. Volumes (persistência) e redes (containers conversando entre si).
5. Variáveis de ambiente e arquivo `.env`.
6. `docker compose`: serviços, dependências, healthchecks.

## ✏️ Exercícios
1. Rode um container `postgres` e conecte nele pelo DBeaver.
2. Faça os dados do Postgres sobreviverem à remoção do container (volume).
3. Escreva um `Dockerfile` para o `csv-profiler` do módulo 01 e execute-o passando um arquivo por volume.
4. Reduza o tamanho dessa imagem (imagem `slim`, `.dockerignore`, cache do pip).
5. Escreva um `docker-compose.yml` com Postgres + pgAdmin.

## 🧪 Projeto: pipeline Olist containerizado
`docker compose up` deve:
1. Subir o Postgres.
2. Rodar um container Python que carrega os dados do Olist (módulos 02/03) no banco.
3. Subir o pgAdmin para inspecionar o resultado.

Sem nenhuma instalação local além do Docker.

## ✅ Checklist
- [ ] Exercícios 1 a 5
- [ ] Stack sobe com um único comando
- [ ] Credenciais fora do código (`.env`, não versionado)

## 🔗 Recursos
- [Docker – Get started](https://docs.docker.com/get-started/)
- [Data Engineering Zoomcamp – Módulo 1](https://github.com/DataTalksClub/data-engineering-zoomcamp)

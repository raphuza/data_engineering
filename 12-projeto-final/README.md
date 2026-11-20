# Módulo 12 · Projeto final

⏱️ ~30h+ · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivo
Construir um pipeline de dados **ponta a ponta**, do zero, sobre um tema escolhido por mim, aplicando o que aprendi na trilha. É a peça principal do portfólio.

## 📋 Requisitos mínimos
- **Fonte real**: uma API pública ou dataset relevante (ex.: dados abertos do governo, ANP, INMET, B3, Transparência).
- **Ingestão** incremental e idempotente.
- **Armazenamento** em camadas (bronze/prata/ouro) local ou na nuvem.
- **Transformação** com dbt e/ou Spark.
- **Orquestração** com Airflow.
- **Qualidade**: testes de código e de dados.
- **Infra reproduzível**: Docker Compose e/ou Terraform.
- **CI** no GitHub Actions.
- **Consumo**: um dashboard (Metabase, Superset ou Streamlit) respondendo perguntas de negócio.
- **Documentação**: diagrama de arquitetura, decisões técnicas, como rodar, custos.

## 🗂️ Etapas
1. Proposta: problema, perguntas de negócio, fontes (1 página).
2. Desenho da arquitetura (diagrama).
3. Ingestão → transformação → orquestração → qualidade → consumo.
4. Documentação e apresentação (README + vídeo curto ou post no LinkedIn).

## ✅ Checklist
- [ ] Proposta aprovada na revisão
- [ ] Pipeline rodando de ponta a ponta
- [ ] README com arquitetura e instruções
- [ ] Publicado e divulgado

# Módulo 05 · Ingestão de dados

⏱️ ~15h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Consumir APIs REST de forma robusta (paginação, retries, limites).
- Entender carga completa vs. incremental e idempotência.
- Validar dados na entrada.

## 📚 Conteúdo
1. HTTP, JSON e a biblioteca `requests` (ou `httpx`).
2. Paginação, *rate limit*, *timeouts* e *retries* com *backoff*.
3. Carga **full** vs **incremental** (marca d'água / *watermark*) e **idempotência**.
4. Validação de esquema com Pydantic.
5. Armazenamento em camadas: `raw` (como veio) → `processed`.
6. Configuração por variáveis de ambiente.

## 📦 Fonte
[API SGS do Banco Central](https://dadosabertos.bcb.gov.br/), por exemplo a série 1 (dólar comercial), 432 (Selic) e 433 (IPCA). Formato: `https://api.bcb.gov.br/dados/serie/bcdata.sgs.{codigo}/dados?formato=json&dataInicial=dd/mm/aaaa&dataFinal=dd/mm/aaaa`.

## ✏️ Exercícios
1. Busque a série do dólar do último mês e salve o JSON bruto com a data da extração no nome.
2. Adicione *timeout* e *retry* com *backoff* exponencial.
3. Modele o registro com Pydantic e rejeite (com log) registros inválidos.
4. Rode a extração duas vezes e garanta que o resultado no banco seja o mesmo (idempotência via `UPSERT`).
5. Implemente carga incremental: buscar apenas datas após o último registro salvo.

## 🧪 Projeto: coletor de indicadores econômicos
Pacote Python que, para uma lista configurável de séries:
- extrai incrementalmente da API;
- salva o bruto em `data/raw/`;
- valida, transforma e grava no Postgres (do módulo 04);
- tem testes (com *mock* da API) e roda em Docker.

## ✅ Checklist
- [ ] Exercícios 1 a 5
- [ ] Coletor idempotente e incremental
- [ ] Testes com mock

## 🔗 Recursos
- [Documentação do requests](https://requests.readthedocs.io/)
- [Pydantic](https://docs.pydantic.dev/)

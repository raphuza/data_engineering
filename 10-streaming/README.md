# Módulo 10 · Streaming com Kafka

⏱️ ~15h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Entender quando usar processamento em tempo real em vez de batch.
- Produzir e consumir eventos com Kafka.
- Conhecer o processamento de streams com Spark Structured Streaming.

## 📚 Conteúdo
1. Batch vs streaming; latência vs custo.
2. Kafka: tópicos, partições, *offsets*, *consumer groups*, retenção.
3. Kafka via Docker Compose (modo KRaft) e uma UI (Redpanda Console ou Kafka UI).
4. Produtores e consumidores em Python (`confluent-kafka`).
5. Serialização (JSON, Avro) e *schema registry*.
6. Semântica de entrega: *at-most-once*, *at-least-once*, *exactly-once*.
7. Spark Structured Streaming: janelas, *watermark*.

## ✏️ Exercícios
1. Suba o Kafka e crie um tópico com 3 partições.
2. Escreva um produtor que gera eventos simulados de pedidos.
3. Escreva um consumidor que grava os eventos no Postgres.
4. Rode dois consumidores no mesmo grupo e observe a divisão das partições.
5. Calcule a receita por janela de 1 minuto com Structured Streaming.

## 🧪 Projeto: pedidos em tempo real
Simulador de pedidos (baseado no Olist) → Kafka → consumidor que agrega métricas em tempo real e grava no Postgres, com um painel simples (Streamlit ou Metabase).

## ✅ Checklist
- [ ] Exercícios 1 a 5
- [ ] Pipeline de streaming ponta a ponta

## 🔗 Recursos
- [Confluent Developer – Kafka 101](https://developer.confluent.io/courses/apache-kafka/events/)

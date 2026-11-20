# Módulo 09 · Cloud e Data Lake

⏱️ ~25h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Conhecer os serviços de dados essenciais da AWS (escolhida por ser a mais pedida em vagas no Brasil e no exterior, veja o [roadmap](../README.md)).
- Construir um data lake em camadas (bronze, prata, ouro).
- Provisionar infraestrutura como código e controlar custos.

## 📚 Conteúdo
1. Conta, *free tier*, **alertas de orçamento** (primeira coisa a configurar!).
2. IAM: usuários, papéis, políticas, menor privilégio.
3. S3: buckets, prefixos, classes de armazenamento, `boto3`.
4. Arquitetura *medallion*: bronze → prata → ouro.
5. Glue Data Catalog + Athena para consultar o lake com SQL.
6. Processamento na nuvem: Glue Jobs ou EMR Serverless rodando o job Spark do módulo 08.
7. Introdução a Terraform: provider, resources, state.
8. Comparativo: GCP (GCS + BigQuery), Azure, Databricks, Snowflake. O que muda e o que se mantém.

## ✏️ Exercícios
1. Crie um alerta de orçamento de US$ 5.
2. Crie um usuário IAM com acesso apenas a um bucket.
3. Envie arquivos ao S3 com `boto3` e liste-os por prefixo.
4. Crie uma tabela no Glue Catalog e consulte com Athena.
5. Provisione o bucket e as permissões com Terraform e destrua tudo com `terraform destroy`.

## 🧪 Projeto: data lake na nuvem
Mover o pipeline do NYC Taxi ou do Banco Central para a nuvem: bruto no bronze (S3), Spark/Pandas gerando prata e ouro, tabelas catalogadas e consultáveis no Athena. Infra em Terraform.

## ✅ Checklist
- [ ] Alerta de orçamento ativo
- [ ] Exercícios 1 a 5
- [ ] Lake em camadas consultável via SQL
- [ ] Infra versionada em Terraform

## 🔗 Recursos
- [AWS Skill Builder](https://skillbuilder.aws/) (cursos gratuitos)
- Certificação-alvo: [AWS Certified Data Engineer – Associate](https://aws.amazon.com/certification/certified-data-engineer-associate/)
- [Terraform – Get started AWS](https://developer.hashicorp.com/terraform/tutorials/aws-get-started)

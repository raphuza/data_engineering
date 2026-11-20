# Módulo 11 · Qualidade, testes e CI/CD

⏱️ ~10h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Garantir qualidade de código e de dados de forma automática.
- Configurar integração contínua no GitHub Actions.

## 📚 Conteúdo
1. Testes de código: unitários e de integração com `pytest`, *fixtures*, *mocks*.
2. Qualidade de código: `ruff` (lint e formatação), `mypy`, `pre-commit`.
3. Qualidade de dados: testes do dbt, Great Expectations ou Soda, contratos de dados.
4. Observabilidade: métricas de pipeline (linhas, duração, frescor), alertas.
5. GitHub Actions: workflows, jobs, *matrix*, segredos.
6. Gerenciamento de dependências moderno com `uv` ou Poetry.

## ✏️ Exercícios
1. Configure `ruff` e `pre-commit` no repositório.
2. Crie um workflow do GitHub Actions que roda `ruff` e `pytest` a cada push.
3. Adicione um teste de integração do coletor (módulo 05) usando Postgres em container no CI.
4. Escreva 5 expectativas de qualidade para a tabela de pedidos.
5. Faça o CI rodar `dbt build` em um banco temporário.

## 🧪 Projeto
Aplicar CI/CD e testes de dados aos projetos anteriores, com *badge* de status no README principal.

## ✅ Checklist
- [ ] Lint e testes automáticos em todo push
- [ ] Testes de dados nas tabelas principais
- [ ] Badge de CI no README

## 🔗 Recursos
- [Documentação do GitHub Actions](https://docs.github.com/pt/actions)
- [Great Expectations](https://docs.greatexpectations.io/)

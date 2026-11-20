# Módulo 01 · Python para dados

⏱️ ~8h · ⚡ módulo acelerado · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Dominar as estruturas de Python que aparecem todo dia em pipelines.
- Ler e escrever arquivos (CSV, JSON) sem bibliotecas externas.
- Escrever código organizado em funções e módulos, com tipagem, logging e testes.

> ⚡ **Já programa em Python?** Os exercícios 1 a 3 são um aquecimento rápido (uma sessão). **Está começando?** Leia as aulas com calma e reserve mais tempo para o módulo; um bom complemento é o [tutorial oficial](https://docs.python.org/pt-br/3/tutorial/). O foco está nos itens marcados com ⭐, que são os que mais aparecem em código de pipeline.

## 📖 Aulas
Leia cada aula antes dos exercícios indicados no final dela. As aulas explicam os conceitos com exemplos executáveis, usando dados diferentes dos exercícios.

| # | Aula |
|---|------|
| 01.1 | [Estruturas de dados, funções e decorators](aulas/01-estruturas-e-funcoes.md) |
| 01.2 | [Arquivos: pathlib, CSV e JSON](aulas/02-arquivos.md) |
| 01.3 | [Tratamento de erros e logging](aulas/03-erros-e-logging.md) |
| 01.4 | [Iteradores e geradores](aulas/04-geradores.md) |
| 01.5 | [Type hints e dataclasses](aulas/05-tipagem-e-dataclasses.md) |
| 01.6 | [Módulos, pacotes, CLI e pyproject.toml](aulas/06-modulos-pacotes-cli.md) |
| 01.7 | [Testes com pytest](aulas/07-testes-pytest.md) |

## 📚 Tópicos do módulo
1. Revisão rápida: estruturas de dados, compreensões, `*args`/`**kwargs`.
2. Funções como parâmetro, `functools`, decorators ⭐
3. Leitura e escrita de arquivos: `open`, `with`, módulos `csv`, `json`, `pathlib`.
4. Tratamento de erros: `try/except/finally`, exceções customizadas.
5. Módulos e pacotes: `import`, `if __name__ == "__main__"`, estrutura `src/`.
6. Type hints e `dataclasses` ⭐
7. Geradores e `yield` (processar arquivos grandes linha a linha) ⭐
8. `logging` em vez de `print` ⭐
9. Testes com `pytest` ⭐
10. Linha de comando com `argparse`.
11. Empacotamento: `pyproject.toml`, instalação em modo editável, `uv` ⭐

## ✏️ Exercícios
> Cada exercício em um arquivo `exercicios/exNN_nome.py`. Escreva primeiro, depois peça revisão.

1. **Dicionários**: dada uma lista de vendas `[{"produto": ..., "valor": ...}]`, calcule o total por produto.
2. **Arquivos CSV**: leia um CSV com o módulo `csv` e retorne a lista de linhas como dicionários.
3. **JSON**: converta o resultado do exercício 2 para um arquivo JSON formatado.
4. **Erros**: crie uma função que converte texto em número e registra (log) as linhas inválidas sem interromper o processamento.
5. **Geradores**: leia um arquivo de 1 milhão de linhas sem carregá-lo inteiro na memória e conte as linhas por categoria.
6. **Dataclasses**: modele um `Pedido` com tipagem e um método que calcula o valor total.
7. **pathlib**: liste todos os `.csv` de uma pasta (incluindo subpastas) com tamanho e data de modificação.
8. **Testes**: escreva testes `pytest` para os exercícios 1, 4 e 6.
9. **Decorators**: crie um decorator `@medir_tempo` que registra no log a duração de qualquer função, e outro `@retry(n)` que tenta de novo em caso de exceção.

## 🧪 Projeto: `csv-profiler`
Uma ferramenta de linha de comando que recebe um CSV e gera um relatório:
- número de linhas e colunas;
- para cada coluna: tipo inferido, % de nulos, valores distintos, mínimo/máximo (numéricas);
- saída em JSON e um resumo legível no terminal.

Requisitos: sem Pandas, instalável via `pyproject.toml` (só biblioteca padrão), código em pacote `src/`, logging, testes com `pytest`, `README.md` explicando como usar.

## ✅ Checklist
- [ ] Exercícios 1 a 9 resolvidos e revisados
- [ ] Projeto `csv-profiler` funcionando com testes
- [ ] README do projeto escrito

## 🔗 Recursos
- [Tutorial oficial do Python (pt-br)](https://docs.python.org/pt-br/3/tutorial/)
- [Real Python](https://realpython.com/)
- [Documentação do pytest](https://docs.pytest.org/)

# Aula 00.2 · Git e GitHub

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Em times de dados, todo código de pipeline, SQL de transformação, DAG e configuração de infraestrutura vive no Git. Pull Requests são o lugar onde o código é revisado antes de ir para produção. Dominar o fluxo básico é obrigatório.

## 1. Conceitos
- **Repositório**: pasta cujo histórico é controlado pelo Git (a pasta oculta `.git` guarda esse histórico).
- **Commit**: uma "foto" das mudanças, com autor, data e mensagem.
- **Branch**: uma linha de desenvolvimento independente. A principal costuma se chamar `main`.
- **Remote**: a cópia do repositório em um servidor (ex.: GitHub), normalmente chamada `origin`.
- **Pull Request (PR)**: pedido para juntar (*merge*) uma branch na outra, com espaço para revisão.

Os três "lugares" onde um arquivo pode estar:

```
 diretório de trabalho  ──git add──▶  staging (índice)  ──git commit──▶  histórico local  ──git push──▶  GitHub
```

## 2. Configuração inicial (uma vez por máquina)
```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu-email@exemplo.com"
git config --global init.defaultBranch main
```

## 3. O ciclo do dia a dia
```bash
git status                    # o que mudou? (use o tempo todo)
git add arquivo.py            # prepara um arquivo
git add .                     # prepara tudo (respeitando o .gitignore)
git commit -m "feat: adiciona leitura de CSV"
git push                      # envia para o GitHub
git log --oneline             # histórico resumido
```

Exemplo de saída do `git status`:
```
On branch main
Changes not staged for commit:
        modified:   README.md
Untracked files:
        01-python/exercicios/ex01.py
```
"Untracked" significa que o Git ainda não conhece o arquivo: ele só entra no histórico depois de um `git add`.

## 4. O `.gitignore`
Lista de padrões que o Git deve ignorar. Exemplo:
```gitignore
.venv/          # ambiente virtual
__pycache__/    # cache do Python
.env            # senhas e chaves: NUNCA versionar
data/*          # dados brutos
!data/.gitkeep  # ...exceto este arquivo, para a pasta existir no repo
```
> ⚠️ Se um arquivo já foi commitado, adicioná-lo ao `.gitignore` não o remove do histórico. Use `git rm --cached arquivo` e faça um novo commit.

## 5. Conectando ao GitHub
1. Crie o repositório vazio no GitHub (sem README, para não gerar conflito).
2. Conecte e envie:
```bash
git remote add origin https://github.com/SEU_USUARIO/data_engineering.git
git push -u origin main     # o -u faz os próximos pushes não precisarem de argumentos
```

## 6. Branches e Pull Requests
```bash
git switch -c minha-feature     # cria e entra na branch
# ... edita arquivos, add, commit ...
git push -u origin minha-feature
```
No GitHub aparece o botão **Compare & pull request**. Descreva o que mudou, revise o diff e faça o **merge**. Depois, atualize sua `main` local:
```bash
git switch main
git pull
```

## 7. Boas mensagens de commit
Um padrão muito usado é o [Conventional Commits](https://www.conventionalcommits.org/pt-br/):

| Prefixo | Uso | Exemplo |
|---------|-----|---------|
| `feat` | nova funcionalidade | `feat(01-python): exercício 02 - leitura de CSV` |
| `fix` | correção | `fix(02-pandas): trata datas nulas` |
| `docs` | documentação | `docs: atualiza roadmap` |
| `refactor` | melhoria sem mudar comportamento | `refactor: extrai função de validação` |
| `test` | testes | `test: adiciona testes do profiler` |

## ❌ Armadilhas comuns
- **Commitar senhas** (`.env`, chaves da AWS). Mesmo apagando depois, ficam no histórico. Na dúvida, rode `git status` antes de cada commit.
- **Commits gigantes** com "várias coisas". Prefira commits pequenos e com uma intenção clara.
- **Trabalhar direto na `main`** em projetos de time. Use branches e PRs.

## ✏️ Pratique
Faça os **exercícios 2 a 5** do [módulo 00](../README.md).

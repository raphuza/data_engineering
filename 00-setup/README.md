# Módulo 00 · Setup do ambiente

⏱️ ~3h · [⬅ Voltar ao roadmap](../README.md)

## 🎯 Objetivos
- Ter um ambiente de desenvolvimento Python profissional no Windows.
- Saber o fluxo básico do Git: `init`, `add`, `commit`, `push`, `branch`.
- Publicar este repositório no GitHub.

## 📖 Aulas
Leia cada aula antes dos exercícios indicados no final dela. As aulas explicam os conceitos com exemplos executáveis, usando dados diferentes dos exercícios.

| # | Aula |
|---|------|
| 00.1 | [Terminal, Python e ambientes virtuais](aulas/01-terminal-python-venv.md) |
| 00.2 | [Git e GitHub](aulas/02-git-e-github.md) |

## 📚 Tópicos do módulo
1. **Terminal**: PowerShell ou Git Bash. Navegar (`cd`, `ls`/`dir`), criar pastas e arquivos.
2. **Python 3.12+** instalado pelo site oficial (marcar *Add to PATH*). Verificar com `python --version`.
3. **Ambientes virtuais**: `python -m venv .venv` e ativação (`.venv\Scripts\activate`). Por que nunca instalar pacotes no Python global.
4. **VS Code** com extensões: Python, Pylance, Ruff, Jupyter, GitLens.
5. **Git e GitHub**: configurar `user.name`/`user.email`, criar repositório remoto, SSH ou HTTPS.
6. **WSL2** (opcional agora, necessário a partir do módulo 04 para o Docker).

## ✏️ Exercícios
1. Crie e ative um ambiente virtual na raiz do repositório. Instale `pandas` e gere um `requirements.txt` com `pip freeze`.
2. Faça o primeiro commit com a estrutura atual da trilha.
3. Crie o repositório `data_engineering` no GitHub e faça o `push`.
4. Crie uma branch `modulo-01`, faça uma alteração no `PROGRESSO.md`, abra um Pull Request no GitHub para você mesmo e faça o merge.
5. Escreva no `README.md` principal a seção "Sobre mim".

## ✅ Checklist
- [ ] Python e venv funcionando
- [ ] VS Code configurado
- [ ] Repositório publicado no GitHub
- [ ] Fiz meu primeiro Pull Request

## 🔗 Recursos
- [Pro Git (em português)](https://git-scm.com/book/pt-br/v2)
- [Documentação do venv](https://docs.python.org/pt-br/3/library/venv.html)

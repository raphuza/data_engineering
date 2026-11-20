# Aula 00.1 · Terminal, Python e ambientes virtuais

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Engenharia de dados é feita, na maior parte do tempo, longe de interfaces gráficas: em servidores, containers e pipelines de CI. Saber se virar no terminal e isolar as dependências de cada projeto é o que separa um script que "funciona na minha máquina" de um código que roda em qualquer lugar.

## 1. O terminal
No Windows você pode usar o **PowerShell** (já vem instalado) ou o **Git Bash** (vem com o Git e aceita comandos no estilo Linux). Os comandos mais usados:

| Ação | PowerShell | Git Bash / Linux |
|------|-----------|------------------|
| Onde estou? | `pwd` | `pwd` |
| Listar arquivos | `ls` ou `dir` | `ls -la` |
| Entrar em uma pasta | `cd C:\data_engineering` | `cd /c/data_engineering` |
| Voltar uma pasta | `cd ..` | `cd ..` |
| Criar pasta | `mkdir dados` | `mkdir dados` |
| Criar arquivo vazio | `New-Item teste.txt` | `touch teste.txt` |
| Ver conteúdo | `cat teste.txt` | `cat teste.txt` |

> 💡 Use a tecla **Tab** para autocompletar nomes de arquivos e pastas, e as **setas ↑ ↓** para repetir comandos anteriores.

## 2. Instalando o Python
1. Baixe a versão estável mais recente em [python.org](https://www.python.org/downloads/).
2. Na instalação, marque **"Add python.exe to PATH"**.
3. Abra um terminal novo e confira:

```powershell
python --version
# Python 3.13.7
pip --version
```

## 3. Ambientes virtuais (venv)
Cada projeto precisa de bibliotecas em versões específicas. Se você instala tudo no Python "global", um projeto que precisa do pandas 1.5 e outro que precisa do pandas 2.2 entram em conflito. O **ambiente virtual** é uma pasta com uma cópia isolada do Python e das bibliotecas daquele projeto.

```powershell
# cria o ambiente na pasta .venv (o ponto no começo é convenção)
python -m venv .venv

# ativa (PowerShell)
.venv\Scripts\Activate.ps1
# ativa (Git Bash)
source .venv/Scripts/activate

# o prompt muda e mostra (.venv) no início
(.venv) PS C:\data_engineering>
```

> ⚠️ Se o PowerShell reclamar de "execution policy", rode uma vez:
> `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`

Com o ambiente ativo, tudo que você instalar fica só nele:

```powershell
pip install requests
pip list                 # mostra o que está instalado neste ambiente
deactivate               # sai do ambiente
```

## 4. Registrando dependências
Para que outra pessoa (ou um servidor) recrie o mesmo ambiente, registramos as dependências em um arquivo:

```powershell
pip freeze > requirements.txt     # grava as versões exatas instaladas
pip install -r requirements.txt   # recria em outra máquina
```

Exemplo de `requirements.txt`:
```
certifi==2024.8.30
requests==2.32.3
urllib3==2.2.3
```

> 📌 Mais à frente (módulo 01) veremos o `pyproject.toml` e o `uv`, a forma moderna de gerenciar dependências. O `requirements.txt` continua muito comum e é o ponto de partida.

## 5. VS Code
Extensões recomendadas: **Python**, **Pylance**, **Ruff**, **Jupyter** e **GitLens**.
Depois de criar o venv, use `Ctrl+Shift+P → Python: Select Interpreter` e escolha o Python de dentro de `.venv`. Assim o editor entende as bibliotecas que você instalou.

## ❌ Armadilhas comuns
- **Versionar o venv no Git.** A pasta `.venv` tem milhares de arquivos e é específica da sua máquina. Ela fica no `.gitignore`; o que vai para o Git é o `requirements.txt`.
- **Esquecer de ativar o ambiente** e instalar pacotes no Python global. Confira se o `(.venv)` aparece no prompt.
- **Dar ao venv o mesmo nome de uma pasta do projeto**, o que confunde caminhos e imports. Prefira `.venv`.

## ✏️ Pratique
Faça o **exercício 1** do [módulo 00](../README.md).

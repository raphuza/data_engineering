# Aula 01.2 · Arquivos: pathlib, CSV e JSON

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Grande parte das ingestões começa com arquivos: exportações em CSV, respostas JSON salvas em disco, arquivos que chegam em uma pasta todo dia. Ler e escrever arquivos corretamente (encoding, separador, caminhos) evita muitos bugs silenciosos.

## 1. Caminhos com `pathlib`
Esqueça concatenar strings com `"\\"`. O `pathlib` funciona igual no Windows, Linux e Mac.

```python
from pathlib import Path

raiz = Path("data")
arquivo = raiz / "vendas" / "2024-01.csv"   # o operador / monta o caminho

arquivo.name        # '2024-01.csv'
arquivo.stem        # '2024-01'
arquivo.suffix      # '.csv'
arquivo.parent      # Path('data/vendas')
arquivo.exists()    # True / False

raiz.mkdir(parents=True, exist_ok=True)     # cria a pasta (e as de cima) se não existir

for p in Path(".").glob("*.md"):            # arquivos .md desta pasta
    print(p, p.stat().st_size, "bytes")
```
`glob("*.md")` procura só na pasta atual. Para incluir subpastas, existe `rglob`.

## 2. Abrindo arquivos com `with`
O `with` garante que o arquivo seja fechado mesmo se der erro no meio.

```python
with open("notas.txt", "w", encoding="utf-8") as f:
    f.write("primeira linha\n")
    f.write("segunda linha\n")

with open("notas.txt", encoding="utf-8") as f:
    for linha in f:                 # lê uma linha por vez, sem carregar tudo
        print(linha.rstrip("\n"))
```

| Modo | Significado |
|------|-------------|
| `"r"` | leitura (padrão) |
| `"w"` | escrita (apaga o conteúdo anterior!) |
| `"a"` | acrescenta ao final |

> ⚠️ **Sempre informe `encoding="utf-8"`.** No Windows o padrão pode ser `cp1252`, e acentos viram `Ã©`. Arquivos exportados do Excel às vezes vêm em `latin-1` ou `utf-8-sig` (com BOM).

## 3. CSV com o módulo `csv`
Considere o arquivo `funcionarios.csv`:
```csv
nome;departamento;salario
Ana;Dados;8500
Bruno;Vendas;4200
```

Lendo como listas:
```python
import csv

with open("funcionarios.csv", encoding="utf-8", newline="") as f:
    leitor = csv.reader(f, delimiter=";")
    cabecalho = next(leitor)       # ['nome', 'departamento', 'salario']
    for linha in leitor:
        print(linha)               # ['Ana', 'Dados', '8500']
```

Com `DictReader`, cada linha vira um `dict` usando o cabeçalho como chaves:
```python
with open("funcionarios.csv", encoding="utf-8", newline="") as f:
    for registro in csv.DictReader(f, delimiter=";"):
        print(registro["nome"], registro["salario"])
```
> ⚠️ O módulo `csv` **sempre devolve texto**. `"8500"` é uma string; converter para número é responsabilidade sua.

Escrevendo:
```python
linhas = [{"nome": "Carla", "departamento": "RH", "salario": 5100}]

with open("saida.csv", "w", encoding="utf-8", newline="") as f:
    escritor = csv.DictWriter(f, fieldnames=["nome", "departamento", "salario"])
    escritor.writeheader()
    escritor.writerows(linhas)
```
O `newline=""` evita linhas em branco extras no Windows.

## 4. JSON
JSON mapeia quase direto para estruturas Python: objeto ↔ `dict`, array ↔ `list`, `null` ↔ `None`.

```python
import json

config = {"fonte": "api", "series": [1, 432], "ativo": True, "obs": None}

texto = json.dumps(config)                     # dict → string
json.loads(texto)                              # string → dict

with open("config.json", "w", encoding="utf-8") as f:
    json.dump(config, f, ensure_ascii=False, indent=2)

with open("config.json", encoding="utf-8") as f:
    dados = json.load(f)
```
`ensure_ascii=False` mantém acentos legíveis; `indent=2` deixa o arquivo formatado.

### JSON Lines
Em engenharia de dados é comum o formato **JSONL**: um objeto JSON por linha. Permite ler arquivos enormes linha a linha.
```python
with open("eventos.jsonl", encoding="utf-8") as f:
    for linha in f:
        evento = json.loads(linha)
```

## ❌ Armadilhas comuns
- Separador errado (`,` vs `;`). CSVs brasileiros costumam usar `;` porque a vírgula é separador decimal.
- Ler arquivo grande com `f.read()` ou `f.readlines()` e estourar a memória.
- Datas e decimais em formato brasileiro (`31/12/2024`, `1.234,56`) tratados como texto sem conversão.

## ✏️ Pratique
Exercícios **2**, **3** e **7** do [módulo 01](../README.md).

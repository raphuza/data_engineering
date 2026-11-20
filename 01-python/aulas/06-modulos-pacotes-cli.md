# Aula 01.6 · Módulos, pacotes, CLI e pyproject.toml

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Notebook é ótimo para explorar, mas pipeline de verdade é código organizado em módulos, que pode ser importado, testado, instalado e executado por linha de comando (pelo Airflow, por um container, pelo CI).

## 1. Módulos e imports
Todo arquivo `.py` é um módulo.
```
projeto/
├── conversores.py
└── main.py
```
```python
# conversores.py
def celsius_para_fahrenheit(c: float) -> float:
    return c * 9 / 5 + 32
```
```python
# main.py
from conversores import celsius_para_fahrenheit
print(celsius_para_fahrenheit(25))   # 77.0
```

## 2. `if __name__ == "__main__":`
Quando um arquivo é **executado diretamente**, `__name__` vale `"__main__"`. Quando é **importado**, vale o nome do módulo. Isso permite que o mesmo arquivo seja biblioteca e script:

```python
# conversores.py
def celsius_para_fahrenheit(c: float) -> float:
    return c * 9 / 5 + 32

if __name__ == "__main__":
    # só roda com: python conversores.py
    print(celsius_para_fahrenheit(100))
```
Sem esse `if`, o `print` rodaria também em cada `import`.

## 3. Pacotes e o layout `src/`
Um **pacote** é uma pasta com módulos. O layout recomendado:
```
meu-projeto/
├── pyproject.toml
├── README.md
├── src/
│   └── clima/
│       ├── __init__.py
│       ├── conversores.py
│       └── cli.py
└── tests/
    └── test_conversores.py
```
```python
from clima.conversores import celsius_para_fahrenheit
```
O `src/` obriga você a instalar o pacote para usá-lo, o que garante que os testes rodem contra o código "como ele seria instalado".

## 4. `pyproject.toml`
Arquivo padrão que descreve o projeto: nome, versão, dependências e comandos.
```toml
[project]
name = "clima"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = []

[project.optional-dependencies]
dev = ["pytest", "ruff"]

[project.scripts]
clima = "clima.cli:main"      # cria o comando `clima` no terminal

[build-system]
requires = ["setuptools>=68"]
build-backend = "setuptools.build_meta"
```
Instale em **modo editável** (mudanças no código valem sem reinstalar):
```bash
pip install -e ".[dev]"
```

### `uv`: a alternativa moderna
O [uv](https://docs.astral.sh/uv/) substitui `pip` + `venv` e é muito mais rápido:
```bash
uv init clima          # cria projeto com pyproject.toml
uv add requests        # adiciona dependência
uv add --dev pytest
uv run pytest          # roda dentro do ambiente, criando se precisar
```

## 5. Linha de comando com `argparse`
```python
# src/clima/cli.py
import argparse
from clima.conversores import celsius_para_fahrenheit

def main() -> None:
    parser = argparse.ArgumentParser(description="Converte temperaturas.")
    parser.add_argument("valor", type=float, help="temperatura em Celsius")
    parser.add_argument("--casas", type=int, default=1, help="casas decimais")
    parser.add_argument("-v", "--verbose", action="store_true")
    args = parser.parse_args()

    resultado = celsius_para_fahrenheit(args.valor)
    if args.verbose:
        print(f"{args.valor} °C =", end=" ")
    print(f"{resultado:.{args.casas}f} °F")

if __name__ == "__main__":
    main()
```
```bash
clima 36.5 --casas 2 -v
# 36.5 °C = 97.70 °F
clima --help              # ajuda gerada automaticamente
```

## ❌ Armadilhas comuns
- Dar a um arquivo o nome de uma biblioteca (`csv.py`, `pandas.py`): o seu arquivo "sequestra" o import.
- Imports circulares (A importa B, que importa A). Normalmente indicam que uma parte deve virar um terceiro módulo.
- Código solto no nível do módulo (conexões, leitura de arquivos) que roda em todo `import`.

## ✏️ Pratique
Aplicado no **projeto `csv-profiler`** do [módulo 01](../README.md).

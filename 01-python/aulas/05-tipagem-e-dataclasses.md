# Aula 01.5 · Type hints e dataclasses

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Em pipelines, o "formato" dos dados é tudo. Type hints documentam o que cada função espera e devolve, e permitem que o editor e ferramentas como o `mypy` encontrem erros antes de rodar. `dataclasses` dão uma forma enxuta de representar registros com estrutura definida.

## 1. Type hints
```python
def calcular_media(valores: list[float]) -> float:
    return sum(valores) / len(valores)

def buscar_usuario(user_id: int) -> dict[str, str] | None:
    ...
```
| Escrita | Significa |
|---------|-----------|
| `int`, `str`, `float`, `bool` | tipos básicos |
| `list[int]` | lista de inteiros |
| `dict[str, float]` | dict com chave texto e valor float |
| `tuple[int, str]` | tupla com 2 elementos |
| `str \| None` | texto ou `None` |
| `Iterator[str]` | algo que produz textos (ex.: um gerador) |

> 💡 O Python **não obriga** os tipos em tempo de execução. Eles servem para leitura, autocompletar e checagem estática (`pip install mypy` e `mypy arquivo.py`).

```python
from collections.abc import Iterator

def ler_linhas(caminho: str) -> Iterator[str]:
    with open(caminho, encoding="utf-8") as f:
        yield from f
```

## 2. Dataclasses
Sem dataclass, uma classe simples exige bastante código repetitivo:
```python
class ProdutoManual:
    def __init__(self, sku, nome, preco):
        self.sku = sku
        self.nome = nome
        self.preco = preco
```
Com dataclass:
```python
from dataclasses import dataclass, field

@dataclass
class Produto:
    sku: str
    nome: str
    preco: float
    tags: list[str] = field(default_factory=list)   # padrão mutável do jeito certo

    def preco_com_desconto(self, percentual: float) -> float:
        return round(self.preco * (1 - percentual / 100), 2)

p = Produto("A1", "Caneca", 39.9)
print(p)                          # Produto(sku='A1', nome='Caneca', preco=39.9, tags=[])
p.preco_com_desconto(10)          # 35.91
p == Produto("A1", "Caneca", 39.9)   # True: compara pelos valores
```
Você ganha `__init__`, `__repr__` e `__eq__` de graça.

### Validação no `__post_init__`
```python
@dataclass
class Temperatura:
    cidade: str
    celsius: float

    def __post_init__(self):
        if self.celsius < -90 or self.celsius > 60:
            raise ValueError(f"temperatura improvável: {self.celsius}")
```

### Imutável e com `frozen=True`
```python
@dataclass(frozen=True)
class Coordenada:
    lat: float
    lon: float
```
Objetos imutáveis podem ser usados como chave de `dict` e em `set`.

### Composição
Um registro pode conter outros:
```python
@dataclass
class Endereco:
    cidade: str
    uf: str

@dataclass
class Loja:
    nome: str
    endereco: Endereco

Loja("Centro", Endereco("Recife", "PE"))
```

## 3. Dataclass ou Pydantic?
- **dataclass**: biblioteca padrão, leve, sem validação automática de tipos.
- **Pydantic** (módulo 05): valida e converte tipos automaticamente (`"10"` → `10`), ideal para dados que vêm de fora (APIs, arquivos).

## ❌ Armadilhas comuns
- `tags: list[str] = []` dá erro em dataclass. Use `field(default_factory=list)`.
- Achar que o type hint converte o valor: `Produto("A1", "x", "39.9")` aceita a string sem reclamar.

## ✏️ Pratique
Exercício **6** do [módulo 01](../README.md).

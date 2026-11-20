# Aula 01.1 · Estruturas de dados, funções e decorators

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Antes de qualquer biblioteca, dados em Python são listas e dicionários: uma resposta JSON de API vira um `dict`, um CSV lido linha a linha vira uma lista de `dict`. Escrever transformações claras com essas estruturas é a base de todo o resto.

## 1. As quatro estruturas principais

```python
cidades = ["SP", "RJ", "SP", "BH"]          # list: ordenada, mutável, aceita repetidos
coordenada = (-23.55, -46.63)               # tuple: ordenada, imutável
estados = {"SP", "RJ", "BH"}                # set: sem ordem, sem repetidos
cliente = {"id": 1, "nome": "Ana", "uf": "SP"}  # dict: chave → valor
```

| Preciso de... | Use |
|---|---|
| uma sequência que vai mudar | `list` |
| um registro fixo (ex.: chave composta) | `tuple` |
| remover duplicados / testar pertencimento rápido | `set` |
| acessar valores por nome / agrupar | `dict` |

Testar se algo está em um `set` é muito mais rápido que em uma `list` grande:
```python
ids_validos = set(range(1_000_000))
42 in ids_validos   # instantâneo, não percorre tudo
```

## 2. Dicionários na prática
```python
produto = {"sku": "A1", "preco": 10.0}

produto["preco"]                 # 10.0
produto.get("estoque")           # None (não quebra se a chave não existir)
produto.get("estoque", 0)        # 0 (valor padrão)

for chave, valor in produto.items():
    print(chave, valor)
```

### Agrupando com `dict`
Um padrão muito comum é acumular valores por chave. Veja como **contar palavras** de um texto:

```python
texto = "dados geram dados que geram valor"
contagem = {}
for palavra in texto.split():
    contagem[palavra] = contagem.get(palavra, 0) + 1

print(contagem)
# {'dados': 2, 'geram': 2, 'que': 1, 'valor': 1}
```

A biblioteca padrão tem atalhos para isso:
```python
from collections import Counter, defaultdict

Counter(texto.split()).most_common(2)    # [('dados', 2), ('geram', 2)]

por_letra = defaultdict(list)            # cria a lista automaticamente
for palavra in texto.split():
    por_letra[palavra[0]].append(palavra)
# {'d': ['dados', 'dados'], 'g': ['geram', 'geram'], 'q': ['que'], 'v': ['valor']}
```

## 3. Compreensões (comprehensions)
Forma curta e legível de criar listas, dicts e sets a partir de outra coleção.

```python
precos = [10.0, 25.5, 3.2, 99.9]

com_imposto = [p * 1.1 for p in precos]                 # transformar
caros = [p for p in precos if p > 20]                   # filtrar
indice = {i: p for i, p in enumerate(precos)}           # dict
```
> 💡 Se a compreensão ficou com mais de uma condição e uma transformação complexa, use um `for` normal. Legibilidade vem primeiro.

## 4. Funções
```python
def aplicar_desconto(preco: float, percentual: float = 10) -> float:
    """Retorna o preço com desconto. percentual vai de 0 a 100."""
    return preco * (1 - percentual / 100)

aplicar_desconto(200)                  # 180.0
aplicar_desconto(200, percentual=25)   # 150.0  (argumento nomeado: mais legível)
```

### `*args` e `**kwargs`
```python
def montar_url(base, *partes, **params):
    url = "/".join([base, *partes])
    if params:
        url += "?" + "&".join(f"{k}={v}" for k, v in params.items())
    return url

montar_url("https://api.exemplo.com", "v1", "pedidos", pagina=2, limite=100)
# 'https://api.exemplo.com/v1/pedidos?pagina=2&limite=100'
```

### Funções como parâmetro
Em Python, funções são objetos: dá para passar uma função para outra. Isso permite escrever transformações genéricas.

```python
def padronizar(valores, regra):
    return [regra(v) for v in valores]

nomes = ["  ana ", "BRUNO", "Carla  "]
padronizar(nomes, str.strip)                    # ['ana', 'BRUNO', 'Carla']
padronizar(nomes, lambda n: n.strip().title())  # ['Ana', 'Bruno', 'Carla']
```

## 5. Decorators
Um **decorator** é uma função que recebe outra função e devolve uma versão "embrulhada" dela, adicionando comportamento sem mudar o código original. Em pipelines são usados para log, medição, cache, validação e novas tentativas. O Airflow (`@task`, `@dag`) e o pytest (`@pytest.fixture`) usam decorators o tempo todo.

Exemplo: um decorator que **avisa quando uma função é chamada e com quais argumentos**.

```python
import functools

def anunciar(func):
    @functools.wraps(func)          # preserva nome e docstring da função original
    def wrapper(*args, **kwargs):
        print(f"→ chamando {func.__name__} com args={args} kwargs={kwargs}")
        resultado = func(*args, **kwargs)
        print(f"← {func.__name__} retornou {resultado!r}")
        return resultado
    return wrapper

@anunciar
def somar(a, b):
    return a + b

somar(2, 3)
# → chamando somar com args=(2, 3) kwargs={}
# ← somar retornou 5
```

Escrever `@anunciar` acima da função é o mesmo que `somar = anunciar(somar)`.

### Decorators com parâmetros
Quando o decorator precisa de configuração (ex.: `@repetir(3)`), adicionamos mais uma camada:

```python
def prefixar(texto):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            return f"{texto} {func(*args, **kwargs)}"
        return wrapper
    return decorator

@prefixar("[ETL]")
def status():
    return "carga concluída"

status()   # '[ETL] carga concluída'
```

## ❌ Armadilhas comuns
- **Argumento padrão mutável**: `def f(itens=[])` reaproveita a mesma lista em todas as chamadas. Use `itens=None` e crie a lista dentro.
- **Modificar uma lista enquanto percorre**: crie uma nova lista com compreensão.
- **Esquecer o `functools.wraps`**: logs e ferramentas passam a mostrar `wrapper` no lugar do nome real.

## ✏️ Pratique
Exercícios **1** e **9** do [módulo 01](../README.md).

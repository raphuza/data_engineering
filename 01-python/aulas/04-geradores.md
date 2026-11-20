# Aula 01.4 · Iteradores e geradores

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Um arquivo de 10 GB não cabe na memória do seu notebook. Geradores permitem **processar dados em fluxo**, um item de cada vez, com uso de memória constante. É a mesma ideia de streaming que veremos depois no Spark e no Kafka.

## 1. Lista vs gerador
```python
import sys

lista = [x * 2 for x in range(1_000_000)]     # cria tudo agora
gerador = (x * 2 for x in range(1_000_000))   # cria sob demanda (note os parênteses)

sys.getsizeof(lista)     # ~8 MB
sys.getsizeof(gerador)   # ~200 bytes
```
O gerador não guarda os valores, ele sabe **como calcular o próximo**.

## 2. Funções geradoras com `yield`
```python
def contagem_regressiva(n):
    while n > 0:
        yield n        # entrega um valor e "pausa" aqui
        n -= 1

for i in contagem_regressiva(3):
    print(i)
# 3, 2, 1
```
Cada `yield` devolve um valor e congela o estado da função até o próximo pedido.

## 3. Pipelines de geradores
O poder aparece quando encadeamos etapas. Cada uma processa um item e passa adiante, como uma esteira:

```python
def ler_linhas(caminho):
    with open(caminho, encoding="utf-8") as f:
        for linha in f:
            yield linha.rstrip("\n")

def pular_vazias(linhas):
    for linha in linhas:
        if linha.strip():
            yield linha

def para_float(linhas):
    for linha in linhas:
        yield float(linha)

# monta a esteira: nada é lido ainda
valores = para_float(pular_vazias(ler_linhas("temperaturas.txt")))

# só agora os dados fluem, uma linha por vez
total = sum(valores)
```
Não importa se o arquivo tem 100 linhas ou 100 milhões: a memória usada é praticamente a mesma.

## 4. Processando em lotes (*batches*)
Muitas vezes queremos agrupar itens, por exemplo para inserir no banco de 1.000 em 1.000:

```python
from itertools import islice

def em_lotes(iteravel, tamanho):
    it = iter(iteravel)
    while lote := list(islice(it, tamanho)):
        yield lote

for lote in em_lotes(range(10), 4):
    print(lote)
# [0, 1, 2, 3]
# [4, 5, 6, 7]
# [8, 9]
```
> 📌 A partir do Python 3.12 existe `itertools.batched(iteravel, n)`, que faz o mesmo.

## 5. Ferramentas úteis do `itertools`
```python
from itertools import islice, chain

list(islice(range(100), 5))           # primeiros 5: [0, 1, 2, 3, 4]
list(chain([1, 2], [3], [4, 5]))      # junta iteráveis: [1, 2, 3, 4, 5]
```

## 6. Gerando um arquivo grande para testes
Para praticar, crie seus próprios arquivos grandes:
```python
import random

with open("grande.txt", "w", encoding="utf-8") as f:
    for _ in range(1_000_000):
        f.write(f"{random.uniform(-10, 40):.2f}\n")
```

## ❌ Armadilhas comuns
- **Gerador só pode ser percorrido uma vez.** Depois de consumido, fica vazio.
- Chamar `list(gerador)` "para ver o conteúdo" e anular a economia de memória. Para espiar, use `islice`.
- Esquecer que nada executa até alguém consumir o gerador (avaliação preguiçosa, *lazy*). O Spark funciona do mesmo jeito.

## ✏️ Pratique
Exercício **5** do [módulo 01](../README.md).

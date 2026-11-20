# Aula 02.4 · Junções: merge e concat

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Dados úteis raramente estão em uma tabela só. Pedidos precisam de clientes, itens precisam de produtos. Junções erradas são uma das maiores fontes de números inflados ou de linhas que desaparecem.

```python
import pandas as pd
vendas = pd.read_csv("dados/vendas_livraria.csv", sep=";", decimal=",", parse_dates=["data"]).drop_duplicates()

clientes = pd.DataFrame({
    "cliente":  ["Ana", "Bruno", "Carla", "Fabio"],
    "segmento": ["ouro", "prata", "ouro", "bronze"],
})
```

## 1. `merge`
```python
vendas.merge(clientes, on="cliente", how="left", indicator=True)[["id_venda", "cliente", "segmento", "_merge"]]
```
```
   id_venda cliente segmento     _merge
0         1     Ana     ouro       both
1         2   Bruno    prata       both
2         3   Carla     ouro       both
3         4     Ana     ouro       both
4         5   Diego      NaN  left_only
5         6   Elisa      NaN  left_only
```
`indicator=True` cria a coluna `_merge`, que mostra de onde veio cada linha. Ótimo para auditar junções.

| `how=` | SQL | Mantém |
|--------|-----|--------|
| `"inner"` (padrão) | `INNER JOIN` | só o que casa dos dois lados |
| `"left"` | `LEFT JOIN` | tudo da esquerda |
| `"right"` | `RIGHT JOIN` | tudo da direita |
| `"outer"` | `FULL OUTER JOIN` | tudo dos dois lados |
| `"cross"` | `CROSS JOIN` | todas as combinações |

Colunas com nomes diferentes:
```python
pedidos.merge(clientes, left_on="id_cliente", right_on="customer_id")
```

## 2. Anti-join: "quem não tem..."
Pandas não tem `anti join` direto, mas o `indicator` resolve:
```python
todos = clientes.merge(vendas[["cliente", "id_venda"]], on="cliente", how="left", indicator=True)
todos.loc[todos["_merge"] == "left_only", ["cliente", "segmento"]]
```
```
  cliente segmento
4   Fabio   bronze        ← cliente cadastrado que nunca comprou
```

## 3. Protegendo-se de duplicação com `validate`
Se a tabela da direita tiver chaves repetidas, o `merge` multiplica linhas sem avisar. Declare o que você espera:
```python
vendas.merge(clientes, on="cliente", how="left", validate="many_to_one")
```
Se `clientes` tivesse "Ana" duas vezes, o Pandas levantaria `MergeError` em vez de duplicar a receita da Ana. Opções: `"one_to_one"`, `"one_to_many"`, `"many_to_one"`, `"many_to_many"`.

> 💡 Hábito saudável: compare `len()` antes e depois de cada `merge`. Em um `left` com `many_to_one`, o número de linhas não pode mudar.

## 4. `concat`: empilhar tabelas
```python
jan = pd.DataFrame({"id": [1, 2], "valor": [10, 20]})
fev = pd.DataFrame({"id": [3], "valor": [30]})

pd.concat([jan, fev], ignore_index=True)       # equivale a UNION ALL
```
Muito usado para juntar vários arquivos de uma pasta:
```python
from pathlib import Path
partes = [pd.read_csv(p) for p in Path("data/mensal").glob("*.csv")]
tudo = pd.concat(partes, ignore_index=True)
```

## ❌ Armadilhas comuns
- Juntar por colunas com tipos diferentes (`int` de um lado, `str` do outro): nada casa e o resultado vem cheio de nulos.
- Chaves com espaços ou maiúsculas diferentes. Padronize antes ([aula 02.2](02-limpeza.md)).
- Nomes de colunas repetidos geram sufixos `_x` e `_y`. Use `suffixes=("_pedido", "_cliente")`.

## ✏️ Pratique
Exercício **4** do [módulo 02](../README.md) e o projeto do módulo.

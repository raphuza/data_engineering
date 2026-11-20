# Aula 01.3 · Tratamento de erros e logging

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Dados reais são sujos: campos vazios, datas inválidas, APIs fora do ar. Um pipeline bom **não quebra por uma linha ruim** e **conta o que aconteceu**. Para isso usamos exceções (controlar o erro) e logging (registrar o que houve).

## 1. `try / except / else / finally`
```python
def dividir(a, b):
    try:
        resultado = a / b
    except ZeroDivisionError:
        print("divisão por zero")
        return None
    else:
        print("deu certo")          # roda só se não houve exceção
        return resultado
    finally:
        print("sempre executa")     # bom para liberar recursos
```

Capture **exceções específicas**, nunca tudo de uma vez:
```python
# ❌ ruim: esconde qualquer bug, até erro de digitação
try:
    processar()
except Exception:
    pass

# ✅ bom: trata só o que você espera e sabe resolver
try:
    valor = int(texto)
except ValueError:
    valor = None
```

## 2. Exemplo: validar datas sem parar o processamento
```python
from datetime import datetime

datas_brutas = ["2024-01-15", "15/01/2024", "", "2024-02-30", "2024-03-01"]

validas, invalidas = [], []
for bruto in datas_brutas:
    try:
        validas.append(datetime.strptime(bruto, "%Y-%m-%d").date())
    except ValueError:
        invalidas.append(bruto)

print(len(validas), "válidas")      # 2 válidas
print(invalidas)                    # ['15/01/2024', '', '2024-02-30']
```
O ponto-chave: **o erro de um registro não derruba o lote**, e os inválidos ficam guardados para análise.

## 3. Exceções customizadas
Criar suas próprias exceções deixa claro *que tipo* de problema aconteceu:
```python
class ErroDeSchema(Exception):
    """O arquivo não tem as colunas esperadas."""

def checar_colunas(colunas, esperadas):
    faltando = set(esperadas) - set(colunas)
    if faltando:
        raise ErroDeSchema(f"colunas ausentes: {sorted(faltando)}")

checar_colunas(["id", "nome"], ["id", "nome", "email"])
# ErroDeSchema: colunas ausentes: ['email']
```
Regra prática: **erro em uma linha** → registre e siga; **erro estrutural** (arquivo sem colunas, banco fora do ar) → levante exceção e pare.

## 4. Logging em vez de `print`
`print` não diz quando, onde nem quão grave é a mensagem, e não dá para desligar em produção. O módulo `logging` resolve isso.

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)-8s | %(name)s | %(message)s",
)
logger = logging.getLogger(__name__)   # um logger por módulo

logger.debug("detalhe para depuração")        # não aparece com level=INFO
logger.info("iniciando carga de %s", "clientes.csv")
logger.warning("linha %d ignorada: %s", 42, "data inválida")
logger.error("falha ao conectar no banco")
```
Saída:
```
2024-06-01 10:00:00,123 | INFO     | __main__ | iniciando carga de clientes.csv
2024-06-01 10:00:00,124 | WARNING  | __main__ | linha 42 ignorada: data inválida
2024-06-01 10:00:00,124 | ERROR    | __main__ | falha ao conectar no banco
```

| Nível | Quando usar |
|-------|-------------|
| `DEBUG` | detalhes para investigar problemas |
| `INFO` | marcos normais (início, fim, quantas linhas) |
| `WARNING` | algo estranho, mas o processo continua |
| `ERROR` | uma operação falhou |
| `CRITICAL` | o sistema não consegue continuar |

> 💡 Use `logger.info("x=%s", x)` em vez de f-string: a formatação só acontece se a mensagem for realmente exibida.

### Registrando a exceção completa
```python
try:
    1 / 0
except ZeroDivisionError:
    logger.exception("erro no cálculo")   # nível ERROR + traceback completo
```

### Gravando em arquivo também
```python
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s | %(levelname)s | %(message)s",
    handlers=[logging.StreamHandler(), logging.FileHandler("pipeline.log", encoding="utf-8")],
)
```

## ❌ Armadilhas comuns
- `except: pass`, que engole erros e deixa o pipeline "verde" com dados errados.
- Logar dados sensíveis (CPF, senha, token).
- Chamar `logging.basicConfig` dentro de bibliotecas: configure o logging só no ponto de entrada do programa.

## ✏️ Pratique
Exercício **4** do [módulo 01](../README.md).

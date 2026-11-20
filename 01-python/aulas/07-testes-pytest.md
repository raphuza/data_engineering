# Aula 01.7 · Testes com pytest

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Um pipeline sem testes quebra em silêncio: o job fica verde e o dado sai errado. Testes automatizados garantem que uma mudança não estrague o que já funcionava, e são pré-requisito para CI/CD (módulo 11).

## 1. Primeiro teste
```python
# src/clima/conversores.py
def celsius_para_fahrenheit(c: float) -> float:
    return c * 9 / 5 + 32
```
```python
# tests/test_conversores.py
from clima.conversores import celsius_para_fahrenheit

def test_ponto_de_congelamento():
    assert celsius_para_fahrenheit(0) == 32

def test_ponto_de_ebulicao():
    assert celsius_para_fahrenheit(100) == 212
```
```bash
pip install pytest
pytest -v
# tests/test_conversores.py::test_ponto_de_congelamento PASSED
# tests/test_conversores.py::test_ponto_de_ebulicao PASSED
```
O pytest encontra sozinho arquivos `test_*.py` e funções `test_*`.

## 2. Estrutura de um bom teste: Arrange, Act, Assert
```python
def test_remove_espacos_e_padroniza_maiusculas():
    # Arrange: prepara a entrada
    entrada = ["  sp", "Rj ", "mg"]
    # Act: executa o que está sendo testado
    resultado = [uf.strip().upper() for uf in entrada]
    # Assert: verifica
    assert resultado == ["SP", "RJ", "MG"]
```

## 3. Números com casas decimais
```python
import pytest

def test_conversao_decimal():
    assert celsius_para_fahrenheit(36.6) == pytest.approx(97.88)
```
Comparar floats com `==` falha por arredondamento (`0.1 + 0.2 != 0.3`).

## 4. Testando que um erro acontece
```python
def dividir(a, b):
    if b == 0:
        raise ValueError("divisor não pode ser zero")
    return a / b

def test_divisao_por_zero():
    with pytest.raises(ValueError, match="zero"):
        dividir(1, 0)
```

## 5. Vários casos com `parametrize`
```python
@pytest.mark.parametrize("celsius, esperado", [
    (0, 32),
    (100, 212),
    (-40, -40),
])
def test_conversoes(celsius, esperado):
    assert celsius_para_fahrenheit(celsius) == esperado
```

## 6. Fixtures: preparar dados reutilizáveis
```python
@pytest.fixture
def arquivo_temperaturas(tmp_path):
    caminho = tmp_path / "temps.txt"      # tmp_path: pasta temporária criada pelo pytest
    caminho.write_text("10\n20\n\n30\n", encoding="utf-8")
    return caminho

def test_le_arquivo(arquivo_temperaturas):
    linhas = arquivo_temperaturas.read_text(encoding="utf-8").split()
    assert linhas == ["10", "20", "30"]
```
Testes que leem/escrevem arquivos devem usar `tmp_path` para não sujar seu projeto.

## 7. O que testar em código de dados
- **Caminho feliz**: entrada normal gera a saída esperada.
- **Bordas**: lista vazia, arquivo vazio, um único registro.
- **Dados sujos**: nulos, texto onde deveria ser número, datas inválidas.
- **Erros esperados**: a função levanta a exceção certa.

## ❌ Armadilhas comuns
- Testes que dependem uns dos outros ou de ordem de execução.
- Testar contra arquivos grandes reais: crie entradas pequenas e explícitas no próprio teste.
- Testar a implementação em vez do comportamento. Teste *o que* a função entrega, não *como*.

## ✏️ Pratique
Exercício **8** do [módulo 01](../README.md) e testes do projeto `csv-profiler`.

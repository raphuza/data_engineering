# Aula 03.2 · Dimensões que mudam (SCD)

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Clientes mudam de cidade, produtos mudam de categoria, funcionários mudam de departamento. A pergunta é: **as vendas antigas devem aparecer com o valor antigo ou com o novo?** A resposta define o tipo de *Slowly Changing Dimension* (SCD).

## 1. Os tipos mais usados
| Tipo | O que faz | Histórico | Quando usar |
|------|-----------|-----------|-------------|
| **0** | nunca muda | — | data de nascimento, data de cadastro |
| **1** | sobrescreve | não guarda | correções (nome digitado errado) |
| **2** | cria uma nova versão da linha | guarda tudo | quando a análise histórica importa (região do cliente na época da compra) |
| **3** | coluna "valor anterior" | só o último | raro |

## 2. SCD tipo 1
Simplesmente atualiza:
```sql
update dim_produto set nome = 'Caneca Térmica 500ml' where codigo_produto = 'A1';
```

## 3. SCD tipo 2: estrutura
Exemplo com **funcionários**, onde queremos saber em qual departamento cada um estava em qualquer data:

```sql
create table dim_funcionario (
    sk_funcionario serial primary key,     -- uma por VERSÃO
    matricula      int  not null,          -- chave natural (se repete entre versões)
    nome           text,
    departamento   text,
    valido_de      date not null,
    valido_ate     date,                   -- null = versão atual (intervalo [de, até) )
    atual          boolean not null default true
);
```
Depois de Ana mudar de "Dados" para "Engenharia" em 15/03/2024:
```
 sk | matricula | departamento | valido_de  | valido_ate | atual
----+-----------+--------------+------------+------------+-------
  1 |        10 | Dados        | 2024-01-01 | 2024-03-15 | f
  2 |        11 | Vendas       | 2024-01-01 |            | t
  3 |        10 | Engenharia   | 2024-03-15 |            | t
```
Fatos registrados antes de 15/03 apontam para `sk = 1`; depois, para `sk = 3`. Assim, o relatório de março mostra cada venda no departamento correto **da época**.

## 4. SCD tipo 2: a carga em dois passos
Partimos de uma tabela de *staging* (`stg_funcionario`) com a foto atual da origem.

**Passo 1, encerrar as versões que mudaram:**
```sql
update dim_funcionario d
set    valido_ate = date '2024-03-15',
       atual      = false
from   stg_funcionario s
where  d.matricula = s.matricula
  and  d.atual
  and  d.departamento is distinct from s.departamento;   -- compara tratando null corretamente
```

**Passo 2, inserir novas versões (e registros novos):**
```sql
insert into dim_funcionario (matricula, nome, departamento, valido_de)
select s.matricula, s.nome, s.departamento, date '2024-03-15'
from   stg_funcionario s
left join dim_funcionario d
       on d.matricula = s.matricula and d.atual
where  d.matricula is null;     -- não tem versão atual: é novo ou acabou de ser encerrado
```
Rode os dois passos **na mesma transação** para nunca deixar a dimensão pela metade.

> 💡 Por que `is distinct from` e não `<>`? Porque `null <> 'Dados'` dá `null` (não `true`), e a mudança passaria despercebida.

## 5. Consultando
Versão atual:
```sql
select * from dim_funcionario where atual;
```
Versão vigente em uma data:
```sql
select * from dim_funcionario
where date '2024-02-01' >= valido_de
  and (valido_ate is null or date '2024-02-01' < valido_ate);
```
Na carga da fato, é essa condição que escolhe a `sk` certa para cada evento.

## 6. Quais colunas versionar?
Nem toda mudança precisa de nova versão. É comum misturar: tipo 2 para atributos analíticos (departamento, cidade) e tipo 1 para correções (grafia do nome). Deixe isso documentado.

No módulo 07 você vai ver que o dbt implementa SCD2 automaticamente com **snapshots**. Entender a mecânica aqui é o que permite depurar quando algo sai errado.

## ❌ Armadilhas comuns
- Joins na dimensão SCD2 só pela chave natural: multiplica as linhas da fato (uma por versão).
- Esquecer novos registros (só tratar os que mudaram).
- Intervalos sobrepostos ou com buracos. Use a convenção `[valido_de, valido_ate)` e teste.

## ✏️ Pratique
Exercício **4** do [módulo 03](../README.md).

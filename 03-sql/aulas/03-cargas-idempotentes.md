# Aula 03.3 · Cargas full, incrementais e idempotentes

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Pipelines falham e são executados de novo. Também são reprocessados de propósito (*backfill*) quando se descobre um erro. Se rodar duas vezes a mesma carga duplica dados, o pipeline não é confiável. **Idempotência** significa: executar uma vez ou dez vezes dá o mesmo resultado.

## 1. Estratégias de carga
| Estratégia | Como funciona | Prós | Contras |
|-----------|---------------|------|---------|
| **Full** (*truncate + insert*) | apaga tudo e recarrega | simples, sempre idempotente | caro em tabelas grandes |
| **Incremental append** | insere só o que é novo | barato | duplica se reexecutar |
| **Incremental com upsert** | insere novos e atualiza existentes pela chave | idempotente, barato | precisa de chave única |
| **Delete + insert por partição** | apaga o período (ex.: o dia) e recarrega só ele | idempotente, ótimo para fatos | precisa de uma coluna de partição |

## 2. Upsert no Postgres: `INSERT ... ON CONFLICT`
Exemplo com cotações diárias de moedas:
```sql
create table cotacao (
    moeda         text,
    data          date,
    valor         numeric(10,4),
    carregado_em  timestamptz default now(),
    primary key (moeda, data)          -- a chave que define "o mesmo registro"
);

insert into cotacao (moeda, data, valor)
values ('USD', '2024-06-03', 5.2551),
       ('USD', '2024-06-04', 5.3010)
on conflict (moeda, data) do update
    set valor        = excluded.valor,   -- "excluded" = a linha que tentou entrar
        carregado_em = now();
```
Rodando de novo com o dia 04 corrigido e um dia novo:
```sql
insert into cotacao (moeda, data, valor)
values ('USD', '2024-06-04', 5.3050),
       ('USD', '2024-06-05', 5.2700)
on conflict (moeda, data) do update set valor = excluded.valor, carregado_em = now();
```
```
 moeda |    data    | valor
-------+------------+--------
 USD   | 2024-06-03 | 5.2551
 USD   | 2024-06-04 | 5.3050   ← atualizado, não duplicado
 USD   | 2024-06-05 | 5.2700   ← inserido
```
Use `on conflict do nothing` quando um registro, uma vez gravado, nunca muda.

### `MERGE` (Postgres 15+ e padrão SQL)
Mais expressivo, e é a sintaxe que você vai encontrar em Snowflake, BigQuery, Databricks e SQL Server:
```sql
merge into cotacao c
using (values ('USD', date '2024-06-05', 5.2800)) as n(moeda, data, valor)
   on c.moeda = n.moeda and c.data = n.data
when matched then
    update set valor = n.valor
when not matched then
    insert (moeda, data, valor) values (n.moeda, n.data, n.valor);
```

## 3. Delete + insert por partição
Para fatos grandes, reprocessar "o dia 2024-06-04" de forma idempotente:
```sql
begin;
delete from fato_acesso where data = date '2024-06-04';
insert into fato_acesso (data, usuario_id, pagina, acessos)
select data, usuario_id, pagina, count(*)
from   stg_acesso
where  data = date '2024-06-04'
group by 1, 2, 3;
commit;
```
A transação garante que ninguém veja a tabela no meio do processo. Esse padrão é o que o Airflow (módulo 06) usa com a `logical_date` de cada execução.

## 4. Incremental por *watermark*
Em vez de reler tudo, lemos só o que mudou desde a última carga. A **marca d'água** é o maior valor já carregado de uma coluna confiável (data de atualização, id sequencial):

```sql
select coalesce(max(data), date '1900-01-01') as ultima_data from cotacao;
```
O pipeline então busca na origem apenas registros com `data > ultima_data` e grava com upsert.

> ⚠️ Cuidado com dados que chegam **atrasados**. Uma prática comum é reprocessar uma janela de segurança (ex.: os últimos 3 dias) a cada execução. Como a carga é idempotente, reprocessar não causa problema.

## 5. Checklist de uma carga confiável
- [ ] Existe uma chave que define unicidade (e ela está declarada como `primary key` ou `unique`).
- [ ] Reexecutar não duplica nem perde dados.
- [ ] Tudo dentro de uma transação.
- [ ] Registra quantas linhas foram inseridas/atualizadas.
- [ ] Sabe lidar com dados atrasados.

## ❌ Armadilhas comuns
- Usar `now()` como watermark em vez da data do dado: registros criados durante a extração se perdem.
- Append puro "porque nunca vai rodar duas vezes". Vai.
- `ON CONFLICT` sem índice único na coluna: o Postgres recusa o comando.

## ✏️ Pratique
Exercício **5** do [módulo 03](../README.md).

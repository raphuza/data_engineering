# Aula 03.4 · Otimização no PostgreSQL

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Uma consulta que leva 2 segundos em desenvolvimento pode levar 20 minutos com o volume de produção e atrasar todo o pipeline. Saber ler um plano de execução e escolher índices e particionamento é o que permite consertar isso com método, em vez de tentativa e erro.

## 1. Lendo o plano com `EXPLAIN ANALYZE`
- `EXPLAIN`: mostra o plano que o banco **pretende** usar (não executa).
- `EXPLAIN ANALYZE`: **executa** e mostra tempos e linhas reais.
- `EXPLAIN (ANALYZE, BUFFERS)`: inclui quantas páginas vieram da memória ou do disco.

Exemplo em uma tabela de 500 mil pedidos, sem índice:
```sql
explain analyze select * from pedido where cliente_id = 4242;
```
```
Gather  (actual time=1.676..13.275 rows=16 loops=1)
  Workers Planned: 2
  ->  Parallel Seq Scan on pedido  (actual time=0.906..7.960 rows=5 loops=3)
        Filter: (cliente_id = 4242)
        Rows Removed by Filter: 166661
Execution Time: 13.291 ms
```
Leia **de dentro para fora**. `Seq Scan` + `Rows Removed by Filter` alto = leu a tabela toda para achar 16 linhas.

Com um índice:
```sql
create index idx_pedido_cliente on pedido (cliente_id);
```
```
Bitmap Heap Scan on pedido  (actual time=0.022..0.038 rows=16 loops=1)
  ->  Bitmap Index Scan on idx_pedido_cliente
        Index Cond: (cliente_id = 4242)
Execution Time: 0.051 ms
```
De 13 ms para 0,05 ms: **~260x** mais rápido.

### O que procurar no plano
| Sinal | Possível causa |
|-------|----------------|
| `Seq Scan` em tabela grande com filtro seletivo | falta índice |
| `rows=` estimado muito diferente do real | estatísticas desatualizadas → `ANALYZE` |
| `Sort` com `external merge Disk` | memória insuficiente (`work_mem`) |
| `Nested Loop` com milhões de linhas | join mal estimado ou sem índice |

## 2. Tipos de índice
| Índice | Bom para | Exemplo |
|--------|----------|---------|
| **B-tree** (padrão) | `=`, `<`, `>`, `between`, `order by` | `create index on pedido (cliente_id)` |
| **Composto** | filtros por várias colunas (a ordem importa!) | `create index on pedido (status, criado_em)` |
| **Parcial** | quando só um subconjunto é consultado | `create index on pedido (criado_em) where status = 'pendente'` |
| **BRIN** | tabelas enormes com dados em ordem física (logs, eventos) | `create index on acesso using brin (acessado_em)` |
| **GIN** | JSONB, arrays, busca textual | `create index on evento using gin (payload)` |

Índice composto `(status, criado_em)` atende `where status = 'x'` e `where status = 'x' and criado_em > ...`, mas **não** atende bem `where criado_em > ...` sozinho.

> ⚠️ Índices aceleram leitura e **atrasam escrita**. Em tabelas de staging que recebem cargas grandes, às vezes é melhor criar o índice depois da carga.

## 3. Reescrevendo consultas
Às vezes o problema não é índice, é a consulta:
```sql
-- ❌ função na coluna impede o uso do índice em criado_em
where date(criado_em) = '2024-06-01'

-- ✅ intervalo na coluna original
where criado_em >= '2024-06-01' and criado_em < '2024-06-02'
```
Outros clássicos: `select *` quando só 3 colunas são usadas, `not in` com subconsulta que pode ter nulos (prefira `not exists`), e CTEs grandes reutilizadas várias vezes.

## 4. Particionamento declarativo
Divide uma tabela grande em tabelas menores por um critério (normalmente tempo). Consultas que filtram pela coluna de partição leem só as partições necessárias (**partition pruning**), e apagar dados antigos vira `drop table` de uma partição.

```sql
create table acesso (
    id           bigint generated always as identity,
    acessado_em  timestamptz not null,
    usuario_id   int,
    pagina       text
) partition by range (acessado_em);

create table acesso_2024_01 partition of acesso for values from ('2024-01-01') to ('2024-02-01');
create table acesso_2024_02 partition of acesso for values from ('2024-02-01') to ('2024-03-01');
create table acesso_2024_03 partition of acesso for values from ('2024-03-01') to ('2024-04-01');
```
```sql
explain (costs off)
select count(*) from acesso
where acessado_em >= '2024-02-10' and acessado_em < '2024-02-20';
```
```
Aggregate
  ->  Seq Scan on acesso_2024_02 acesso
        Filter: ((acessado_em >= '2024-02-10') AND (acessado_em < '2024-02-20'))
```
Só `acesso_2024_02` foi lida; as outras partições foram ignoradas.

## 5. Manutenção: `ANALYZE` e `VACUUM`
- `ANALYZE tabela`: atualiza as estatísticas que o planejador usa para estimar linhas. Rode depois de cargas grandes.
- `VACUUM`: recupera espaço de linhas apagadas/atualizadas (o Postgres não sobrescreve no lugar). O *autovacuum* faz isso sozinho, mas cargas massivas podem precisar de ajuda.

## 6. OLTP vs OLAP: as regras mudam
Postgres é um banco **orientado a linhas**, pensado para transações. Data warehouses como BigQuery, Redshift, Snowflake e DuckDB são **colunares**:
- Lá quase não existem índices tradicionais. O que importa é **ler menos colunas** e **menos partições/blocos**.
- `select *` é caro de verdade (e cobrado, no BigQuery).
- Particionamento e *clustering* substituem boa parte do papel dos índices.

## ❌ Armadilhas comuns
- Criar índice em toda coluna "por garantia".
- Medir uma vez só: a segunda execução pode vir do cache. Rode algumas vezes e compare.
- Otimizar a consulta errada. Comece pela que mais consome tempo no pipeline.

## ✏️ Pratique
Exercícios **6** e **7** do [módulo 03](../README.md).

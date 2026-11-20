# Aula 03.1 · Modelagem dimensional

[⬅ Voltar ao módulo](../README.md)

## Por que isso importa
Um banco transacional (OLTP) é modelado para **gravar** rápido e sem inconsistência: tabelas normalizadas, muitas junções. Um data warehouse (OLAP) é modelado para **ler e analisar** rápido: perguntas como "receita por mês, por região, por categoria" precisam ser simples de escrever e baratas de executar. A modelagem dimensional, popularizada por Ralph Kimball, é a abordagem mais usada para isso e é a base dos *marts* que você vai construir com dbt.

## 1. Fatos e dimensões
- **Fato**: um evento de negócio mensurável. Tem as **métricas** (valores numéricos que se somam) e as chaves das dimensões. Ex.: uma venda, um ingresso vendido, um clique.
- **Dimensão**: o contexto do fato, com os atributos pelos quais filtramos e agrupamos. Ex.: quem, o quê, onde, quando.

Exemplo: uma **rede de cinemas**.
```
                 ┌──────────────┐
                 │  dim_filme   │
                 └──────┬───────┘
┌──────────────┐ ┌──────┴───────┐ ┌──────────────┐
│  dim_data    ├─┤ fato_ingresso├─┤  dim_sala    │
└──────────────┘ └──────┬───────┘ └──────────────┘
                 ┌──────┴───────┐
                 │ dim_cliente  │
                 └──────────────┘
```
Esse desenho com a fato no centro é o **esquema estrela** (*star schema*).

```sql
create table fato_ingresso (
    sk_data      int  not null references dim_data,
    sk_filme     int  not null references dim_filme,
    sk_sala      int  not null references dim_sala,
    sk_cliente   int  not null references dim_cliente,
    id_ingresso  text not null,        -- dimensão degenerada (identificador sem tabela própria)
    valor_pago   numeric(10,2) not null,
    desconto     numeric(10,2) not null default 0
);
```

## 2. O processo de 4 passos de Kimball
1. **Escolher o processo de negócio**: venda de ingressos.
2. **Declarar a granularidade**: *o que uma linha da fato representa?* → "um ingresso vendido".
3. **Identificar as dimensões**: data, filme, sala, cliente.
4. **Identificar os fatos (métricas)**: valor pago, desconto.

A **granularidade** é a decisão mais importante. Se uma linha fosse "uma compra" (que pode ter vários ingressos), você não conseguiria analisar por filme quando a compra inclui dois filmes. Regra geral: **modele no menor nível de detalhe disponível**; agregar depois é fácil, desagregar é impossível.

## 3. Tipos de tabela fato
| Tipo | Uma linha é... | Exemplo |
|------|----------------|---------|
| **Transacional** | um evento | cada ingresso vendido |
| **Snapshot periódico** | o estado em um momento fixo | ocupação de cada sala ao fim do dia |
| **Snapshot acumulativo** | o ciclo de vida de um processo, atualizado a cada etapa | pedido: data da compra, do pagamento, do envio, da entrega |

### Métricas aditivas, semi e não aditivas
- **Aditiva**: soma em qualquer dimensão (valor pago).
- **Semi-aditiva**: soma em algumas dimensões, mas não no tempo (saldo de conta, estoque).
- **Não aditiva**: nunca se soma (percentuais, preço unitário). Guarde numerador e denominador e calcule na consulta.

## 4. Chaves substitutas (*surrogate keys*)
A dimensão ganha uma chave própria, sem significado de negócio (`sk_filme`), além da chave natural da origem (`codigo_filme`).

Por que não usar só a chave natural?
- A origem pode reutilizar ou trocar códigos.
- Dados de sistemas diferentes podem ter chaves conflitantes.
- Permite guardar **várias versões** do mesmo registro (SCD tipo 2, próxima aula).
- Joins por inteiro são mais baratos.

```sql
create table dim_filme (
    sk_filme       serial primary key,   -- surrogate key
    codigo_filme   text not null,        -- chave natural
    titulo         text not null,
    genero         text,
    classificacao  text,
    duracao_min    int
);
```
> 💡 Em ferramentas como o dbt é comum usar *hash keys* (`md5(codigo_filme)`) como chave substituta, porque podem ser geradas sem depender de sequência do banco.

## 5. Dimensões de tempo com `generate_series`
Dimensões de calendário não vêm da origem: são **geradas**. O `generate_series` do Postgres cria uma linha por intervalo. Exemplo de uma **dimensão de horário** com sessões a cada 30 minutos:

```sql
create table dim_hora as
select
    row_number() over (order by h)  as sk_hora,
    h::time                         as hora,
    extract(hour from h)::int       as hora_do_dia,
    case
        when extract(hour from h) < 12 then 'manhã'
        when extract(hour from h) < 18 then 'tarde'
        else 'noite'
    end                             as periodo
from generate_series('2000-01-01 00:00'::timestamp,
                     '2000-01-01 23:30'::timestamp,
                     interval '30 minutes') as h;
```
```
 sk_hora |   hora   | hora_do_dia | periodo
---------+----------+-------------+---------
      23 | 11:00:00 |          11 | manhã
      37 | 18:00:00 |          18 | noite
```
A mesma técnica, com `interval '1 day'`, gera uma dimensão de datas.

## 6. Outras abordagens que você vai encontrar
- **Snowflake schema**: dimensões normalizadas em subdimensões. Economiza espaço, mas exige mais joins. Hoje é pouco recomendado.
- **One Big Table (OBT)**: fato e dimensões "achatadas" numa tabela larga. Comum em bancos colunares e para consumo por BI.
- **Data Vault**: hubs, links e satellites. Focado em auditoria e integração de muitas fontes, costuma ficar na camada anterior aos marts.

## ❌ Armadilhas comuns
- Não declarar a granularidade e misturar níveis na mesma fato (itens e totais do pedido).
- Guardar métricas calculadas não aditivas (ex.: ticket médio) na fato.
- Colocar atributos descritivos na fato em vez de na dimensão.

## ✏️ Pratique
Exercícios **2** e **3** do [módulo 03](../README.md).

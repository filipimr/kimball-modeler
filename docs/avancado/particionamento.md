# Particionamento

Tabelas grandes, como fatos de um data warehouse, podem ser divididas em partições. Esta página mostra como configurar e o que cada banco gera.

## Configurar

1. Abra Propriedades da tabela › **Particionamento**.
2. Escolha range, list ou hash e a coluna da chave.
3. Gere uma partição por mês (range) ou N partes (hash), ou escreva cada limite:
    - `from '2026-01-01' to '2026-02-01'` (range)
    - `in ('SP', 'RJ')` (list)
    - `default` (o que não cair em nenhuma outra)

As partições ficam dentro da tabela, que mostra um selo como **range ×12**.

!!! warning "Chave primária no PostgreSQL"
    No PostgreSQL a chave primária precisa conter a coluna da chave de partição.

## No DBML

```dbml
Table dw.fato_venda [partition_by: 'range (data_venda)',
                     partitions: 'fato_venda_2026_01: from \'2026-01-01\' to \'2026-02-01\'; fato_venda_outros: default'] {
  id bigint
  data_venda date [not null]
  indexes { (id, data_venda) [pk] }
}
```

`partition_by` aceita `range (colunas)`, `list (colunas)` ou `hash (colunas)`. Em `partitions`, cada entrada é `nome: limite`, separadas por `;`. Limites: `from X to Y`, `in (A, B)`, `modulus N remainder R` ou `default`. Não escreva `Table` para as partições: elas pertencem à tabela.

## SQL gerado

=== "PostgreSQL"

    ```sql
    CREATE TABLE dw.fato_venda (
        id bigint NOT NULL,
        data_venda date NOT NULL,
        CONSTRAINT fato_venda_pk PRIMARY KEY (id, data_venda)
    )
    PARTITION BY RANGE (data_venda);
    CREATE TABLE dw.fato_venda_2026_01 PARTITION OF dw.fato_venda FOR VALUES FROM ('2026-01-01') TO ('2026-02-01');
    CREATE TABLE dw.fato_venda_outros PARTITION OF dw.fato_venda DEFAULT;
    ```

=== "SQL Server"

    ```sql
    CREATE PARTITION FUNCTION fato_venda_pf (date) AS RANGE RIGHT FOR VALUES ('2026-02-01');
    GO
    CREATE PARTITION SCHEME fato_venda_ps AS PARTITION fato_venda_pf ALL TO ([PRIMARY]);
    GO
    CREATE TABLE dw.fato_venda (
        ...
    )
    ON fato_venda_ps (data_venda);
    ```

    O SQL Server só aceita range de uma coluna.

=== "Snowflake"

    ```sql
    CREATE TABLE dw.fato_venda (
        ...
    )
    CLUSTER BY (data_venda);
    ```

    O Snowflake não tem partições declaradas: a chave vira `CLUSTER BY`.

## Dica para data warehouse

Particione fatos grandes por mês da data do fato e mantenha uma partição `default` para datas fora da faixa.

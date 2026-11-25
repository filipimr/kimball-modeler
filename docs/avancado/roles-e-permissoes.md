# Roles e permissões

O modelo pode trazer as roles do banco e o que cada uma pode fazer em cada tabela. Esta página mostra como criar roles, conceder privilégios e o SQL que sai.

## Criar uma role

1. ++ctrl+k++ › **Nova role**, ou **+ role** nas Propriedades do projeto.
2. Marque login, se a role entra no banco, e escolha de quais roles ela é membro.

## Conceder permissões

1. Abra Propriedades da tabela › **Permissões**.
2. Escolha o privilégio e a role e clique em **+**.

schema
:   concede o mesmo em todas as tabelas do schema.

ligada/desligada
:   tira a permissão do script sem apagá-la do modelo.

Também vale escrever um revoke, como `revoke delete from public`.

## No DBML

```dbml
Note bi_reader [role: true] {
  'Leitura para o BI'
}

Note etl_writer [role: true, login: true, member_of: 'bi_reader'] {
  'Carga do ETL'
}

Table dw.fato_venda [grants: 'select to bi_reader; insert, update to etl_writer'] {
  id bigint [pk]
}
```

Roles não têm schema. Em `grants`, as entradas são separadas por `;`:

| Forma | Exemplo |
|---|---|
| conceder | `select to bi_reader` |
| com repasse | `insert, update to etl_writer with grant option` |
| revogar | `revoke delete from public` |
| desligada | `off delete to etl_writer` |

`public` vale para todos; `all` são todos os privilégios de tabela do banco.

## SQL gerado

=== "PostgreSQL"

    ```sql
    DO $$ BEGIN CREATE ROLE bi_reader NOLOGIN; EXCEPTION WHEN duplicate_object THEN NULL; END $$;
    DO $$ BEGIN CREATE ROLE etl_writer LOGIN; EXCEPTION WHEN duplicate_object THEN NULL; END $$;
    GRANT bi_reader TO etl_writer;
    ...
    GRANT SELECT ON TABLE dw.fato_venda TO bi_reader;
    GRANT INSERT, UPDATE ON TABLE dw.fato_venda TO etl_writer;
    ```

=== "SQL Server"

    ```sql
    IF DATABASE_PRINCIPAL_ID(N'bi_reader') IS NULL CREATE ROLE bi_reader;
    GO
    IF DATABASE_PRINCIPAL_ID(N'etl_writer') IS NULL CREATE ROLE etl_writer;
    GO
    ALTER ROLE bi_reader ADD MEMBER etl_writer;
    GO
    ...
    GRANT SELECT ON dw.fato_venda TO bi_reader;
    GO
    GRANT INSERT, UPDATE ON dw.fato_venda TO etl_writer;
    ```

=== "Snowflake"

    ```sql
    CREATE ROLE IF NOT EXISTS bi_reader COMMENT = 'Leitura para o BI';
    CREATE ROLE IF NOT EXISTS etl_writer COMMENT = 'Carga do ETL';
    GRANT ROLE bi_reader TO ROLE etl_writer;
    ...
    GRANT SELECT ON TABLE dw.fato_venda TO ROLE bi_reader;
    GRANT INSERT, UPDATE ON TABLE dw.fato_venda TO ROLE etl_writer;
    ```

## Dica para data warehouse

Uma role de leitura para o BI em todas as tabelas do schema e uma de escrita para o ETL cobrem a maioria dos casos.

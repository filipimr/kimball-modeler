# Bancos suportados

O mesmo modelo gera SQL para cinco bancos. A validação confere o modelo contra o que o banco e a versão de destino aceitam, então trocar o destino mostra na hora o que deixa de funcionar. O banco de destino aparece na pílula do topo (por exemplo, "PostgreSQL 16").

## Bancos e versões

| Banco | Versões | Padrão | Conexão direta |
|---|---|---|---|
| PostgreSQL | 12 a 18 | 16 | sim ([Conectar ao PostgreSQL](conectar-postgresql.md)) |
| SQL Server | 2016, 2017, 2019, 2022, 2025 | 2022 | não, use script |
| Snowflake | atual | atual | não, use script |
| MySQL | 8.0, 8.4, 9.0 | 8.4 | não, use script |
| Oracle | 19c, 21c, 23ai | 23ai | não, use script |

Nos bancos sem conexão direta, a engenharia reversa e a comparação usam o script DDL do banco. Veja [Importar](importar.md) e [Comparar e atualizar](comparar-e-atualizar.md).

## O que cada banco tem de particular

Propriedades físicas de um banco só vão para o DBML com o prefixo dele, e os outros bancos as ignoram.

=== "PostgreSQL"

    - Domínios viram `CREATE DOMAIN`.
    - Colunas geradas **virtuais** exigem PostgreSQL 18 ou mais novo; as armazenadas funcionam nas outras versões.
    - Único banco com conexão direta: importar e comparar/executar sem script.

=== "SQL Server"

    - Domínios viram um tipo apelido.
    - Use **Converter para outro banco** para ver o que muda nos tipos antes de trocar o destino.

=== "Snowflake"

    - Domínios são expandidos nas colunas.
    - Não gera índice para as FKs.
    - Propriedades com prefixo `snowflake_`, como `snowflake_cluster_by`.

=== "MySQL"

    - Propriedades com prefixo `mysql_`: `mysql_engine`, `mysql_charset`, `mysql_collation`, `mysql_row_format`, `mysql_auto_increment`, `mysql_collate`.
    - Importação a partir de `mysqldump --no-data`.

=== "Oracle"

    - Propriedades com prefixo `oracle_`: `oracle_tablespace`, `oracle_compress`, `oracle_blockchain`, `oracle_immutable`, `oracle_hashing`, `oracle_no_drop_days`, `oracle_no_delete_days`.
    - Importação a partir da saída de `DBMS_METADATA.GET_DDL`.

Exemplo de propriedade por banco:

```dbml
Table vendas.pedido [snowflake_cluster_by: 'data_pedido', mysql_engine: 'InnoDB'] {
  id bigint [pk]
  data_pedido date [not null]
}
```

!!! note "Outros bancos"
    Bancos que ainda não são suportados estão no [roadmap](../referencia/roadmap.md) como planejados.

## Nesta seção

- [Converter para outro banco](conversao.md)
- [Importar um banco](importar.md)
- [Conectar ao PostgreSQL](conectar-postgresql.md)
- [Comparar e atualizar o banco](comparar-e-atualizar.md)

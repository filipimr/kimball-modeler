# Colunas e índices avançados

Esta página cobre de onde vem o valor de uma coluna (default, autoincremento, coluna gerada) e as opções avançadas de índice.

## Valor da coluna (fx)

Em Propriedades da tabela, o botão **fx** na linha da coluna escolhe de onde vem o valor. É uma opção por coluna:

informado na gravação
:   a aplicação manda o valor.

default
:   um valor ou expressão: `0`, `'ativo'`, `now()`, `nextval('schema.seq')`.

autoincremento
:   início, passo e **ALWAYS** (o banco recusa valor informado; só no PostgreSQL).

gerada
:   calculada pelo banco a partir de colunas da própria tabela (`quantidade * preco`), armazenada ou virtual. A virtual exige PostgreSQL 18 ou mais novo.

A coluna gerada mostra **GEN** no lugar de **fx**. Renomear uma coluna reescreve as expressões que a usam.

```dbml
Table vendas.item {
  id bigint [pk, increment, identity: 'always', identity_start: 1000]
  quantidade numeric(12,3) [not null]
  preco numeric(15,2) [not null]
  valor_total numeric(15,2) [not null, generated: 'quantidade * preco']
}
```

Para a virtual, acrescente `generated_storage: 'virtual'`. Ter duas fontes de valor na mesma coluna (por exemplo `default` e `generated`) é erro.

=== "PostgreSQL"

    ```sql
    id bigint GENERATED ALWAYS AS IDENTITY (START WITH 1000) NOT NULL,
    ...
    valor_total numeric(15, 2) GENERATED ALWAYS AS (quantidade * preco) STORED NOT NULL,
    ```

=== "SQL Server"

    ```sql
    id bigint IDENTITY(1000,1) NOT NULL,
    ...
    valor_total AS (CAST((quantidade * preco) AS decimal(15, 2))) PERSISTED NOT NULL,
    ```

=== "Snowflake"

    O Snowflake não tem coluna gerada: `valor_total` sai como coluna comum, com aviso. Calcule na carga ou numa view.

## Índices avançados

Em Propriedades da tabela, o botão **opções** no índice abre:

| Opção | O que faz |
|---|---|
| só as linhas onde… | índice parcial; no SQL Server, índice filtrado |
| colunas incluídas | INCLUDE: índice de cobertura |
| DESC, NULLS FIRST/LAST | ordem por elemento |
| collation, classe de operador | por elemento |
| CONCURRENTLY | cria o índice sem travar a tabela (PostgreSQL) |

No DBML as opções ficam na linha do índice: `where`, `include`, `order`, `collate`, `opclass` e `concurrently`.

```dbml
Table vendas.item {
  id bigint [pk]
  quantidade numeric(12,3) [not null]
  preco numeric(15,2) [not null]
  ativo boolean [not null]

  indexes {
    (quantidade) [name: 'ix_item_ativo', where: 'ativo', include: 'preco']
  }
}
```

```sql title="PostgreSQL e SQL Server"
CREATE INDEX ix_item_ativo ON vendas.item (quantidade) INCLUDE (preco) WHERE ativo;
```

!!! warning "Extensão do Kimball"
    Essas opções de índice são próprias do Kimball. Outros leitores de DBML recusam arquivos que as usam. O que o banco de destino não tem aparece como fora do DDL, e o modelo guarda a opção assim mesmo.

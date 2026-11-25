# Domínios e sequences

Domínios são tipos de negócio reutilizáveis (e-mail, CPF, dinheiro). Sequences são contadores do banco. Esta página mostra como criar os dois e o que cada banco recebe.

## Domínios

1. ++ctrl+k++ › **Novo domínio**, ou **+ domínio** nas Propriedades do projeto.
2. Escolha o tipo base, o default, NOT NULL e as regras. Numa regra, `VALUE` é o valor da coluna (`VALUE LIKE '%@%'`).
3. Escreva o nome do domínio como tipo da coluna. O autocompletar do tipo o oferece.

Mudar o domínio muda todas as colunas dele. Renomeá-lo troca o tipo delas; apagá-lo devolve o tipo base (com **Desfazer**).

```dbml
Note cadastro.email [domain: true, type: 'varchar(254)', not_null: true, check_formato: 'VALUE LIKE \'%@%\''] {
  'E-mail'
}

Table cadastro.cliente {
  id bigint [pk]
  email cadastro.email
}
```

`check_<nome>` dá nome à regra. Aspas simples dentro do texto se escrevem `\'`.

=== "PostgreSQL"

    ```sql
    CREATE DOMAIN cadastro.email AS varchar(254) NOT NULL CONSTRAINT email_formato CHECK (VALUE LIKE '%@%');
    ...
    CREATE TABLE cadastro.cliente (
        id bigint NOT NULL,
        email cadastro.email,
        CONSTRAINT cliente_pk PRIMARY KEY (id)
    );
    ```

=== "SQL Server"

    ```sql
    CREATE TYPE cadastro.email FROM nvarchar(254) NOT NULL;
    GO
    CREATE TABLE cadastro.cliente (
        id bigint NOT NULL,
        email cadastro.email NOT NULL,
        CONSTRAINT cliente_pk PRIMARY KEY (id),
        CONSTRAINT cliente_email_formato CHECK (email LIKE '%@%')
    );
    ```

=== "Snowflake"

    ```sql
    CREATE TABLE cadastro.cliente (
        id BIGINT NOT NULL,
        email VARCHAR(254) NOT NULL,
        CONSTRAINT cliente_pk PRIMARY KEY (id)
    );
    ```

    O Snowflake não tem CHECK, então a regra fica de fora.

`expand_domains: true` no bloco `Project` repete tipo, default e regras em cada coluna, em qualquer banco.

## Sequences

1. ++ctrl+k++ › **Nova sequence**, ou **+ sequence** nas Propriedades do projeto (sem nada selecionado).
2. Preencha início, passo, mínimo, máximo, cache, ciclo, tipo e a coluna dona. Se a coluna dona sair, a sequence sai junto.

Sequences não aparecem no canvas. Numa coluna com autoincremento, o botão **INC** troca por uma sequence própria; numa coluna que usa sequence, **SEQ** volta para autoincremento (a sequence antiga fica no modelo).

```dbml
Table vendas.pedido {
  id bigint [pk, default: `nextval('vendas.seq_pedido')`]
}

Note vendas.seq_pedido [sequence: true, start: 1000, increment: 1, cache: 20, owned_by: 'vendas.pedido.id'] {
  'Numeração dos pedidos'
}
```

=== "PostgreSQL"

    ```sql
    CREATE SEQUENCE vendas.seq_pedido START WITH 1000 INCREMENT BY 1 CACHE 20;
    ...
        id bigint DEFAULT nextval('vendas.seq_pedido') NOT NULL,
    ...
    ALTER SEQUENCE vendas.seq_pedido OWNED BY vendas.pedido.id;
    ```

=== "SQL Server"

    ```sql
    CREATE SEQUENCE vendas.seq_pedido START WITH 1000 INCREMENT BY 1 MINVALUE 1 CACHE 20;
    ...
        id bigint CONSTRAINT pedido_id_df DEFAULT NEXT VALUE FOR vendas.seq_pedido NOT NULL,
    ```

=== "Snowflake"

    ```sql
    CREATE SEQUENCE vendas.seq_pedido START WITH 1000 INCREMENT BY 1;
    ...
        id BIGINT DEFAULT vendas.seq_pedido.NEXTVAL NOT NULL,
    ```

O que o banco não tem (OWNED BY no SQL Server, mínimo e máximo no Snowflake) fica fora, com aviso.

!!! tip "Autoincremento ou sequence?"
    Para a chave de uma tabela só, prefira autoincremento. Use sequence quando várias tabelas dividem a numeração, quando o número é de negócio (número da nota) ou quando o banco legado já tem uma.

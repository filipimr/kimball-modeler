# Importar um banco

A engenharia reversa transforma um banco existente num modelo novo, com tabelas, colunas, chaves, índices e relacionamentos. Abra **Importar** na tela inicial ou no topo e escolha a fonte.

## Fontes aceitas

| Fonte | O que você entrega |
|---|---|
| **Script SQL / DDL** | o script de criação do banco |
| **Modelo Físico XML (.pdm)** | o arquivo do modelo salvo em XML |
| **Planilha** | `.xlsx`, `.xls`, `.ods` ou `.csv`, uma linha por coluna |
| **Banco PostgreSQL conectado** | servidor, porta, banco e usuário ([Conectar ao PostgreSQL](conectar-postgresql.md)) |

Dá para importar vários arquivos de uma vez: eles viram um só modelo.

## Script SQL / DDL

Gere o script só com a estrutura (sem dados):

| Banco | Como gerar |
|---|---|
| PostgreSQL | `pg_dump --schema-only` |
| SQL Server | "Generate Scripts" do SSMS |
| Snowflake | `GET_DDL` |
| MySQL | `mysqldump --no-data` |
| Oracle | saída de `DBMS_METADATA.GET_DDL` |

## Modelo Físico XML (.pdm)

Traz tabelas, colunas, chaves, índices, relacionamentos e comentários. Os nomes de negócio vão para as notas, e as tabelas ficam na mesma posição do diagrama original. O arquivo precisa estar salvo em XML.

## Planilha

Uma linha por coluna. Cabeçalhos em português ou inglês.

| tabela | coluna | tipo | pk | nulo | padrao | descricao | referencia |
|---|---|---|---|---|---|---|---|
| vendas.cliente | id | bigint | sim | não | | Código do cliente | |
| vendas.pedido | id | bigint | sim | não | | | |
| vendas.pedido | id_cliente | bigint | | não | | | vendas.cliente.id |
| vendas.pedido | criado_em | timestamp | | não | now() | | |

A coluna `referencia` usa o formato `schema.tabela.coluna` e cria o relacionamento. A aba Colunas do dicionário exportado em Excel volta por aqui.

## Pelo terminal

```bash
kimball import script.sql --dialect postgres -o modelo.dbml
kimball import dump.sql --dialect mysql -o modelo.dbml
kimball import modelo.pdm -o modelo.dbml
kimball import planilha.xlsx -o modelo.dbml
```

Por exemplo, este script:

```sql title="banco.sql"
CREATE TABLE cliente (
  id bigint PRIMARY KEY,
  nome varchar(100) NOT NULL,
  fax varchar(20)
);
```

vira:

```dbml
Project banco {
  database_type: 'PostgreSQL'
}

Table cliente {
  id bigint [pk]
  nome varchar(100) [not null]
  fax varchar(20)
}
```

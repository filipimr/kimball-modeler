# DBML no Kimball

O modelo do Kimball é um arquivo DBML, a linguagem aberta de definição de bancos de dados. Esta página resume o que o Kimball lê e escreve, incluindo as marcações próprias dele. As marcações próprias são configurações comuns entre colchetes, então o arquivo continua sendo DBML válido.

Você não precisa escrever nada disso à mão: o diagrama grava o texto por você. A página serve para quem prefere o editor ou revisa o arquivo no Git.

## Projeto e banco de destino

```dbml
Project loja {
  database_type: 'PostgreSQL'   // PostgreSQL, SQL Server, Snowflake, MySQL ou Oracle
  target_version: '16'
  Note: 'Modelo da loja'
}
```

A pílula do banco, no topo do app, grava `database_type` e `target_version`. As versões aceitas estão em [Bancos de dados](../bancos/index.md).

## Tabelas, colunas, índices e regras

```dbml
Table cadastro.cliente [headercolor: #3B82F6, note: 'Clientes'] {
  id bigint [pk, increment]
  documento varchar(14) [not null, unique, note: 'CPF ou CNPJ sem máscara']
  status cadastro.status_cliente [not null, default: 'ativo']
  limite_credito decimal(15, 2) [default: 0, check: `limite_credito >= 0`]
  criado_em timestamptz [not null, default: `now()`]

  indexes {
    (documento) [unique, name: 'ux_cliente_documento']
    (status, criado_em) [name: 'ix_cliente_status_criado']
  }
}
```

Configurações de coluna: `pk`, `not null`, `null`, `unique`, `increment`, `default: ...`, `note: '...'`, `check: `...`` e `ref: > tabela.coluna`. Expressões ficam entre crases.

## Relacionamentos

```dbml
Ref fk_endereco_cliente: cadastro.endereco.id_cliente > cadastro.cliente.id [delete: cascade, update: cascade]
```

| Operador | Significado |
|---|---|
| `>` | muitos para um (filho > pai) |
| `<` | um para muitos |
| `-` | um para um |
| `<>` | muitos para muitos |

Ações em `delete:` e `update:`: `cascade`, `restrict`, `set null`, `set default` e `no action`. Veja [Relacionamentos](../guia/relacionamentos.md).

## Enum, grupos e notas

```dbml
Enum cadastro.status_cliente {
  ativo [note: 'contrato vigente']
  inativo
  "em análise"
}

TableGroup vendas [color: #22C55E] {
  vendas.pedido
  vendas.pedido_item
}

Note leia_me {
  'Post-it no canvas'
}
```

## Marcações próprias do Kimball

| Onde | Marcação | Para quê |
|---|---|---|
| Ref | `propagated: true` | as colunas FK do filho foram criadas pela relação e a acompanham (apagar a relação apaga as colunas) |
| Ref | `fk_index: 'btree'` | a relação mantém um índice nas colunas FK do filho |
| Ref | `deferrable` ou `deferrable: deferred` | FK adiável, conferida no commit (onde o banco tem) |
| Ref | `match: full` | MATCH FULL, em FK de várias colunas |
| Ref | `fk_constraint: false` | FK lógica: aparece no diagrama e na documentação, sem constraint no DDL |
| Ref | `inactive` | só desenho e documentação |
| Table | `associative: true` | tabela associativa criada por uma relação N:N |
| Table | `headercolor: #RRGGBB` | cor da tabela no diagrama |
| Table, Enum, Ref, Index | `ddl: false` | stub: fica no modelo, sem CREATE, e a comparação com o banco ignora |
| Project | `fk_on_delete`, `fk_on_update`, `fk_index` | padrões das relações novas do projeto |
| Project | `disabled_ddl: 'comment'` | stubs saem comentados no script em vez de omitidos |
| Project | `expand_domains: true` | expande os domínios em colunas comuns em qualquer banco |

Os recursos maiores têm página própria, com exemplos:

| Recurso | Marcações principais | Página |
|---|---|---|
| Domínios | `Note nome [domain: true, type: ..., check_<regra>: ...]` | [Domínios e sequences](../avancado/dominios-e-sequences.md) |
| Sequences | `Note nome [sequence: true, start: ..., owned_by: ...]` | [Domínios e sequences](../avancado/dominios-e-sequences.md) |
| Colunas geradas e identidade | `generated`, `generated_storage`, `identity`, `identity_start` | [Valores de coluna e índices](../avancado/colunas-e-indices.md) |
| Índices avançados | `where`, `include`, `order`, `collate`, `opclass`, `concurrently` | [Valores de coluna e índices](../avancado/colunas-e-indices.md) |
| Particionamento | `partition_by`, `partitions` | [Particionamento](../avancado/particionamento.md) |
| Roles e permissões | `Note nome [role: true]`, `grants` | [Roles e permissões](../avancado/roles-e-permissoes.md) |
| Stubs | `ddl: false` | [Tabelas stub e código por objeto](../avancado/stubs-e-codigo.md) |

## Opções físicas por banco

Opções que só existem num banco levam o prefixo dele. Cada banco usa as suas e ignora as dos outros, então o mesmo arquivo serve para todos.

```dbml
Table vendas.pedido [snowflake_cluster_by: 'criado_em', mysql_engine: 'InnoDB'] {
  id bigint [pk]
  criado_em timestamp
}
```

| Banco | Propriedades |
|---|---|
| PostgreSQL | `postgres_tablespace`, `postgres_unlogged`, `postgres_fillfactor`, `postgres_where` e `postgres_include` (índice), `postgres_collate` (coluna) |
| SQL Server | `sqlserver_filegroup`, `sqlserver_data_compression`, `sqlserver_clustered_pk`, `sqlserver_include`, `sqlserver_where` e `sqlserver_filegroup_index` (índice), `sqlserver_collate` (coluna) |
| Snowflake | `snowflake_cluster_by`, `snowflake_transient`, `snowflake_data_retention_days`, `snowflake_change_tracking`, `snowflake_collate` (coluna) |
| MySQL | `mysql_engine`, `mysql_charset`, `mysql_collation`, `mysql_row_format`, `mysql_auto_increment`, `mysql_collate` (coluna) |
| Oracle | `oracle_tablespace`, `oracle_compress`, `oracle_blockchain`, `oracle_immutable`, `oracle_hashing`, `oracle_no_drop_days`, `oracle_no_delete_days` |

O autocompletar do editor (++ctrl+space++) sugere as propriedades do banco de destino. No terminal, `kimball dialects` lista o que cada banco suporta.

## Posições do diagrama

As posições das tabelas não ficam no DBML. Elas vão para o arquivo `modelo.layout.json`, ao lado do modelo. Tabelas sem posição (escritas à mão ou criadas por outra ferramenta) são encaixadas sozinhas pelo app.

# Relacionamentos

Esta página explica como ligar tabelas, o que o Kimball cria em cada tipo de relação e como ajustar as ações da chave estrangeira.

## Criar uma relação

Selecione a tabela pai e arraste a **alça laranja** até a tabela filha. Ou escolha a ferramenta pelo teclado e clique nas duas tabelas:

| Tecla | Relação | O que nasce |
|---|---|---|
| ++1++ | 1:1 | coluna FK na filha |
| ++2++ | 1:N | coluna FK na filha (ex.: `id_cliente`) |
| ++3++ | N:N | tabela associativa `many_a_has_many_b`, com as duas FKs |

As colunas criadas por uma relação são **propagadas**: o tipo acompanha a chave do pai, o nome aparece em itálico e elas somem quando a relação é apagada.

## Como fica no DBML

```dbml
Table vendas.pedido_produto [associative: true] {
  id_pedido bigint [pk, not null]
  sku_produto varchar(30) [pk, not null]
  quantidade int [not null, default: 1]
}

Ref pedido_fk: vendas.pedido_produto.id_pedido > vendas.pedido.id [propagated: true]
Ref produto_fk: vendas.pedido_produto.sku_produto > vendas.produto.sku [propagated: true, update: cascade]
```

`[propagated: true]` marca a relação dona das colunas; `[associative: true]` marca a tabela criada por um N:N. Os dois são DBML válido em qualquer leitor.

## Ações padrão

Relações novas usam ON UPDATE CASCADE. Ao apagar, a FK obrigatória usa ON DELETE RESTRICT e a opcional, SET NULL. Troque o padrão em **Configurações** › Ações das FKs novas, ou fixe no projeto:

```dbml
Project loja {
  database_type: 'PostgreSQL'
  fk_on_delete: 'cascade'
  fk_on_update: 'no action'
}
```

## Popover da relação

Clique na linha: ela fica laranja com três contas, as duas colunas acendem e um popover deixa trocar a cardinalidade, a ação ao apagar, o **Índice da FK** e o nome da FK.

## Propriedades da relação

ON DELETE / ON UPDATE
:   `cascade`, `restrict`, `set null`, `set default` ou `no action`.

Adiamento
:   a FK é conferida só no commit. Útil em cargas que inserem filhos antes dos pais.

MATCH
:   MATCH FULL, para FKs de várias colunas.

Adiamento e MATCH aparecem quando o banco de destino tem. O que o banco não tem fica fora do SQL e aparece em [Problemas](validacao.md).

## Índice da FK

Sem índice, apagar o pai e juntar as tabelas varre a filha inteira. **Índice da FK** cria `{tabela_pai}_idx` nas colunas da FK, com o método do banco. O índice acompanha a relação e some com ela. O padrão fica em **Configurações** ou em `fk_index: 'btree'` no bloco `Project`. No Snowflake, que não tem índices, a escolha fica guardada e nada é gerado. FK escrita à mão sem índice gera um aviso.

## Tabela ligada a ela mesma

Para hierarquias (funcionário → gestor), com ++1++, ++2++ ou ++3++ faça ++shift++ + clique na tabela, ou use **Mais** › Ligar à própria tabela. A FK nasce opcional, porque as raízes não têm pai, e a relação aparece como um laço à direita. Se a FK virar obrigatória, o Kimball avisa que a primeira linha nunca poderá ser inserida.

## Converter em FK manual

Para editar as colunas da FK por conta própria (nome, tipo, nulo), use **Converter em FK manual** no popover ou em Propriedades. A relação continua desenhada; depois, apagá-la remove só a FK. **Desfazer** volta atrás.

## Ler o desenho

| Sinal | Significado |
|---|---|
| chave amarela | chave primária |
| elo laranja | chave estrangeira (itálico: criada pela relação) |
| linha tracejada | FK lógica, sem constraint no SQL |
| pé de galinha | lado "muitos" |

**Configurações** › Relacionamentos troca o pé de galinha por linha simples.

## Apagar uma relação

Selecione a linha e aperte ++del++. As colunas que ela criou vão junto, e **Desfazer** devolve tudo.

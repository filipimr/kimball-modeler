# Stubs e código

Algumas tabelas precisam estar no modelo sem que você as crie, porque outro time é dono delas. Esta página mostra como marcá-las como stub e como ver o SQL de qualquer seleção com o painel Código.

## Tabela de outro time (stub)

1. Abra Propriedades da tabela.
2. Desmarque **Gerar DDL**.

A tabela continua no modelo, para as FKs e a documentação. Ela ganha borda tracejada e o selo **DDL off**, sai do script e a comparação com o banco a ignora. **Desligar também nos dependentes** faz o mesmo com as FKs que apontam para ela.

```dbml
Table rh.funcionario [ddl: false] {
  id bigint [pk]
}

Table vendas.pedido {
  id bigint [pk]
  id_vendedor bigint [ref: > rh.funcionario.id]
}
```

`ddl: false` também vale para enums, relações e índices.

## Stub comentado no script

Para o leitor do script saber que a tabela existe, use `disabled_ddl: 'comment'` no bloco `Project`. O stub sai comentado:

```dbml
Project loja {
  database_type: 'PostgreSQL'
  disabled_ddl: 'comment'
}
```

```sql
-- [DDL desativado: objeto gerenciado fora deste modelo]
-- CREATE TABLE rh.funcionario (
--     id bigint NOT NULL,
--     CONSTRAINT funcionario_pk PRIMARY KEY (id)
-- );
CREATE TABLE vendas.pedido (
    id bigint NOT NULL,
    id_vendedor bigint,
    CONSTRAINT pedido_pk PRIMARY KEY (id)
);
ALTER TABLE vendas.pedido ADD CONSTRAINT pedido_id_vendedor_fk FOREIGN KEY (id_vendedor) REFERENCES rh.funcionario (id);
```

## Código (Alt S)

++alt+s++ (ou **Prévia** no **Ver DDL**, ou a aba Código da folha Propriedades) mostra o SQL da seleção em qualquer banco e versão, sem trocar o destino do projeto.

| Opção | O que entra |
|---|---|
| **só o objeto** | o CREATE da seleção |
| **com índices e FKs** | o objeto, seus índices e chaves estrangeiras |
| **com o que ele precisa** | schemas, enums e tabelas pais: roda num banco vazio |
| **+ tabela…** | junta outras tabelas |
| **DBML** | o trecho do arquivo em vez do SQL |

Os avisos daquela versão aparecem em cima. Por exemplo, em PostgreSQL 12 a opção "nulos iguais" fica fora do DDL. Opções que só existem em algumas versões levam um selo com a faixa, como `PostgreSQL ≥ 15`.

Para o SQL do modelo inteiro, use **Exportar** › Script DDL (veja [Exportar](../guia/exportar.md)).

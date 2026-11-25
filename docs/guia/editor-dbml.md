# Editor DBML

O texto DBML é a fonte do modelo. Esta página mostra como trabalhar no editor, como ele conversa com o canvas e o que acontece quando outro programa muda o arquivo.

## Modos de visualização

| Atalho | Modo |
|---|---|
| ++ctrl+1++ | Diagrama: só o canvas |
| ++ctrl+2++ | Dividido: canvas e editor à direita |
| ++ctrl+3++ | DBML: só o editor |
| ++ctrl+backslash++ | alterna entre Diagrama e Dividido |

No modo Dividido, arraste a divisória para mudar a largura do editor (também com ++arrow-left++ / ++arrow-right++); clique duplo nela volta a 500 px. A tabela selecionada no canvas acende no editor.

## Ajuda enquanto você digita

| Atalho | O que faz |
|---|---|
| ++ctrl+space++ | sugere palavras, tipos e colunas, também dentro de `Ref` |
| ++f12++ | vai para a definição |
| ++f2++ | renomeia em todo o arquivo |
| ++shift+alt+f++ | formata o arquivo |

Erros aparecem sublinhados, com a linha e a coluna.

```dbml
Table cliente {
  id bigint [pk, increment]
  nome varchar(200) [not null]
}

Table pedido {
  id bigint [pk]
  id_cliente bigint [ref: > cliente.id]
}
```

## Canvas e texto sincronizados

O que você digita aparece no canvas. O que você muda no canvas reescreve só o trecho afetado: comentários, ordem e formatação do resto do arquivo ficam como estavam. ++ctrl+z++ usa o mesmo histórico no editor e no canvas.

## Mudanças feitas fora do app

Uma IA na IDE (pelo [MCP](../mcp/index.md)), o [terminal](../cli/index.md) ou outro editor podem mudar o arquivo aberto. O app confere a cada 2 segundos:

- **sem alterações não salvas no app:** recarrega sozinho e avisa; ++ctrl+z++ volta ao que estava;
- **com alterações não salvas:** pergunta se carrega a versão do arquivo ou mantém a sua. Mesmo carregando, o que você tinha continua no desfazer.

## Propriedades de um banco

Opções físicas de um banco vão entre colchetes, com o prefixo dele. Cada banco usa as suas e ignora as dos outros, e o arquivo continua válido em qualquer leitor de DBML.

```dbml
Table dw.fato_venda [snowflake_cluster_by: 'data_venda', postgres_fillfactor: 90] {
  id bigint [pk]
  data_venda date [not null]
}
```

| Prefixo | Exemplos |
|---|---|
| `postgres_` | `postgres_tablespace`, `postgres_unlogged`, `postgres_fillfactor` |
| `sqlserver_` | `sqlserver_filegroup`, `sqlserver_data_compression` |
| `snowflake_` | `snowflake_cluster_by`, `snowflake_transient` |
| `mysql_` | `mysql_engine`, `mysql_charset`, `mysql_row_format` |
| `oracle_` | `oracle_tablespace`, `oracle_compress` |

O autocompletar do editor mostra a lista completa. A sintaxe inteira está na [referência de DBML](../referencia/dbml.md).

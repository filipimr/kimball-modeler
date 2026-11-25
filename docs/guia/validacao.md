# Validação

O Kimball confere o modelo contra o banco e a versão de destino enquanto você trabalha. Esta página mostra onde ver os problemas e como corrigi-los.

## Pílula de validação

Ao lado da pílula do banco, no topo, fica o resumo: "Sem problemas", "N avisos" ou "N erros". Clique nela para abrir a folha **Problemas**. O aviso também aparece na linha da coluna e no editor DBML.

## Erros e avisos

| Tipo | O que significa | Exemplo |
|---|---|---|
| Erro | o banco não aceita; bloqueia a exportação | CHECK no Snowflake |
| Aviso | o SQL sai, mas algo se perde ou merece atenção | perda de tipo, FK sem índice |

Trocar o banco de destino muda a validação: um modelo sem problemas no PostgreSQL pode ter erros no Snowflake.

## Folha Problemas

Cada item leva até a linha do problema.

## Aplicar correção

Os casos seguros trazem **Aplicar correção**. Um clique aplica, e **Desfazer** volta.

| Problema |
|---|
| UNIQUE que repete a chave primária |
| FK com SET NULL em coluna que não aceita nulo |
| índice ou FK com o mesmo nome de outro objeto do schema |

## Validar antes de salvar, exportar e comparar

Em **Configurações** › Validação, a opção "Validar antes de salvar, exportar e comparar" vem ligada. Com ela, salvar avisa dos erros, e exportar o DDL e comparar com o banco esperam a correção.

## Corrigir com o assistente

Na folha Problemas, **Corrigir com o assistente** manda o problema para o [assistente de IA](../assistente/index.md). A correção volta como proposta no canvas e só entra no modelo quando você clica em **Aplicar**.

## No terminal

`kimball validate` faz a mesma conferência sem abrir o app, útil em CI. Veja [Comandos](../cli/comandos.md).

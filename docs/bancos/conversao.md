# Converter para outro banco

Trocar o banco de destino de um modelo é uma conversão: tipos mudam de nome, e recursos que o outro banco não tem se perdem. O Kimball mostra tudo isso antes de trocar.

## Como converter

1. Clique na pílula do banco de destino, no topo (por exemplo, "PostgreSQL 16").
2. Escolha **Converter para outro banco** e o banco e a versão de destino.
3. Leia o relatório: o que muda (tipos) e o que se perde (recursos que o outro banco não tem).
4. Confirme para trocar o destino, ou cancele e o modelo fica como estava.

Depois da troca, a [validação](../guia/validacao.md) passa a conferir o modelo contra o novo banco. Avisos indicam perdas; erros bloqueiam a exportação até serem corrigidos.

## Guardar o relatório

O relatório pode ser salvo em Markdown, para anexar a um ticket ou revisar com a equipe antes da migração.

## Só conferir, sem trocar

Para ver como uma tabela fica em outro banco sem mudar o destino, selecione-a e abra **Código** (++alt+s++): ele mostra o SQL da seleção em qualquer banco e versão, com os avisos de cada um.

## Pela IA da IDE

Pelo servidor MCP, a ferramenta `convert_report` devolve o mesmo relatório de conversão para o banco (e a versão) que você pedir, sem alterar o arquivo. Veja [Ferramentas MCP](../mcp/ferramentas.md).

No terminal não há um comando de conversão. Para conferir o SQL e os problemas em outro banco, use `validate` e `export` com `--dialect`:

```bash
kimball validate modelo.dbml --dialect mysql --target-version 8.4
kimball export modelo.dbml --dialect sqlserver -o modelo.sql
```

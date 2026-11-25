# Exportar

Esta página lista tudo o que sai do modelo: SQL, script de alteração, dicionário, esquemas e imagens do diagrama.

## Exportar (Ctrl E)

O botão laranja **Exportar** no topo (++ctrl+e++) abre a folha com as opções:

| Formato | Para quê |
|---|---|
| Script DDL | criar o banco do zero |
| Script de alteração | levar um banco existente até o modelo, sem executar nada |
| Dados de teste | INSERTs para o banco de destino ou um CSV por tabela |
| Dicionário de dados | HTML (imprime em PDF), Word (.rtf), Excel (.xlsx) ou CSV |
| JSON Schema / XML Schema (XSD) | contratos para APIs e integrações |
| Arquivo .dbml | uma cópia do modelo |
| Banco × modelo | [comparar e atualizar o banco](../bancos/comparar-e-atualizar.md) |

## Script DDL

A prévia mostra o SQL do modelo inteiro e dá para ver em outro banco sem trocar o destino do projeto.

Formato
:   script único, um arquivo por objeto (`001_schema_vendas.sql`, `002_table_...`), um por tipo (schemas, enums, tabelas, índices, chaves estrangeiras) ou cada tabela com seus índices. Rodados em ordem de nome, criam o banco.

DROP antes de criar
:   recria o banco.

CASCADE e IF NOT EXISTS
:   só aparecem no banco que os tem (PostgreSQL).

Comentários numa linha
:   junta os comentários.

**Salvar na pasta…** grava os arquivos. Use uma pasta vazia: arquivos antigos com outros números continuariam lá. **Script de DROP…** salva só os DROP, dependentes primeiro. Todo arquivo traz um cabeçalho com o banco e, se o `Project` tiver, nome, `version` e `author`.

## Script de alteração

Escolha o script do banco atual. Sai só o que muda para ele ficar igual ao modelo, com os passos destrutivos marcados. Nada é executado.

## Dicionário de dados

O Excel tem as abas Tabelas, Colunas e Relacionamentos. A aba Colunas pode voltar pelo [Importar](../bancos/importar.md).

## Exportar diagrama (Ctrl Shift E)

**Exportar** › Diagrama… ou ++ctrl+shift+e++. A imagem segue o tamanho do diagrama, não o da janela: entram todas as tabelas do recorte, mesmo as que estão fora da tela. A prévia mostra o resultado e o tamanho em pixels.

| Formato | Opções |
|---|---|
| PNG, JPG | 1×, 2× ou 3× de nitidez; imagens grandes demais para a memória são reduzidas sozinhas |
| SVG | no tamanho do diagrama |
| PDF | tamanho do diagrama, ou A4, A3 ou Carta, em pé ou deitada conforme o desenho; com título e banco de destino |
| Markdown | diagrama Mermaid, a imagem PNG ao lado e o dicionário de dados |

O que exportar
:   o diagrama inteiro, a tabela selecionada e as ligadas, um grupo ou um schema.

Cores
:   como na tela, claro (para imprimir) ou escuro.

## Pelo terminal

`kimball export` gera o DDL sem abrir o app. Veja [Comandos](../cli/comandos.md).

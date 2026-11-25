# Ferramentas MCP

O servidor `kimball mcp` oferece 39 ferramentas, agrupadas abaixo pelo que fazem. Você não precisa chamá-las pelo nome: peça em linguagem natural e a IA escolhe. Esta página serve para saber o que a IA consegue fazer e o que cada modo de trava libera.

Toda ferramenta aceita o argumento `file` para trabalhar num `.dbml` diferente do padrão (com `--locked`, só o arquivo travado).

## Ler o modelo

| Ferramenta | O que faz |
|---|---|
| `model_overview` | Projeto, banco de destino, tabelas, relacionamentos (com número), enums, grupos e problemas. |
| `table_show` | Colunas, índices, regras, propriedades e relacionamentos de uma tabela. |
| `object_dependencies` | Quem usa um objeto e do que ele precisa, para saber o que quebra antes de mudar. |
| `dbml_read` | O texto DBML completo. |
| `validate` | Erros e avisos para o banco de destino, ou outro banco informado. |
| `diagram_view` | O diagrama em palavras: grupos, posições, cores, notas e visões salvas. |

## Editar o modelo

| Ferramenta | O que faz |
|---|---|
| `new_model` | Cria um `.dbml` novo só com o bloco Project. Não sobrescreve. |
| `set_project` | Muda o nome do projeto, o banco de destino e a versão. |
| `create_table` | Cria uma tabela com colunas, descrição e cor. |
| `set_table_note` | Define ou apaga a descrição de uma tabela. |
| `rename_table` | Renomeia a tabela e atualiza as referências. |
| `delete_table` | Remove a tabela e os relacionamentos dela. |
| `add_column` | Acrescenta uma coluna. |
| `update_column` | Muda só os campos informados de uma coluna (`name` renomeia). |
| `delete_column` | Remove uma coluna. |
| `create_relationship` | Relaciona pai e filho e cria a coluna FK no filho. `1:1`, `1:N` ou `N:N` (tabela associativa), opcional, lógico, ações e índice da FK. |
| `link_columns` | Liga colunas que já existem, sem criar colunas. |
| `delete_relationship` | Remove um relacionamento pelo número mostrado em `model_overview`. |
| `detach_relationship` | Converte uma relação propagada em FK manual. |
| `set_ddl_enabled` | Liga ou desliga a DDL de um objeto (stub), opcionalmente também nos dependentes. |
| `create_enum` | Cria um tipo enumerado. |
| `add_check` | Acrescenta uma regra CHECK à tabela. |
| `set_group` | Cria ou atualiza um grupo de tabelas, com cor. |
| `create_domain` | Cria um domínio: tipo base, NOT NULL, default e regras com `VALUE`. |
| `create_sequence` | Cria uma sequence, opcionalmente dona de uma coluna. |
| `create_role` | Cria uma role (grupo de permissões). |
| `set_permissions` | GRANT e REVOKE por tabela ou para o schema inteiro. |
| `set_partitioning` | Particiona por range, list ou hash, ou tira o particionamento. |
| `apply_operations` | Várias operações de uma vez, tudo ou nada. |
| `dbml_write` | Troca o texto inteiro. Recusa DBML com erro de sintaxe. |

As edições mudam só o trecho afetado do arquivo. `dbml_write` é a exceção e fica para mudanças grandes.

## Gerar

| Ferramenta | O que faz |
|---|---|
| `generate_ddl` | Script de criação para o banco de destino ou outro, inteiro, dividido em arquivos, só os DROP, ou o código de alguns objetos. Não executa nada. |
| `test_data` | INSERTs de dados de teste coerentes com chaves e enums. |

## Comparar e importar

| Ferramenta | O que faz |
|---|---|
| `diff_models` | Diferenças entre outro `.dbml` (antes) e o modelo (depois). |
| `alter_script` | Compara o script do banco atual com o modelo e devolve o ALTER. Não executa nada. |
| `import_sql` | Converte um script DDL em DBML, opcionalmente gravando num arquivo novo (`save_as`). |

## Bancos

| Ferramenta | O que faz |
|---|---|
| `list_dialects` | Bancos de destino, versões e versão padrão. |
| `dialect_info` | O que o banco aceita naquela versão e como os tipos comuns são traduzidos. |
| `convert_report` | O que muda ao levar o modelo para outro banco: tipos adaptados, perdas e validação. |

## Skills

| Ferramenta | O que faz |
|---|---|
| `read_skill` | Lê um guia do Kimball: convenções de modelagem relacional e dimensional, referência de DBML, uso do app, CLI e engenharia reversa. |

Veja [Skills](skills.md).

## O que fica disponível com cada trava

| Modo | Ferramentas |
|---|---|
| Sem trava | Todas as 39. |
| `--locked` | Todas, menos `new_model`, `dbml_write` e `diff_models`. `import_sql` funciona, mas sem `save_as`. |
| `--locked --read-only` | Só as que não mudam nada: `model_overview`, `table_show`, `object_dependencies`, `dbml_read`, `validate`, `diagram_view`, `generate_ddl`, `test_data`, `alter_script`, `list_dialects`, `dialect_info`, `convert_report` e `read_skill`. |

As ferramentas que só leem vêm marcadas como somente leitura, e as que removem (`delete_table`, `delete_column`, `delete_relationship`, `dbml_write`, `apply_operations`) como destrutivas. Se a sua IDE estiver configurada para isso, ela pede confirmação antes das destrutivas. Destrutivo aqui quer dizer que remove do modelo, nunca do banco.

## Prompt

Além das ferramentas, o servidor oferece o prompt `modelar`, que recebe o assunto a modelar e traz as convenções do Kimball. Veja [IA na IDE](index.md#o-prompt-modelar).

# Referência de comandos

Todos os comandos e opções do `kimball`, agrupados por finalidade. Para ver a mesma lista no terminal, rode `kimball help`.

## Convenções

Bancos (`--dialect ID`, ou `-d ID`)
:   `postgres`, `sqlserver`, `snowflake`, `mysql`, `oracle`. Sem a opção, vale o `database_type` do bloco `Project` do modelo.

Versão (`--target-version V`)
:   Versão do banco de destino, por exemplo `16` no PostgreSQL ou `2022` no SQL Server. Sem a opção, vale a do modelo ou a versão padrão do banco.

Saída (`-o arquivo`, ou `--output arquivo`)
:   Grava o resultado no arquivo. Sem a opção, o resultado sai na tela.

Colunas
:   `nome:tipo[:pk][:nn][:uq][:inc]`: chave primária, NOT NULL, UNIQUE e autoincremento. Tipos com parênteses vão entre aspas: `"nome:varchar(200):nn"`.

Tabelas
:   `schema.tabela` ou só `tabela`, quando não há outra com o mesmo nome em outro schema.

### Códigos de saída

| Código | Significado |
|---|---|
| `0` | Deu certo. |
| `1` | Problemas encontrados: erros de validação (`validate`, `export`, `ddl`), arquivo fora do formato (`fmt --check`), diferenças (`diff`, `alter`). |
| `2` | Uso incorreto ou erro ao ler ou gravar arquivo. |

Avisos de validação não mudam o código de saída. Só erros fazem `validate` sair com `1`.

## Modelar

Cada comando muda só o trecho afetado do `.dbml`. Comentários e formatação do resto ficam intactos.

| Comando | O que faz |
|---|---|
| `kimball new <arquivo.dbml> [--dialect ID] [--target-version V] [--name N]` | Cria um modelo novo só com o bloco `Project`. |
| `kimball show <arquivo.dbml> [tabela]` | Visão geral do modelo, ou os detalhes de uma tabela. |
| `kimball add-table <arquivo.dbml> <schema.tabela> [colunas ...] [--note T]` | Cria uma tabela, com colunas e descrição opcionais. |
| `kimball add-column <arquivo.dbml> <tabela> <coluna> [--note T]` | Acrescenta uma coluna. |
| `kimball drop-column <arquivo.dbml> <tabela> <coluna>` | Remove uma coluna. |
| `kimball rename-table <arquivo.dbml> <tabela> <novo_nome>` | Renomeia a tabela e atualiza as referências. |
| `kimball drop-table <arquivo.dbml> <tabela>` | Remove a tabela e os relacionamentos dela. |
| `kimball link <arquivo.dbml> <pai> <filho> [--kind 1:1\|1:N\|N:N] [--optional] [--logical] [--fk-index MÉTODO\|none]` | Relaciona pai e filho e cria a coluna FK no filho. |
| `kimball deps <arquivo.dbml> <objeto> [--transitive]` | Quem usa o objeto e do que ele precisa. |
| `kimball set-ddl <arquivo.dbml> <objeto> on\|off [--dependents]` | Liga ou desliga a DDL de um objeto (stub). |
| `kimball detach-rel <arquivo.dbml> <nome-da-FK\|número>` | Converte uma relação propagada em FK manual. |
| `kimball add-domain <arquivo.dbml> <schema.nome> --type <tipo> [--not-null] [--default V] [--rule nome=regra ...] [--note T]` | Cria um domínio. |
| `kimball add-sequence <arquivo.dbml> <schema.nome> [--type bigint] [--start N] [--increment N] [--owned-by tabela.coluna] [--note T]` | Cria uma sequence. |
| `kimball add-role <arquivo.dbml> <nome> [--login] [--member-of a,b] [--note T]` | Cria uma role (grupo de permissões). |
| `kimball grant <arquivo.dbml> <tabela\|schema.*> "privilégios to role" ... [--reset]` | Dá ou revoga permissões. |
| `kimball partition <arquivo.dbml> <tabela> --by "range (coluna)" [...]` | Particiona uma tabela, ou tira o particionamento com `--off`. |
| `kimball apply <arquivo.dbml> <operacoes.json\|-> [--dry-run]` | Aplica várias operações de uma vez, tudo ou nada. |

### link

O pai é o lado 1, o filho recebe a coluna FK (por exemplo `id_cliente`).

`--kind 1:1|1:N|N:N`
:   Cardinalidade. O padrão é `1:N`. `N:N` cria a tabela associativa.

`--optional`
:   A FK aceita nulo: o filho pode existir sem o pai.

`--logical`
:   Só documenta a relação, sem constraint no banco.

`--fk-index btree|hash|...|none`
:   Cria um índice nas colunas da FK com o método informado, ou nenhum com `none`.

```bash
kimball link vendas.dbml cliente pedido
kimball link vendas.dbml cliente pedido --fk-index btree
kimball link vendas.dbml produto pedido --kind N:N
```

### deps

Aceita `tabela`, `tabela.coluna`, `enum:nome` ou o nome de uma FK. Com `--transitive`, segue a cadeia e mostra também os dependentes indiretos.

```bash
kimball deps vendas.dbml cliente.id --transitive
```

### set-ddl

Um objeto com a DDL desligada continua no modelo (para FKs e documentação), mas não entra no script nem na comparação com o banco. `--dependents` aplica a mesma escolha a quem usa o objeto. Veja [Stubs e código](../avancado/stubs-e-codigo.md).

```bash
kimball set-ddl vendas.dbml stg_vendas off
```

### detach-rel

Recebe o nome da FK ou o número da relação mostrado por `kimball show`. As colunas, o unique e o índice viram objetos comuns, editáveis.

```bash
kimball detach-rel vendas.dbml cliente_fk
```

### add-domain, add-sequence, add-role

Em `--rule`, `VALUE` representa o valor da coluna. A opção pode se repetir.

```bash
kimball add-domain   vendas.dbml cadastro.email --type "varchar(254)" --rule "formato=VALUE LIKE '%@%'"
kimball add-sequence vendas.dbml vendas.seq_pedido --start 1000 --owned-by vendas.pedido.id
kimball add-role     vendas.dbml bi_reader --note "Leitura para o BI"
```

Detalhes em [Domínios e sequences](../avancado/dominios-e-sequences.md) e [Roles e permissões](../avancado/roles-e-permissoes.md).

### grant

Cada texto tem a forma `"privilégios to role"`, com `with grant option` opcional no fim. Também aceita `off` e `revoke`. `--reset` troca as permissões do alvo em vez de somar. Use `schema.*` para o schema inteiro.

```bash
kimball grant vendas.dbml "vendas.*" "select to bi_reader"
kimball grant vendas.dbml pedido "select, insert to etl with grant option"
```

### partition

`--by "range (coluna)"`
:   Tipo e chave: `range`, `list` ou `hash`.

`--monthly AAAA-MM:N`
:   Uma partição range por mês, a partir do mês informado, por N meses.

`--hash N`
:   N partições hash.

`--part "nome: limite"`
:   Uma partição escrita à mão. Pode se repetir.

`--with-default`
:   Acrescenta a partição padrão.

`--off`
:   Tira o particionamento.

```bash
kimball partition vendas.dbml pedido --by "range (emitido_em)" --monthly 2026-01:12 --with-default
kimball partition vendas.dbml pedido --off
```

### apply

Lê uma lista de operações em JSON (o mesmo formato das edições do app) de um arquivo, ou da entrada padrão com `-`. Se uma operação falhar, nenhuma é gravada. `--dry-run` confere sem gravar.

```bash
kimball apply vendas.dbml operacoes.json --dry-run
```

## Gerar e validar

| Comando | O que faz |
|---|---|
| `kimball validate <arquivo.dbml> [--dialect ID] [--target-version V] [--json]` | Erros e avisos para o banco de destino. |
| `kimball export <arquivo.dbml> [--dialect ID] [--target-version V] [-o saida.sql] [--force]` | Script DDL completo. |
| `kimball ddl <modelo.dbml> [opções]` | DDL com opções de divisão em arquivos, DROP e código de objetos. |
| `kimball fmt <arquivo.dbml> [--check \| --write]` | Formata o DBML. |
| `kimball testdata <arquivo.dbml> [--dialect ID] [--rows N] [--seed S] [-o dados.sql]` | INSERTs de dados de teste. |
| `kimball schema <arquivo.dbml> [--xsd] [-o saida]` | JSON Schema, ou XML Schema com `--xsd`. |
| `kimball sheet <arquivo.dbml> [-o dicionario.csv]` | Planilha de tabelas e colunas. |

### validate

Mostra cada problema com a linha do arquivo e um resumo com o total de erros e avisos. `--json` devolve a lista em JSON, para usar em scripts. Sai com `1` se houver erro.

```bash
kimball validate vendas.dbml
kimball validate vendas.dbml --dialect snowflake --json
```

### export

Gera o script de criação inteiro. Se o modelo tiver erros, não gera e sai com `1`. `--force` gera mesmo assim.

```bash
kimball export vendas.dbml -o criar.sql
kimball export vendas.dbml --dialect sqlserver --target-version 2019 -o criar.sql
```

### ddl

Para repositórios de migração. Sem opções, gera um script único, como o `export`.

`--split -o pasta`
:   Um arquivo por objeto, numerados na ordem de execução.

`--group-by-type -o pasta`
:   Um arquivo por tipo de objeto.

`--with-children -o pasta`
:   Um arquivo por tabela, com seus índices.

`--drop-script`
:   Só o script de DROP, dependentes primeiro.

`--drop-first`
:   DROP antes dos CREATE, para recriar o banco.

`--cascade`
:   CASCADE nos DROP, onde o banco aceita.

`--if-not-exists`
:   IF NOT EXISTS em tabelas e índices, onde o banco aceita.

`--one-line-comments`
:   Comentários numa linha só.

`--force`
:   Gera mesmo com erros de validação.

Para o código de alguns objetos, use `--object` (pode repetir) com `schema.tabela`, só `tabela` (quando o nome não se repete em outro schema) ou `enum:nome`, e escolha o alcance:

`--only`
:   Só o objeto.

`--with-children`
:   O objeto com índices e FKs (padrão).

`--with-deps`
:   O objeto e tudo de que ele depende.

```bash
kimball ddl vendas.dbml --split -o migracoes/
kimball ddl vendas.dbml --drop-script -o apagar.sql
kimball ddl vendas.dbml --object vendas.pedido --with-deps --dialect oracle
```

### fmt

Sem opção, mostra o arquivo formatado na tela. `--write` grava no próprio arquivo. `--check` não grava e sai com `1` se o arquivo não estiver formatado.

```bash
kimball fmt vendas.dbml --check
kimball fmt vendas.dbml --write
```

### testdata

`--rows` define as linhas por tabela (padrão 20). `--seed` fixa o gerador (padrão 42), para repetir os mesmos dados.

```bash
kimball testdata vendas.dbml --rows 100 -o dados.sql
```

## Comparar e importar

| Comando | O que faz |
|---|---|
| `kimball diff <antes.dbml> <depois.dbml> [--dialect ID] [--target-version V] [--json]` | Diferenças entre duas versões do modelo, com o plano de ALTER. |
| `kimball alter <banco.sql> <modelo.dbml> [--dialect ID] [--preset desenvolvimento\|homologacao\|producao]` | Script de alteração que leva o banco ao modelo. |
| `kimball import <script.sql...\|modelo.pdm\|planilha.xlsx\|.csv> [--dialect ID] [-o modelo.dbml]` | Engenharia reversa para DBML. |

### diff

Cada passo vem marcado como `Direct` (ALTER direto), `Rebuild` (exige recriar o objeto) ou `Unsupported` (o banco não permite), e `[destrutivo]` quando apaga dados. `--json` devolve os passos em JSON. Sai com `1` se houver diferença.

```bash
kimball diff v1.dbml v2.dbml --dialect postgres
```

### alter

Compara o script do banco atual (por exemplo, a saída de `pg_dump --schema-only`) com o modelo e gera o ALTER. Não executa nada. `--preset` escolhe as regras da comparação: os três padrões ou um preset salvo em `modelo.presets.json`, ao lado do modelo. Sai com `1` se houver alteração. Veja [Comparar e atualizar](../bancos/comparar-e-atualizar.md).

```bash
kimball alter banco.sql vendas.dbml --preset producao -o alteracao.sql
```

### import

Aceita um ou mais scripts SQL, um Modelo Físico XML (.pdm) ou uma planilha (.xlsx, .xls, .ods, .csv). Com `-o`, as posições do diagrama, quando existem no arquivo de origem, vão para `modelo.layout.json` ao lado. O que não foi reconhecido aparece como ignorado. Veja [Importar](../bancos/importar.md).

```bash
kimball import schema.sql --dialect postgres -o vendas.dbml
kimball import tabelas.xlsx -o vendas.dbml
```

## IA

| Comando | O que faz |
|---|---|
| `kimball mcp [arquivo.dbml] [--locked [--read-only]]` | Servidor MCP para a IA da IDE. |
| `kimball mcp-config [arquivo.dbml]` | Mostra a configuração pronta para cada IDE. |
| `kimball skills list` | Lista as skills do Kimball para agentes de IA. |
| `kimball skills install [--global] [--dir pasta]` | Instala as skills num projeto ou para todos. |

`--locked` prende o servidor àquele arquivo e `--read-only` deixa só as ferramentas de leitura. Veja [IA na IDE (MCP)](../mcp/index.md) e [Skills](../mcp/skills.md).

```bash
kimball mcp-config vendas.dbml
kimball skills install --global
```

## Outros

| Comando | O que faz |
|---|---|
| `kimball lsp` | Servidor de linguagem DBML, para editores compatíveis com LSP. |
| `kimball dialects` | Bancos, versões e o que cada um aceita, em JSON. |
| `kimball help` | Lista os comandos. |

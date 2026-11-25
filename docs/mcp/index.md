# IA na IDE (MCP)

O `kimball mcp` é um servidor MCP (Model Context Protocol). Ligado à IA da sua IDE, ele deixa o agente ler, validar e modelar o seu `.dbml` com as mesmas edições do app. Esta página mostra como conectar em cada IDE e como conferir se funcionou.

Você precisa do programa `kimball` instalado. Veja [Linha de comando](../cli/index.md#instalar).

## Pegar a configuração pronta

O jeito mais rápido é copiar a configuração do próprio app, já com o caminho do programa e do modelo aberto:

- ++ctrl+k++ e escolha **Conectar a IA da IDE (MCP)…**, ou
- ++f1++, seção **IA e terminal**, botão **Copiar**.

Pelo terminal, o mesmo texto sai de:

```bash
kimball mcp-config C:\projetos\loja\modelo.dbml
```

O arquivo depois de `mcp` é o modelo padrão. Toda ferramenta aceita também um argumento `file` para trabalhar em outro `.dbml`.

## Conectar em cada IDE

Nos exemplos, o programa está em `C:\Ferramentas\kimball.exe` e o modelo em `C:\projetos\loja\modelo.dbml`. Troque pelos seus caminhos.

=== "Claude Code"

    No terminal:

    ```bash
    claude mcp add kimball -- "C:\Ferramentas\kimball.exe" "mcp" "C:\projetos\loja\modelo.dbml"
    ```

=== "VS Code (Copilot)"

    Crie `.vscode/mcp.json` no projeto e use o Copilot no modo agente:

    ```json
    {
      "servers": {
        "kimball": {
          "type": "stdio",
          "command": "C:\\Ferramentas\\kimball.exe",
          "args": ["mcp", "C:\\projetos\\loja\\modelo.dbml"]
        }
      }
    }
    ```

=== "Cursor"

    Em `.cursor/mcp.json` do projeto:

    ```json
    {
      "mcpServers": {
        "kimball": {
          "command": "C:\\Ferramentas\\kimball.exe",
          "args": ["mcp", "C:\\projetos\\loja\\modelo.dbml"]
        }
      }
    }
    ```

=== "Claude Desktop"

    No arquivo de configuração do Claude Desktop (Configurações → Desenvolvedor → Editar configuração):

    ```json
    {
      "mcpServers": {
        "kimball": {
          "command": "C:\\Ferramentas\\kimball.exe",
          "args": ["mcp", "C:\\projetos\\loja\\modelo.dbml"]
        }
      }
    }
    ```

    Reinicie o Claude Desktop depois de salvar.

=== "Windsurf"

    Na configuração de servidores MCP do Windsurf:

    ```json
    {
      "mcpServers": {
        "kimball": {
          "command": "C:\\Ferramentas\\kimball.exe",
          "args": ["mcp", "C:\\projetos\\loja\\modelo.dbml"]
        }
      }
    }
    ```

=== "Codex"

    Em `~/.codex/config.toml`:

    ```toml
    [mcp_servers.kimball]
    command = 'C:\Ferramentas\kimball.exe'
    args = ['mcp', 'C:\projetos\loja\modelo.dbml']
    ```

O mesmo bloco `mcpServers` serve para um `.mcp.json` na raiz do projeto, que vários agentes leem.

## Conferir se ligou

1. Reinicie a IDE ou recarregue os servidores MCP.
2. Veja se o servidor `kimball` aparece com as ferramentas na lista de MCP da IDE.
3. Peça algo simples, como *"mostre a visão geral do modelo no Kimball"*. A IA deve chamar `model_overview`.

Depois, peça o que quiser, por exemplo: *"modele vendas com clientes, pedidos e itens no Kimball"*.

## O prompt modelar

O servidor oferece o prompt `modelar`, que recebe o assunto e passa à IA as convenções do Kimball: nomes em `snake_case` e em português, `id bigint` como chave, relacionamentos criados com `create_relationship`, descrições nas tabelas, prefixos `dim_` e `fato_` em modelos dimensionais e `validate` no fim. Nas IDEs que mostram prompts MCP, ele aparece na lista de comandos.

## Com o app aberto

O app confere a cada 2 segundos se o arquivo mudou fora dele.

- **Sem alterações não salvas no app:** recarrega sozinho e avisa. ++ctrl+z++ volta ao que estava.
- **Com alterações não salvas:** pergunta se carrega a versão do arquivo ou mantém a sua. O que você tinha continua no desfazer.

A IA nunca executa nada em banco de dados. Executar no servidor continua só no app, com confirmação.

## Limitar o servidor

`--locked`
:   Trabalha só no arquivo informado. Recusa o argumento `file` com outro caminho, não oferece `new_model`, `dbml_write` nem `diff_models` e recusa o `save_as` do `import_sql`. Exige o arquivo.

`--read-only`
:   Usado junto com `--locked`. Oferece e aceita só as ferramentas que não mudam nada.

```bash
kimball mcp C:\projetos\loja\modelo.dbml --locked --read-only
```

A lista do que fica disponível em cada caso está em [Ferramentas](ferramentas.md).

## Problemas comuns

??? question "As ferramentas do kimball não aparecem na IDE"
    Confira se o caminho em `command` aponta para o `kimball.exe` e se ele roda no terminal com `kimball help`. Em JSON, as barras invertidas do Windows precisam ser dobradas (`C:\\Ferramentas\\kimball.exe`). Reinicie a IDE depois de mudar a configuração.

??? question "A IA edita outro arquivo, ou diz que falta o arquivo"
    O arquivo depois de `mcp` é o padrão. Sem ele, cada pedido precisa dizer qual `.dbml` usar. Use caminhos absolutos: a IDE pode iniciar o servidor em outra pasta.

??? question "O app não mostrou a mudança feita pela IA"
    Confira se o app e a IDE apontam para o mesmo arquivo. Se havia alterações não salvas no app, ele pergunta antes de recarregar.

## Veja também

- [Ferramentas](ferramentas.md)
- [Skills](skills.md)

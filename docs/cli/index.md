# Linha de comando (kimball)

O `kimball` é o programa de terminal do Kimball Modeler. Com ele você cria e altera modelos `.dbml`, valida, gera DDL, compara versões e liga o modelo à IA da sua IDE, sem abrir o app. Esta página mostra como instalar e faz um tour rápido.

## O que dá para fazer

- **Modelar pelo terminal:** criar tabelas, colunas, relacionamentos, domínios, sequences, roles e permissões.
- **Gerar e validar:** conferir o modelo contra o banco de destino e gerar o script DDL.
- **Comparar e importar:** diferenças entre duas versões do modelo, script de alteração, importação de SQL, Modelo Físico XML (.pdm) ou planilha.
- **IA na IDE:** abrir o servidor MCP para Claude Code, VS Code, Cursor e outros. Veja [IA na IDE (MCP)](../mcp/index.md).

A lista completa está na [referência de comandos](comandos.md).

!!! note "Nada roda no banco"
    O `kimball` só lê e grava arquivos. Ele nunca se conecta nem executa SQL em um banco de dados. Executar no servidor continua sendo feito pelo app, que mostra o SQL e pede confirmação.

## Instalar

O `kimball` é distribuído na [página de Releases](https://github.com/filipimr/kimball-modeler/releases/latest), separado do instalador do app.

=== "Windows"

    1. Baixe o arquivo `kimball-x86_64-pc-windows-gnu.exe` da Release mais recente.
    2. Renomeie para `kimball.exe` e coloque numa pasta só dele.
    3. Acrescente essa pasta ao PATH do seu usuário.

    ```powershell
    $dir = "$env:LOCALAPPDATA\kimball"
    New-Item -ItemType Directory -Force $dir | Out-Null
    Invoke-WebRequest "https://github.com/filipimr/kimball-modeler/releases/latest/download/kimball-x86_64-pc-windows-gnu.exe" -OutFile "$dir\kimball.exe"
    $path = [Environment]::GetEnvironmentVariable("Path", "User")
    if ($path -notlike "*$dir*") {
      [Environment]::SetEnvironmentVariable("Path", "$path;$dir", "User")
    }
    ```

    Feche e abra o terminal para o PATH novo valer.

=== "Linux e macOS"

    O programa para Linux e macOS entra nas próximas versões. Acompanhe o [roadmap](../referencia/roadmap.md).

Para conferir, rode:

```bash
kimball help
```

A saída lista todos os comandos e opções.

## Primeiro modelo pelo terminal

1. Crie o arquivo com o banco de destino:

    ```bash
    kimball new vendas.dbml --dialect postgres
    ```

2. Adicione tabelas. Cada coluna segue o formato `nome:tipo[:pk][:nn][:uq][:inc]`:

    ```bash
    kimball add-table vendas.dbml cadastro.cliente id:bigint:pk:inc "nome:varchar(200):nn" --note "Clientes"
    kimball add-table vendas.dbml vendas.pedido id:bigint:pk:inc emitido_em:timestamptz:nn
    ```

    Tipos com parênteses vão entre aspas, no PowerShell e no bash.

3. Acrescente uma coluna:

    ```bash
    kimball add-column vendas.dbml pedido "total:decimal(15,2):nn" --note "Valor total"
    ```

4. Relacione as tabelas, do pai para o filho. A coluna `id_cliente` e a FK são criadas no pedido:

    ```bash
    kimball link vendas.dbml cliente pedido
    ```

5. Veja o resultado e valide:

    ```bash
    kimball show vendas.dbml
    kimball validate vendas.dbml
    ```

6. Gere o DDL:

    ```bash
    kimball export vendas.dbml -o criar.sql
    ```

Quando só existe uma tabela com aquele nome, basta o nome (`pedido`), sem o schema.

## CLI e app no mesmo arquivo

A CLI e o app editam o mesmo `.dbml`. Cada comando muda só o trecho afetado: comentários, ordem e formatação do resto do arquivo ficam como estavam. Renomear ou remover uma tabela também atualiza o arquivo de posições do diagrama (`modelo.layout.json`).

Com o modelo aberto no app, ele percebe a mudança feita pelo terminal e recarrega sozinho. Se você tinha alterações não salvas, o app pergunta qual versão manter. Em qualquer caso, ++ctrl+z++ volta ao que estava antes.

## Próximos passos

- [Referência de comandos](comandos.md)
- [Uso em CI/CD](ci.md)
- [IA na IDE (MCP)](../mcp/index.md)

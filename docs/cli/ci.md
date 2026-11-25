# Uso em CI/CD

Com o `kimball` no pipeline, cada pull request confere se o modelo está formatado, se valida no banco de destino e quais mudanças traz. Esta página tem um workflow do GitHub Actions e um script que roda em qualquer CI.

Hoje a Release traz o `kimball` só para Windows, então os exemplos usam um runner Windows e PowerShell. Runners Linux entram quando o pacote existir (veja o [roadmap](../referencia/roadmap.md)).

## O que conferir

| Passo | Comando | Falha quando |
|---|---|---|
| Formatação | `kimball fmt modelo.dbml --check` | o arquivo não está formatado |
| Validação | `kimball validate modelo.dbml --json` | há erro para o banco de destino |
| Mudanças | `kimball diff antes.dbml modelo.dbml` | há diferença (código `1`) |
| DDL | `kimball export modelo.dbml -o criar.sql` | o modelo tem erros |

Os comandos de modelagem não reformatam o arquivo inteiro. Rode `kimball fmt modelo.dbml --write` uma vez antes de ligar o passo de formatação.

O `diff` sai com `1` sempre que encontra diferença. Num pull request, diferença é o esperado: trate esse passo como informativo. Os códigos de saída estão na [referência de comandos](comandos.md#codigos-de-saida).

## GitHub Actions

```yaml title=".github/workflows/modelo.yml"
name: Modelo de dados

on:
  pull_request:
    paths: ["modelo/**"]

jobs:
  kimball:
    runs-on: windows-latest
    defaults:
      run:
        shell: pwsh
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Baixar o kimball
        run: |
          New-Item -ItemType Directory -Force bin | Out-Null
          Invoke-WebRequest "https://github.com/filipimr/kimball-modeler/releases/latest/download/kimball-x86_64-pc-windows-gnu.exe" -OutFile bin/kimball.exe
          "$PWD\bin" | Out-File -Append $env:GITHUB_PATH

      - name: Formatação
        run: kimball fmt modelo/vendas.dbml --check

      - name: Validação
        run: |
          kimball validate modelo/vendas.dbml --json | Out-File validacao.json
          if ($LASTEXITCODE -ne 0) { Get-Content validacao.json; exit 1 }

      - name: Mudanças em relação à main
        run: |
          git show "origin/${{ github.base_ref }}:modelo/vendas.dbml" | Out-File -Encoding utf8 antes.dbml
          kimball diff antes.dbml modelo/vendas.dbml
          if ($LASTEXITCODE -gt 1) { exit $LASTEXITCODE }
          exit 0

      - name: DDL
        run: kimball export modelo/vendas.dbml -o criar.sql

      - uses: actions/upload-artifact@v4
        with:
          name: ddl
          path: criar.sql
```

O passo de mudanças só falha com código `2` (erro de uso ou de leitura). Se o arquivo não existe na branch de destino, por ser novo, remova esse passo ou compare com um arquivo vazio.

Para fixar uma versão do `kimball` em vez de usar sempre a mais recente, troque `latest/download` por `download/<tag>` com a tag da Release.

## Script para qualquer CI

O mesmo fluxo em PowerShell, para Azure Pipelines, GitLab com runner Windows, Jenkins ou uma tarefa local.

```powershell title="conferir-modelo.ps1"
param(
  [string]$Modelo = "modelo/vendas.dbml",
  [string]$Antes = ""
)
$ErrorActionPreference = "Stop"

$bin = Join-Path $PWD "bin"
$kimball = Join-Path $bin "kimball.exe"
if (-not (Test-Path $kimball)) {
  New-Item -ItemType Directory -Force $bin | Out-Null
  Invoke-WebRequest "https://github.com/filipimr/kimball-modeler/releases/latest/download/kimball-x86_64-pc-windows-gnu.exe" -OutFile $kimball
}

& $kimball fmt $Modelo --check
if ($LASTEXITCODE -ne 0) { exit 1 }

& $kimball validate $Modelo --json | Out-File validacao.json
if ($LASTEXITCODE -ne 0) { Get-Content validacao.json; exit 1 }

if ($Antes) {
  & $kimball diff $Antes $Modelo
  if ($LASTEXITCODE -gt 1) { exit $LASTEXITCODE }
}

& $kimball export $Modelo -o criar.sql
if ($LASTEXITCODE -ne 0) { exit 1 }
Write-Host "Modelo conferido. DDL em criar.sql"
```

```powershell
powershell -ExecutionPolicy Bypass -File conferir-modelo.ps1 -Modelo modelo/vendas.dbml -Antes antes.dbml
```

!!! warning "O CI não aplica nada no banco"
    O `kimball` gera SQL, mas não se conecta a nenhum banco. Se o pipeline for aplicar o script, revise antes os passos marcados como `[destrutivo]` na saída do `diff`.

## Variações úteis

- **Outro banco de destino:** acrescente `--dialect sqlserver --target-version 2022` ao `validate` e ao `export`.
- **Repositório de migrações:** `kimball ddl modelo.dbml --split -o migracoes/` gera um arquivo por objeto, na ordem de execução.
- **Script de alteração contra o banco real:** com o DDL atual do banco salvo em arquivo, `kimball alter banco.sql modelo.dbml --preset producao -o alteracao.sql`.

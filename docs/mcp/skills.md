# Skills

Skills são guias que ensinam um agente de IA a usar o Kimball: as convenções de modelagem, os comandos e o servidor MCP. Elas vêm embutidas no programa `kimball` e acompanham a versão dele. Esta página mostra quais existem e como instalar.

## As cinco skills

| Skill | Para quê |
|---|---|
| `kimball` | Visão geral do Kimball Modeler, bancos suportados e qual skill usar. |
| `kimball-modelagem` | Modelar relacional ou dimensional: convenções, tipos, chaves e DBML. Inclui a referência de DBML e o guia dimensional. |
| `kimball-mcp` | Ligar o MCP em cada IDE, conferir se funciona, resolver problemas e escolher a ferramenta. |
| `kimball-cli` | Os comandos do terminal e o uso em CI. |
| `kimball-engenharia-reversa` | Importar, comparar com o banco, gerar alteração, converter para outro banco e exportar. |

## Listar

```bash
kimball skills list
```

Mostra cada skill com a descrição de quando usar.

## Instalar

As skills vão para uma pasta `.claude/skills`, que o Claude Code lê.

No projeto atual (`./.claude/skills`):

```bash
kimball skills install
```

Para todos os projetos do seu usuário (`~/.claude/skills`):

```bash
kimball skills install --global
```

Em outra pasta de projeto:

```powershell
kimball skills install --dir C:\projetos\loja
```

Rodar o `install` de novo atualiza as skills que mudaram. Ele só mexe nas pastas `kimball*`.

## Agentes que não leem pastas de skill

Copilot, Cursor e outros agentes recebem o mesmo conteúdo pelo servidor MCP, como recursos:

```text
kimball://skill/kimball
kimball://skill/kimball-modelagem
kimball://skill/kimball-modelagem/dbml-referencia.md
kimball://skill/kimball-modelagem/dimensional.md
kimball://skill/kimball-mcp
kimball://skill/kimball-cli
kimball://skill/kimball-engenharia-reversa
```

A ferramenta `read_skill` lê os mesmos guias. Veja [Ferramentas](ferramentas.md#skills).

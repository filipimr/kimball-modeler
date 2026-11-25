# Comparar e atualizar o banco

A comparação mostra a diferença entre um banco existente e o seu modelo e gera o script que leva o banco ao modelo. Você escolhe passo a passo o que entra.

![Comparação pintada no canvas, com passos seguros e destrutivos](../assets/tela-diff-e-alter.png#only-dark){ .km-shot }
![Comparação pintada no canvas, com passos seguros e destrutivos](../assets/tela-diff-e-alter-claro.png#only-light){ .km-shot }

## Como comparar

1. Abra a folha **Atualizar o banco**.
2. Escolha o banco atual: o script DDL dele ou, no PostgreSQL, a conexão ([Conectar ao PostgreSQL](conectar-postgresql.md)).
3. Revise os passos. O canvas fica pintado com as diferenças.
4. Salve o script (`alteracao.sql`) ou, com PostgreSQL conectado, **Executar no banco**.

## Cores no canvas

| Cor | Significa |
|---|---|
| Verde | novo no modelo |
| Amarelo | alterado ou recriado |
| Vermelho tracejado | removido |

## Passos seguros e destrutivos

Passos seguros já vêm marcados. Passos destrutivos (apagam ou recriam objetos) vêm desmarcados: marque só o que você quer mesmo. Cada passo tem uma classificação:

`Direct`
:   o banco faz a mudança direto (`ALTER`).

`Rebuild`
:   o objeto precisa ser recriado.

`Unsupported`
:   o banco não consegue fazer essa mudança; resolva à mão.

| Tecla | O que faz |
|---|---|
| ++j++ / ++k++ | próximo / anterior |
| ++enter++ | inclui ou tira o passo |

## Executar no banco

Com PostgreSQL conectado, **Executar no banco** pede uma confirmação que lista os passos destrutivos e roda tudo numa única transação. Se algo falhar, nada muda.

## Presets

Escolha um preset na folha, ou ajuste as opções:

| Preset | O que falta no modelo |
|---|---|
| **desenvolvimento** | é apagado, com CASCADE |
| **homologação** | só colunas e constraints são apagadas |
| **produção** | nada é apagado (bom para modelo parcial contra o banco inteiro) |

Opções: ignorar comentários, ignorar propriedades físicas, o que fazer com o que falta no modelo, DROP em cascata e a versão do banco. **Salvar como…**, **Renomear**, **Excluir** e **Restaurar padrão** cuidam dos seus presets, que ficam em `modelo.presets.json`, ao lado do modelo.

## Pelo terminal

`kimball alter` compara o script do banco com o modelo. Nada é executado.

```bash
kimball alter banco.sql modelo.dbml --dialect postgres --preset desenvolvimento
```

```sql title="alteracao.sql"
-- Script de alteração gerado pelo Kimball Modeler
-- Destino: PostgreSQL 16
-- Leva o banco descrito no script de origem ao modelo atual.
-- Revise antes de rodar: passos [destrutivo] apagam ou recriam objetos.

-- [Direct] alterar tipo de cliente.nome: varchar(100) → varchar(120)
ALTER TABLE cliente ALTER COLUMN nome TYPE varchar(120) USING nome::varchar(120);

-- [Direct] adicionar coluna cliente.email
ALTER TABLE cliente ADD COLUMN email varchar(254);

-- [Direct] [destrutivo] remover coluna cliente.fax
ALTER TABLE cliente DROP COLUMN fax CASCADE;
```

Com `--preset producao`, o `DROP COLUMN` não sai. Para comparar duas versões do modelo, use `kimball diff antes.dbml depois.dbml` (com `--json` para CI). Veja [Comandos](../cli/comandos.md).

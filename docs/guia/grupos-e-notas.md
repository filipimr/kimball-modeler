# Grupos, notas e navegação

Esta página cobre o que ajuda a organizar e percorrer um diagrama grande: grupos, notas, organização automática, zoom e busca.

## Grupos

Selecione uma tabela e aperte ++g++. O grupo aparece como uma área tracejada em volta das tabelas. No DBML é um `TableGroup`:

```dbml
TableGroup vendas [color: #22C55E] {
  vendas.pedido
  vendas.pedido_item
  Note: 'Fluxo de vendas'
}
```

Um grupo também serve de recorte ao [exportar o diagrama](exportar.md).

## Notas

++n++ cria uma nota, um post-it no canvas. No DBML:

```dbml
Note leia_me [headercolor: #EAB308] {
  'Post-it no canvas'
}
```

Para escrever a nota de uma tabela, use **Mais** › nota na barra dela.

## Organizar e ajustar à tela

| Atalho | O que faz |
|---|---|
| ++shift+l++ | organiza o diagrama inteiro |
| ++shift+1++ | ajusta o zoom para caber tudo na tela |

Organizar, mover tabelas e mover notas entram no histórico: ++ctrl+z++ desfaz.

## Zoom

O controle fica no canto inferior esquerdo e vai de 10% a 500%. Também funcionam ++ctrl++ + roda do mouse e ++ctrl+equal++ / ++ctrl+minus++. Segure ++space++ e arraste para mover a vista.

As posições das tabelas ficam num arquivo `.layout.json` ao lado do `.dbml`, nunca no texto do modelo.

## Paleta Ctrl K

++ctrl+k++ acha tabelas, colunas e relações e leva até elas. Também roda comandos, como **Novo domínio**, **Nova sequence** e **Nova role**.

## Dependências

Antes de mudar algo, veja o que quebra. Em Propriedades, aba **Associações** (ou **Mais** › Dependências), escolha a tabela, uma coluna ou um índice:

- quem usa o objeto: FKs, índices, checks, views, colunas propagadas;
- do que ele precisa;
- **Incluir indiretos** segue a cadeia inteira;
- clique duplo leva até o objeto.

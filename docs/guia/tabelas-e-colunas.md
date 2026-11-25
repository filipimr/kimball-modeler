# Tabelas e colunas

Esta página mostra como criar tabelas no canvas, digitar colunas no próprio desenho e ajustar chaves, cores e propriedades. Tudo o que você faz aqui vai para o texto DBML do modelo.

![Área de trabalho no modo Dividido, com o autocompletar de tipo aberto numa coluna](../assets/tela-area-de-trabalho.png#only-dark){ .km-shot }
![Área de trabalho no modo Dividido, com o autocompletar de tipo aberto numa coluna](../assets/tela-area-de-trabalho-claro.png#only-light){ .km-shot }

## Criar uma tabela

1. Aperte ++t++ ou dê um clique duplo num ponto vazio do canvas.
2. Digite o nome da tabela.
3. Com a tabela selecionada, aperte ++enter++ para abrir a primeira coluna.

## Escrever colunas no lugar

Com a tabela selecionada, ++enter++ abre uma linha nova. Escreva o nome, o tipo e, se quiser, `pk`. O tipo é sugerido enquanto você digita.

| Você digita | O modelo grava |
|---|---|
| `telefone varchar 20` | `telefone varchar(20)` |
| `id int pk` | `id integer [pk]` |

++enter++ grava a linha e abre a próxima. Para mudar uma coluna que já existe, dê um clique duplo nela. Domínios também aparecem entre as sugestões de tipo (veja [Domínios e sequences](../avancado/dominios-e-sequences.md)).

## Renomear

Clique duplo no nome da tabela renomeia. **Mais** › renomear, na barra da tabela, faz o mesmo.

## Barra da tabela

A tabela selecionada ganha borda laranja e, logo acima, uma barra:

| Botão | O que faz |
|---|---|
| **cor** | troca a cor do cabeçalho |
| **+ Coluna** | abre uma coluna nova |
| **Índice** | cria um índice |
| **Ver DDL** | mostra o SQL da tabela no banco de destino |
| **Mais** | renomear, nota, propriedades, apagar, Dependências, Ligar à própria tabela |
| **Perguntar** | abre o [assistente](../assistente/index.md) sobre esta tabela |

## Propriedades

**Mais** › propriedades abre uma folha à direita com o que não cabe no desenho: default, check, comentário, opções do banco, índices, [valor da coluna](../avancado/colunas-e-indices.md), [particionamento](../avancado/particionamento.md) e [permissões](../avancado/roles-e-permissoes.md).

### Chave primária e UNIQUE

Na linha de uma PK ou de um UNIQUE, em Propriedades da tabela, há duas opções:

adiável
:   a restrição pode ser conferida só no commit.

nulos iguais
:   dois nulos contam como valores repetidos (NULLS NOT DISTINCT). Existe no PostgreSQL 15 ou mais novo; nas versões anteriores fica fora do SQL, com aviso.

```dbml
Table cadastro.cliente {
  id bigint [pk, increment]
  documento varchar(14) [not null]
  email varchar(254)

  indexes {
    (documento) [unique, name: 'ux_cliente_documento']
    (email) [unique, nulls_not_distinct]
  }
}
```

## Cores

O botão **cor** pinta o cabeçalho. No DBML a cor fica em `headercolor`:

```dbml
Table cadastro.cliente [headercolor: #3B82F6] {
  id bigint [pk]
}
```

## Apagar

++del++ apaga a tabela selecionada na hora e mostra **Desfazer**. O aviso diz o que foi junto (relações, colunas propagadas), e **Desfazer** volta tudo.

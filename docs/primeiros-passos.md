# Primeiros passos

Este roteiro leva uns cinco minutos: você abre o exemplo, cria um modelo do zero, liga duas tabelas, confere o SQL e salva.

## 1. Abra o exemplo Northwind

Sem arquivo aberto, o Kimball mostra a tela inicial. Nela você começa um **modelo em branco** (++ctrl+n++), **importa um banco**, abre um `.dbml` (++ctrl+o++), um arquivo recente ou o **exemplo Northwind**. Arrastar um arquivo para a janela também abre ou importa.

![Tela inicial do Kimball Modeler](assets/tela-inicio.png#only-dark){ .km-shot }
![Tela inicial do Kimball Modeler](assets/tela-inicio-claro.png#only-light){ .km-shot }

O Northwind é um banco de vendas clássico, adaptado para PostgreSQL. Ele serve para explorar o diagrama, o DBML e a exportação sem medo de estragar nada. Na primeira vez que você abre o app, o guia (++f1++) aparece sozinho e tem o botão **Abrir o exemplo agora**.

## 2. Escolha o banco de destino

No topo, a pílula do banco (por exemplo, "PostgreSQL 16") define para qual banco e versão o modelo é validado e o SQL é gerado. A pílula ao lado diz se há problemas para esse banco e abre a lista.

Trocar o banco mostra antes o que muda e o que se perde. Veja [Converter para outro banco](bancos/conversao.md).

## 3. Crie tabelas e colunas

Comece um modelo em branco (++ctrl+n++) e:

1. Aperte ++t++ (ou dê um clique duplo no canvas) para criar uma tabela.
2. Com a tabela selecionada, aperte ++enter++ e digite a coluna no lugar: `telefone varchar 20` grava `telefone varchar(20)`, e `id int pk` grava `id integer [pk]`.
3. ++enter++ grava e já abre a linha seguinte. ++esc++ termina.

Os detalhes estão em [Tabelas e colunas](guia/tabelas-e-colunas.md).

## 4. Ligue duas tabelas

Selecione a tabela pai e arraste a alça laranja até a tabela filha. Outra forma: aperte ++2++ (1:N) e arraste do pai para o filho. A coluna de chave estrangeira nasce sozinha, com o nome no padrão `id_cliente`.

++1++ cria uma relação 1:1, e ++3++ cria uma N:N com a tabela associativa pronta. Veja [Relacionamentos](guia/relacionamentos.md).

## 5. Veja o DBML ao lado do diagrama

O Kimball tem três modos de visualização:

| Modo | Atalho | O que mostra |
|---|---|---|
| Diagrama | ++ctrl+1++ | só o canvas |
| Dividido | ++ctrl+2++ | o canvas e, à direita, o editor DBML |
| DBML | ++ctrl+3++ | só o editor |

++ctrl+backslash++ alterna entre Diagrama e Dividido. No Dividido, arraste a divisória para alargar o editor (ou foque nela e use ++arrow-left++ ++arrow-right++). Um clique duplo na divisória volta à largura de 500 px.

O que você muda no diagrama aparece no texto, e o que você digita no texto aparece no diagrama. Comentários e formatação do resto do arquivo ficam como estavam. Mais em [Editor DBML](guia/editor-dbml.md).

## 6. Confira o SQL

Selecione uma tabela e clique em **Ver DDL** na barra acima dela. Para o modelo inteiro, abra **Exportar** (++ctrl+e++) › **Script DDL**. A página [Exportação](guia/exportar.md) lista todos os formatos.

## 7. Salve

++ctrl+s++ grava dois arquivos lado a lado:

`modelo.dbml`
:   O modelo em texto: tabelas, colunas, relações e notas. É o arquivo que vai para o Git e para o code review.

`modelo.layout.json`
:   As posições das tabelas no diagrama. Versione junto, para o time ver o mesmo desenho.

++ctrl+shift+s++ salva com outro nome.

## O que protege o seu trabalho

- **Desfazer:** até 500 passos com ++ctrl+z++, incluindo mover tabelas e organizar o diagrama. ++ctrl+shift+z++ ou ++ctrl+y++ refazem. O editor DBML usa o mesmo histórico.
- **Salvamento automático:** um modelo que já foi salvo é gravado a cada 10 minutos.
- **Recuperação:** uma cópia de recuperação é feita a cada 5 minutos. Se o app fechar sem salvar, ele oferece recuperar ao abrir.

## Próximos passos

- [Assistente de IA](assistente/index.md): peça uma revisão do modelo.
- [Importar um banco](bancos/importar.md): comece de um banco que já existe.
- [Atalhos de teclado](referencia/atalhos.md): a lista completa.

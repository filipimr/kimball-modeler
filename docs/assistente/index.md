# Assistente de IA

O assistente responde perguntas sobre o seu modelo como um DBA sênior: explica tabelas, encontra a causa de um erro, revisa chaves, tipos, índices e desempenho para o banco e a versão de destino, e avisa quando uma mudança exige recriar a tabela ou pode perder dados. Ele não grava sozinho no modelo. O que ele sugere vira uma [proposta](propostas.md), que você aplica ou descarta.

!!! note "Antes de começar"
    O Kimball não inclui assinatura de IA. Conecte um provedor em **Configurações → Assistente** (veja [Provedores](provedores.md)). O uso é cobrado pelo provedor que você escolher.

## Como abrir

| Onde | Como |
|---|---|
| Em qualquer lugar | ++ctrl+j++, também com o foco no editor DBML |
| Canvas | botão flutuante no canto (dá para escondê-lo em Configurações) |
| Tabela selecionada | **Perguntar** na barra da tabela, ou o símbolo do assistente no cabeçalho dela |
| Folha Problemas | **Corrigir com o assistente** num item |
| Editor DBML | **Explicar** sobre um trecho selecionado |
| Tela inicial | **Começar de um texto** |

## Perguntas prontas

Ao perguntar sobre uma tabela, o popover oferece atalhos:

- **Explicar a tabela**
- **Impacto de mudar**
- **Gerar descrições**
- **Sugerir dimensão**
- **Revisar o grão**

## Teclas

| Tecla | O que faz |
|---|---|
| `@` | cita uma tabela na pergunta |
| ++enter++ | envia |
| ++ctrl+m++ | troca o modelo de IA (a lista vem do próprio provedor) |
| ++esc++ | fecha a pergunta, cancela a resposta em andamento e depois fecha o painel |

## Da pergunta à mudança

1. Você pergunta (ou usa uma pergunta pronta).
2. O assistente consulta o modelo e responde. Com provedores que aceitam ferramentas, ele investiga antes (validação, tabelas, dependências, o SQL do banco), e o painel mostra "Consultando…" a cada consulta.
3. Se a resposta inclui uma mudança, ela aparece tracejada em laranja no canvas.
4. Você clica em **Aplicar** (++ctrl+enter++) ou **Descartar**. Aplicar grava tudo num passo de desfazer.

A única exceção é o nível **Editar o modelo** do Claude Code: as mudanças entram sozinhas no fim da execução, também num passo só (++ctrl+z++ desfaz). Veja [Provedores](provedores.md#claude-code-pela-assinatura).

## Nesta seção

- [Provedores](provedores.md): quem responde e como conectar.
- [Propostas](propostas.md): como revisar e aplicar o que o assistente sugere.
- [Privacidade](privacidade.md): o que sai do seu computador.

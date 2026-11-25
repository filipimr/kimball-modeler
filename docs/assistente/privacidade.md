# Privacidade

Esta página diz o que o assistente envia ao provedor de IA e o que fica no seu computador.

## O que é enviado

- A sua pergunta.
- A **estrutura** do modelo: a tabela em questão, as ligadas a ela e um resumo do resto.
- As regras do projeto (abordagem, idioma e instruções), se houver.

**Nunca** são enviados dados das tabelas. O assistente trabalha com o modelo, não com o conteúdo do banco.

## O que fica no seu computador

| Item | Onde fica |
|---|---|
| Chaves de API e token do Claude Code | cofre do sistema operacional, nunca no arquivo do projeto |
| Conversas | só neste computador |
| Regras do projeto | `<arquivo>.ai.json`, ao lado do `.dbml`, sem segredos |

As chamadas ao provedor saem do programa, nunca da página da interface. A tela mostra só os 4 últimos caracteres da chave.

## Sem sair do computador

Com o **Ollama**, o modelo de IA roda localmente (`http://localhost:11434`) e a pergunta não vai para a internet. Veja [Provedores](provedores.md).

## Claude Code

O Claude Code trabalha numa cópia do modelo aberto: não tem terminal, não lê nem grava outros arquivos e não acessa a web. No nível **Somente leitura** ele só consulta e responde. Veja [Provedores](provedores.md#claude-code-pela-assinatura).

## Consultas do assistente

As consultas que o assistente faz para investigar são só de leitura e rodam numa cópia do modelo. Nenhuma delas executa nada em banco de dados.

# Provedores

O assistente usa o provedor de IA que você escolher. O Kimball não inclui assinatura de IA: o uso é cobrado pelo provedor. A exceção é o Claude Code pela assinatura, que não cobra por uso.

## Quem pode responder

| Provedor | Como conecta | Observação |
|---|---|---|
| Anthropic | chave de API | uso cobrado pelo provedor |
| OpenAI | chave de API | uso cobrado pelo provedor |
| Google Gemini | chave de API | uso cobrado pelo provedor |
| Ollama | endereço local (`http://localhost:11434`) | o modelo roda no seu computador |
| Compatível com OpenAI | endereço e chave opcional | Azure OpenAI, OpenRouter, vLLM ou servidor próprio |
| Claude Code (assinatura) | login da conta Claude.ai | assinatura Pro ou Max, sem chave de API |

## Conectar em três passos

1. Em **Configurações → Assistente**, escolha o provedor em **Quem responde** e gere a chave no site dele (no Ollama e nos compatíveis, informe o endereço).
2. Cole a chave e clique em **Testar**. Se der certo, a tela mostra "Funcionou em N ms".
3. Clique em **Salvar**.

A chave fica no cofre do sistema, nunca no arquivo do projeto. A tela mostra só os 4 últimos caracteres.

## Claude Code pela assinatura

Se você assina o Claude Pro ou Max e tem o Claude Code instalado, o Kimball pode usá-lo sem chave de API. Em **Configurações → Assistente → Claude Code (assinatura)**:

1. **Conectar com conta Claude.ai** abre o login oficial no terminal (`claude auth login`). Entre pelo navegador e clique em **Verificar**. Outra opção: **Gerar token** roda `claude setup-token`; cole o token no campo e clique em **Guardar** (ele vai para o cofre do sistema). **Apagar token** remove.
2. Escolha o que o agente pode fazer:

    **Somente leitura**
    :   consulta o modelo (tabelas, validação, SQL) e responde.

    **Propor mudanças** (padrão)
    :   altera uma cópia do modelo; o resultado aparece tracejado esperando **Aplicar**.

    **Editar o modelo**
    :   as mudanças entram sozinhas no fim, num passo só (++ctrl+z++ desfaz tudo).

3. No assistente, ++ctrl+m++ → **Claude Code (assinatura)** e um dos modelos (Sonnet, Opus, Haiku).

Limites, em qualquer nível:

- trabalha só no modelo aberto, por uma cópia;
- não tem terminal, não lê nem grava outros arquivos e não acessa a web;
- o painel mostra a ferramenta em uso, e ++esc++ interrompe na hora;
- se você mexer no modelo durante a execução, o resultado não entra sozinho;
- execuções com mais de 5 minutos são interrompidas.

## Configurações → Assistente

**Quem responde**
:   o provedor e o modelo usados.

**Neste projeto**
:   **Abordagem** (Não definida, Relacional, Dimensional ou Data Vault), **Idioma das respostas** e **Instruções do projeto** (convenções, nomes). Ficam em `<arquivo>.ai.json`, ao lado do `.dbml`, sem segredos. Exige o modelo salvo.

**Neste computador**
:   a opção de mostrar o botão do assistente no canvas. ++ctrl+j++ abre o assistente mesmo com o botão escondido.

Veja também [Privacidade](privacidade.md).

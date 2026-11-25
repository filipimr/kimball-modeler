# Propostas

Quando o assistente sugere uma mudança, ela não vai direto para o modelo. Ela vira uma proposta: conferida numa cópia do modelo, desenhada no canvas e esperando a sua decisão.

## Como uma proposta aparece

- Tabelas, colunas e relações novas ou alteradas ficam **tracejadas em laranja** no canvas.
- O topo mostra o aviso "Prévia do assistente" com o número de mudanças.
- No painel do assistente, o cartão "Proposta · N mudanças" lista o que muda, com **Descartar**, **Aplicar** e **ver no DBML**.

## Aplicar, descartar, desfazer

| Ação | Como | Resultado |
|---|---|---|
| **Aplicar** | botão ou ++ctrl+enter++ | grava tudo no DBML num único passo de desfazer; o cartão passa a "Aplicada · N mudanças", "Gravada no DBML" |
| **Descartar** | botão | a proposta some do canvas e o modelo não muda |
| **Desfazer** | botão no cartão ou ++ctrl+z++ | volta ao modelo de antes da proposta |

## Proposta desatualizada

Se você mexer no modelo antes de aplicar, a proposta é conferida de novo. Quando ela não cabe mais no modelo atual, fica marcada como **Desatualizada**. Peça de novo ao assistente.

## Propostas inválidas não aparecem

Toda proposta passa pela validação do modelo antes de ser mostrada. Uma mudança que não passa não é desenhada, então uma proposta nunca deixa o modelo quebrado.

## Consultas de leitura (modo DBA)

Com Anthropic, OpenAI, Gemini e os modelos do Ollama que aceitam ferramentas, o assistente investiga antes de propor: lê a validação, as tabelas, as dependências, o SQL do banco, o que o banco aceita naquela versão, o diagrama e os guias do Kimball. São até 8 consultas por pergunta, só de leitura e numa cópia. O painel mostra "Consultando…" a cada uma.

## Exceção: Claude Code em Editar o modelo

No nível **Editar o modelo** do Claude Code, as mudanças entram sozinhas no fim da execução, sem esperar **Aplicar**, mas ainda num passo só: ++ctrl+z++ desfaz tudo. Veja [Provedores](provedores.md#claude-code-pela-assinatura).

# Configurações

Abra com ++ctrl+comma++. As configurações ocupam a tela inteira, com as seções à esquerda e uma busca no topo. ++esc++ volta ao modelo.

![Tela de configurações do Kimball Modeler](../assets/tela-configuracoes.png#only-dark){ .km-shot }
![Tela de configurações do Kimball Modeler](../assets/tela-configuracoes-claro.png#only-light){ .km-shot }

## Geral

Como o Kimball guarda o seu trabalho.

### Validação

**Validar antes de salvar, exportar e comparar**
:   Ligado por padrão. Ao salvar, o app avisa dos erros. Exportar o DDL e comparar com o banco esperam a correção. Veja [Validação e correções](../guia/validacao.md).

### Privacidade e telemetria

**Enviar estatísticas anônimas de uso**
:   Ligado por padrão. Envia só contagens de uso (versão, sistema, visualização e folha abertas, banco escolhido), com um identificador aleatório. Nunca envia nomes de tabelas ou colunas, DBML, SQL, conversas com o Assistente ou senhas, e o IP não é guardado. Em ambientes corporativos, `KIMBALL_TELEMETRY=0` desliga para todos. Veja a [Privacidade](/privacidade.html).

### Salvamento e segurança

Este quadro é informativo:

| Item | Como funciona |
|---|---|
| Salvamento automático | a cada 10 min, no próprio arquivo (só para modelo já salvo) |
| Cópia de recuperação | a cada 5 min, oferecida na tela inicial se o app fechar sem salvar |
| Desfazer | até 500 passos (++ctrl+z++) |
| Senhas de banco | só no cofre do sistema |
| Arquivos | `.dbml` (o modelo) e `.layout.json` (posições) |

## Aparência

**Tema**
:   Escuro (preto neutro) ou claro (branco). Troca na hora e fica guardado neste computador.

## Relacionamentos

Os padrões servem para a maioria dos modelos. Mude só se o seu time usa outra convenção.

**Como a relação aparece**
:   **Pé de galinha** (padrão) ou **Linha simples**. Vale para todo o diagrama.

**Destacar as relações da tabela selecionada**
:   Acende as linhas ligadas à tabela que você selecionar.

**Coluna FK**
:   O nome da coluna criada ao ligar duas tabelas. Usa os marcadores `{coluna_pk}`, `{tabela_pai}` e `{tabela}`, com prévia ao lado. O padrão gera nomes como `id_cliente`.

**Nome da constraint FK**
:   O padrão de nome da restrição de chave estrangeira, em **Mais padrões de nome e regras da FK**.

**Ações das FKs novas**
:   **Ao apagar o pai**: RESTRICT quando a FK é obrigatória e SET NULL quando é opcional. **Ao mudar a chave do pai**: CASCADE. No arquivo, o bloco `Project` pode fixar essas ações com `fk_on_delete` e `fk_on_update`, e o que está no arquivo vence.

**Índice nas colunas da FK**
:   Se cada relação nova já cria um índice nas colunas da FK, e com qual método. Padrão: **Nenhum**. No arquivo, `fk_index` no bloco `Project` vence.

Essas escolhas valem para as próximas relações. As que já existem não mudam.

## Dialetos

A lista dos bancos para os quais o Kimball valida e gera SQL. O destino do modelo é trocado na pílula do banco, no topo. Veja [Bancos de dados](../bancos/index.md).

## Assistente

Quem responde às perguntas sobre o modelo: provedores, chaves, Claude Code pela assinatura, instruções do projeto e o botão do assistente no canvas. A página [Provedores e modelos](../assistente/provedores.md) explica cada opção.

## Atalhos

A lista de atalhos do app. A versão completa está em [Atalhos de teclado](atalhos.md).

## Configurações dentro do arquivo

Algumas escolhas ficam no próprio modelo, no bloco `Project` do DBML, e valem para quem abrir o arquivo:

```dbml
Project loja {
  database_type: 'PostgreSQL'
  target_version: '16'
  fk_on_delete: 'restrict'
  fk_on_update: 'cascade'
  fk_index: 'btree'
}
```

`database_type` e `target_version` são gravados pela pílula do banco. A referência completa está em [DBML no Kimball](dbml.md).

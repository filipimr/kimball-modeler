# FAQ e solução de problemas

## Perguntas frequentes

??? question "Preciso saber DBML?"
    Não. Tudo dá para fazer no diagrama, e o DBML é escrito para você. Quem prefere código edita o texto direto. Veja [DBML no Kimball](dbml.md).

??? question "Com quais bancos funciona?"
    O Kimball valida e gera DDL para PostgreSQL, SQL Server, Snowflake, MySQL e Oracle. A conexão direta com o banco, por enquanto, é só com PostgreSQL. Veja [Bancos de dados](../bancos/index.md).

??? question "Onde o modelo fica salvo?"
    No seu computador, em dois arquivos: o `.dbml` com o modelo e o `.layout.json` com as posições do diagrama. Versione os dois no Git.

??? question "A IA vê os meus dados?"
    Não. O assistente recebe só a estrutura do modelo e a sua pergunta. Veja [Privacidade e segurança](../assistente/privacidade.md).

??? question "O script de alteração pode apagar dados?"
    Só se você marcar. Passos destrutivos chegam desmarcados, e rodar no banco pede confirmação. Veja [Comparar e atualizar o banco](../bancos/comparar-e-atualizar.md).

## Solução de problemas

??? failure "O Windows bloqueou o instalador"
    O SmartScreen avisa porque o instalador ainda não tem assinatura de código. Clique em **Mais informações** › **Executar assim mesmo**. Veja [Instalação](../instalacao.md).

??? failure "A exportação está bloqueada"
    Há erro de validação para o banco de destino. Abra a pílula de problemas no topo e corrija, ou use **Aplicar correção**. Veja [Validação e correções](../guia/validacao.md).

??? failure "O assistente não responde"
    Confira o provedor em **Configurações › Assistente** e use **Testar**. Com Ollama, o servidor precisa estar rodando em `localhost:11434`. Veja [Provedores e modelos](../assistente/provedores.md).

??? failure "A IA da IDE não vê o Kimball"
    Confira o caminho do `kimball` e do modelo na configuração e reinicie a IDE. Veja [IA na IDE (MCP)](../mcp/index.md).

??? failure "O comando kimball não é reconhecido"
    A pasta do `kimball.exe` não está no PATH. Veja [Linha de comando](../cli/index.md).

??? failure "O app fechou sem salvar"
    Abra o Kimball de novo: a tela inicial oferece recuperar a cópia feita a cada 5 minutos.

## Ainda com problema?

[Abra uma issue no GitHub](https://github.com/filipimr/kimball-modeler/issues) com o passo a passo e, se puder, um trecho do modelo. Para outros assuntos: [filipimr@outlook.com](mailto:filipimr@outlook.com).

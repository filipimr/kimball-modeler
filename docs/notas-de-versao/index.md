# Notas de versão

Tudo o que mudou em cada versão do Kimball Modeler: recursos novos, correções e o que fazer para atualizar. Os instaladores de cada versão ficam nas [Releases do GitHub](https://github.com/filipimr/kimball-modeler/releases).

| Versão | Data | Tipo | Resumo |
| --- | --- | --- | --- |
| [0.2.1](0.2.1.md) | 28/11/2026 | Correção | Script de alteração sem passos falsos e em transação; DDL válido em SQL Server, MySQL, Oracle e Snowflake; erros antes do banco |
| [0.2.0](0.2.0.md) | 26/11/2026 | Menor | Telemetria anônima de uso (pode ser desligada); Exportar no modo DBML e com o Assistente aberto; botão do Assistente |
| [0.1.1](0.1.1.md) | 25/11/2026 | Correção | MySQL e Oracle em Exportar, seletor de modelo do assistente e "Neste projeto" |
| [0.1.0](0.1.0.md) | 25/11/2026 | Inicial | Primeira versão pública |

## Como as versões são numeradas

O Kimball segue o [versionamento semântico](https://semver.org/lang/pt-BR/) (`MAIOR.MENOR.CORREÇÃO`):

- **Correção** (0.1.**1**): só conserta problemas. Atualize sem medo; seus arquivos continuam iguais.
- **Menor** (0.**2**.0): traz recursos novos sem quebrar o que já existe.
- **Maior** (**1**.0.0): pode mudar formatos ou comportamentos. As notas sempre explicam o que fazer.

Uma versão publicada nunca é substituída: cada correção sai como uma versão nova.

## Como atualizar

O app confere se há versão nova ao abrir e avisa. A atualização é baixada pela internet com HTTPS, e o app confere a assinatura antes de instalar. Detalhes por sistema em [Instalação e atualizações](../instalacao.md).

Achou um problema? Abra uma [Issue](https://github.com/filipimr/kimball-modeler/issues) com a versão (Configurações › Sobre) e os passos para repetir.

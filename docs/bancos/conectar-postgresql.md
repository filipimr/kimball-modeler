# Conectar ao PostgreSQL

O PostgreSQL é o único banco com conexão direta. Com ela você importa um banco sem script e compara o modelo com o banco real, podendo executar a atualização. Nos outros bancos, use o script DDL ([Importar](importar.md)).

## Campos

| Campo | O que informar |
|---|---|
| Servidor | nome ou IP do servidor |
| Porta | normalmente `5432` |
| Banco | nome do banco |
| Usuário | usuário com permissão de leitura do catálogo |
| Senha | pode ser guardada no cofre do sistema |
| Exigir SSL | marque se o servidor exige conexão segura |

## Onde a senha fica

Se você escolher guardar a senha, ela vai para o cofre do sistema operacional. Ela nunca vai para o arquivo do projeto.

## SSL

A conexão usa SSL quando o servidor oferece. Marque **Exigir SSL** se o servidor pedir conexão segura.

## O que é lido

O Kimball lê a estrutura do banco: tabelas, colunas, chaves, índices, enums e comentários. Os dados das tabelas não são lidos.

## Onde usar a conexão

- **Importar → Banco PostgreSQL conectado:** abre o banco como um modelo novo.
- **Exportar → Comparar com banco PostgreSQL conectado:** mostra cada diferença entre o banco e o modelo e permite executar a atualização. Veja [Comparar e atualizar](comparar-e-atualizar.md).

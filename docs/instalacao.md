# Instalação e atualizações

O Kimball Modeler é um app desktop. Tudo roda no seu computador, e os modelos ficam nos arquivos que você salva.

[Baixar a versão mais recente](https://github.com/filipimr/kimball-modeler/releases/latest){ .md-button .md-button--primary }

## O que baixar

| Sistema | Arquivo | Situação |
|---|---|---|
| Windows 10 e 11 (64 bits) | `Kimball.Modeler_<versão>_x64-setup.exe` (instalador) ou `Kimball.Modeler_<versão>_x64_en-US.msi` | disponível |
| Linux | `.AppImage`, `.deb` ou `.rpm` | nas próximas versões |
| macOS | `.dmg` | nas próximas versões |

Na mesma página fica o programa de linha de comando `kimball`, que roda a CLI e o servidor MCP. No Windows ele é o arquivo `kimball-x86_64-pc-windows-gnu.exe`. A instalação dele está em [Linha de comando](cli/index.md).

## Windows

1. Baixe o `x64-setup.exe` (ou o `.msi`, se a sua empresa instala programas por política).
2. Abra o arquivo e siga o instalador.
3. Abra o **Kimball Modeler** pelo menu Iniciar.

!!! note "Aviso do SmartScreen"
    O instalador ainda não tem assinatura de código do Windows, então o SmartScreen pode mostrar o aviso "O Windows protegeu o computador". Clique em **Mais informações** e depois em **Executar assim mesmo**. As atualizações automáticas têm outra proteção, descrita abaixo.

## Linux e macOS

Os pacotes para Linux e macOS entram na página de downloads nas próximas versões. No Linux, o app vai precisar do WebKitGTK 4.1, presente no Ubuntu 22.04 ou mais novo, Debian 12 ou mais novo, Fedora 38 ou mais novo e Arch Linux. Os arquivos `.dbml` e `.layout.json` abrem iguais em qualquer sistema.

## Atualizações automáticas

Ao abrir, o Kimball confere em segundo plano se há versão nova. Isso não atrasa a abertura e, sem internet, não mostra erro.

Quando há versão nova, aparece a janela **Nova versão disponível**, com a versão atual, a nova e as notas da versão:

- **Atualizar** baixa a versão, mostra o progresso, instala e reinicia o app.
- **Depois** fecha a janela. Ela volta na próxima vez que você abrir o app.

Cada atualização é assinada digitalmente. O app confere a assinatura antes de instalar e recusa um arquivo que não bata com a chave do Kimball. Se a atualização automática falhar, a janela oferece **Abrir página da Release** para você baixar o instalador na mão.

!!! tip "Pacotes .deb e .rpm"
    Quando os pacotes de Linux estiverem disponíveis, quem instalar por `.deb` ou `.rpm` não terá atualização interna: o app avisa que há versão nova e abre a página da Release. O `.AppImage` se atualiza sozinho, como no Windows.

## Desinstalar

No Windows, use **Configurações › Aplicativos › Aplicativos instalados › Kimball Modeler › Desinstalar**. Os seus arquivos `.dbml` e `.layout.json` não são apagados.

## Próximo passo

Abra o app e siga os [primeiros passos](primeiros-passos.md).

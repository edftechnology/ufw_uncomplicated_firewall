# Como instalar/configurar/usar o `UFW (Uncomplicated Firewall)` no `Linux Ubuntu`

## Resumo

Este documento apresenta como instalar pelo `apt`, configurar e verificar o `UFW (Uncomplicated Firewall)` no `Linux Ubuntu`.

## _Abstract_

_This document explains how to install `UFW (Uncomplicated Firewall)` with `apt`, configure it, and check its status on `Linux Ubuntu`._


## Descrição

O `UFW (Uncomplicated Firewall)` é a ferramenta padrão de configuração de _firewall_ do `Linux Ubuntu`. Ele fornece uma interface simples para administrar regras de filtragem de tráfego de rede no sistema `netfilter` do núcleo do `Linux`. O pacote pode ser instalado pelo `apt`; após a instalação, o _firewall_ permanece desativado até ser habilitado.


## 1. Configurar/instalar/usar o `UFW (Uncomplicated Firewall)` no `Linux Ubuntu` [1][2]

O `UFW` é a ferramenta padrão de configuração de _firewall_ do `Linux Ubuntu`. O pacote será instalado pelo `apt`; antes de habilitar o _firewall_, revisar as regras necessárias para os serviços usados neste computador.

1. Abrir o `Terminal Emulator`. Você pode fazer isso pressionando:

    ```bash
    Ctrl + Alt + T
    ```


2. Certifique-se de que seu sistema esteja limpo e atualizado.

    2.1 Limpar o `cache` do gerenciador de pacotes `apt`. Especificamente, ele remove todos os arquivos de pacotes (`.deb`) baixados pelo `apt` e armazenados em `/var/cache/apt/archives/`. Digite o seguinte comando:
    ```bash
    sudo apt clean
    ```

    2.2 Remover pacotes `.deb` antigos ou duplicados do `cache` local. É útil para liberar espaço, pois remove apenas os pacotes que não podem mais ser baixados (ou seja, versões antigas de pacotes que foram atualizados). Digite o seguinte comando:
    ```bash
    sudo apt autoclean
    ```

    2.3 Remover pacotes que foram automaticamente instalados para satisfazer as dependências de outros pacotes e que não são mais necessários. Digite o seguinte comando:
    ```bash
    sudo apt autoremove -y
    ```

    2.4 Buscar as atualizações disponíveis para os pacotes que estão instalados em seu sistema. Digite o seguinte comando e pressione `Enter`:
    ```bash
    sudo apt update
    ```

    2.5 **Corrigir pacotes quebrados**: Isso atualizará a lista de pacotes disponíveis e tentará corrigir pacotes quebrados ou com dependências ausentes:
    ```bash
    sudo apt --fix-broken install
    ```

    2.6 Limpar o `cache` do gerenciador de pacotes `apt` novamente:
    ```bash
    sudo apt clean
    ```

    2.7 Para ver a lista de pacotes a serem atualizados, digite o seguinte comando e pressione `Enter`:
    ```bash
    sudo apt list --upgradable
    ```

    2.8 Realmente atualizar os pacotes instalados para as suas versões mais recentes, com base na última vez que você executou `sudo apt update`. Digite o seguinte comando e pressione `Enter`:
    ```bash
    sudo apt full-upgrade -y
    ```

3. Instalar o `UFW` pelo `apt`:

    ```bash
    sudo apt install ufw -y
    ```

4. Se estiver conectado ao computador por `SSH`, permitir a porta usada pela conexão antes de habilitar o _firewall_. Para a porta padrão `22`:

    ```bash
    sudo ufw allow 22/tcp
    ```

5. Habilitar o `UFW`:

    ```bash
    sudo ufw enable
    ```

6. Verificar o status do `UFW`:

    ```bash
    sudo ufw status verbose
    ```

Para permitir outros serviços, adicionar as regras correspondentes antes de habilitar o `UFW`. Consulte a documentação oficial para exemplos e detalhes.


## 1.1 Código completo para configurar/instalar/usar

Para instalar/configurar/usar o `UFW` no `Linux Ubuntu` em um único bloco de comandos, seguir estas etapas:

1. Abrir o `Terminal Emulator`. Você pode fazer isso pressionando:

    ```bash
    Ctrl + Alt + T
    ```

2. Digitar os comandos abaixo e pressionar `Enter`:

    ```bash
    sudo apt clean
    sudo apt autoclean
    sudo apt autoremove -y
    sudo apt update
    sudo apt --fix-broken install
    sudo apt clean
    sudo apt list --upgradable
    sudo apt full-upgrade -y
    sudo apt install ufw -y
    if [ -n "${SSH_CONNECTION:-}" ]; then sudo ufw allow 22/tcp; fi
    sudo ufw enable
    sudo ufw status verbose
    ```

Ao acessar o computador por `SSH`, conferir a porta usada pela conexão e ajustar a regra `22/tcp` no bloco se necessário. Adicionar também as regras para outros serviços antes de habilitar o `UFW`.


## Referências

[1] OPENAI.
**Instalar o `UFW (Uncomplicated Firewall)` no `linux ubuntu` pelo `terminal emulator`**.
Disponível em: <https://chatgpt.com/g/g-p-6980caf949648191ad6acfcdbe590f9e-instalar/c/ccab59aa-3e70-465e-906d-59d7038ea632>.
ChatGPT.
Acessado em: 18/10/2026.

[2] CANONICAL.
**Configurar o `ufw` no `linux ubuntu`**.
Disponível em: <https://ubuntu.com/server/docs/how-to/security/firewalls/>.
Documentação do Ubuntu Server.
Acessado em: 18/10/2026.


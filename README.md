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

4. Se estiver conectado ao computador por `SSH`, configurar primeiro uma regra restrita conforme a seção 2. Não liberar a porta `22` para qualquer origem sem necessidade.

5. Habilitar o `UFW`:

    ```bash
    sudo ufw enable
    ```

6. Verificar o status do `UFW`:

    ```bash
    sudo ufw status verbose
    ```

Para permitir outros serviços, adicionar somente as regras necessárias antes de habilitar o `UFW`.


## 1.1 Código completo para configurar/instalar/usar

Este bloco pressupõe que os comandos sejam executados no próprio computador, não em uma sessão remota por `SSH`. Para uma sessão remota, primeiro configurar e testar a regra restrita adequada conforme a seção 2.

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
    sudo ufw logging low
    sudo ufw enable
    sudo ufw status verbose
    ```


## 2. Recomendações adicionais de segurança para `Gufw` e `UFW`

As recomendações a seguir partem da configuração descrita: _firewall_ ativo, entrada bloqueada e saída permitida. Manter essas políticas. O perfil `Home` identifica um perfil de configuração; por si só, não limita o acesso à rede doméstica.

### 2.1 Conferir políticas, regras e serviços

1. Conferir as políticas padrão e as regras atuais:

    ```bash
    sudo ufw status verbose
    sudo ufw status numbered
    ```

2. Conferir quais portas e endereços estão em escuta:

    ```bash
    sudo ss -tulpen
    ```

A saída de `ss` identifica serviços em escuta, mas não determina sozinha se eles podem ser acessados de outra máquina. Considerar também as regras do `UFW`, as interfaces de rede e o _roteador_.

As políticas `deny (incoming)` e `allow (outgoing)` já são adequadas para um computador de uso geral. Se este computador não encaminhar tráfego nem funcionar como _roteador_, configurar também a política de tráfego roteado:

```bash
sudo ufw default deny routed
```

Não aplicar essa política sem avaliar o uso de encaminhamento, como um _exit node_ ou _subnet router_ do `Tailscale`.

### 2.2 Restringir as regras de `SSH`

Uma regra `OpenSSH ALLOW IN Anywhere` permite conexões IPv4 de qualquer origem; a regra equivalente `(v6)` permite conexões IPv6 de qualquer origem. Se o acesso por `SSH` for necessário, escolher a origem permitida de acordo com o uso:

- Para acesso somente pela rede local, substituir `192.168.1.0/24` abaixo pelo prefixo real da rede local:

    ```bash
    sudo ufw allow from 192.168.1.0/24 to any port 22 proto tcp
    ```

- Para acesso somente pela interface do `Tailscale`:

    ```bash
    sudo ufw allow in on tailscale0 to any port 22 proto tcp
    ```

Ajustar `22` se o serviço `SSH` usar outra porta. As regras IPv4 e IPv6 são distintas; conferir ambas em `sudo ufw status numbered` e manter somente as origens necessárias em cada protocolo.

Para substituir as regras genéricas com segurança:

1. Adicionar a regra restrita escolhida.
2. Manter aberta a sessão remota atual e testar uma nova conexão pelo caminho permitido.
3. Remover as regras `OpenSSH ALLOW IN Anywhere` e `OpenSSH (v6) ALLOW IN Anywhere (v6)` somente depois do teste. Consultar `sudo ufw status numbered` e apagar cada regra pelo número com `sudo ufw delete <número_da_regra>`; consultar a lista novamente depois de cada remoção, pois os números mudam.
4. Abrir outra conexão pelo caminho permitido e confirmar que funciona antes de encerrar a sessão original.

Se o acesso remoto for feito pela internet pública, planejar uma regra que limite as origens permitidas e considerar também as regras do `roteador`. Não apagar a regra atual antes de confirmar um novo caminho de acesso.

### 2.3 Ativar registros do _firewall_

Ativar registros em nível baixo para facilitar a análise sem solicitar o nível mais detalhado:

```bash
sudo ufw logging low
```

Consultar mensagens do _kernel_ relacionadas ao `UFW`:

```bash
sudo journalctl -k | grep -i UFW
```

A disponibilidade dos registros também depende da configuração do serviço de _log_ do sistema.

### 2.4 Manter o `IPv6` protegido e evitar portas desnecessárias

Manter o `IPv6` habilitado no `UFW` para que as regras também protejam conexões IPv6. Conferir a configuração atual:

```bash
grep '^IPV6=' /etc/default/ufw
sudo ufw status numbered
```

Quando o `IPv6` estiver habilitado (`IPV6=yes`), conferir as regras com sufixo `(v6)` e restringir as regras abertas para `Anywhere` da mesma forma que as regras IPv4.

Não abrir portas como `80`, `443`, `8080` ou `3389` sem um serviço que realmente precise recebê-las. Para aplicações de uso local, preferir vinculá-las ao endereço de _loopback_ (`127.0.0.1`). O `Tailscale` normalmente não exige a abertura manual de portas de entrada; liberar portas adicionais somente se houver uma necessidade identificada.

### 2.5 Conferir a configuração final

Depois das alterações, conferir novamente as políticas e regras:

```bash
sudo ufw status verbose
sudo ufw status numbered
sudo ss -tulpen
```

Configuração de referência para um computador que não encaminha tráfego: _firewall_ ativo, entrada `deny`, saída `allow`, tráfego roteado `deny`, registros `low`, `IPv6` protegido e portas de entrada limitadas aos serviços e origens necessários.


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
Acessado em: 29/10/2026.

[3] CANONICAL.
**Manual do comando `ufw` para o `linux ubuntu 22.04 LTS`**.
Disponível em: <https://manpages.ubuntu.com/manpages/jammy/man8/ufw.8.html>.
Manual do Ubuntu.
Acessado em: 29/10/2026.

[4] TAILSCALE.
**Restringir o acesso `SSH` com `ufw` e `Tailscale`**.
Disponível em: <https://tailscale.com/docs/how-to/secure-ubuntu-server-with-ufw>.
Documentação do Tailscale.
Acessado em: 29/10/2026.


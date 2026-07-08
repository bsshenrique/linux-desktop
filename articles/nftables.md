# nftables

`nftables` é a estrutura moderna do _kernel_ Linux utilizada para classificar pacotes.  
Foi desenvolvida para substituir e unificar as implementações `{ip,ip6,arp,eb}tables`.

No conceito básico do `nftables`, existem principalmente _tables_ e _chains_.

_Tables_ representam um conjunto de _chains_.  
Toda _table_ pertence a uma família.

```plaintext
FAMÍLIA   PROTOCOLO         SUBSTITUI
ip        IPv4              iptables
ip6       IPv6              ip6tables
inet      IPv4 e IPv6       iptables e ip6tables
arp       ARP               arptables
bridge    Bridge Ethernet   ebtables
```

_Chains_ são um conjunto de _rules_ armazenadas dentro de uma _table_.  
Podem ser classificadas em _base chains_, que atuam em pontos específicos do fluxo de rede do kernel (como _input_, _output_, _forward_), ou em _regular chains_, que são usadas para agrupar _rules_ e saltar para elas.

As _rules_ são processadas de cima para baixo, em ordem sequencial.

Comandos básicos para administrar o `nftables`.

```bash
sudo nft list ruleset                   # Lista todas as regras

sudo nft flush ruleset                  # Remove todas as regras

sudo nft -c -f arquivo.conf             # Valida um arquivo de configuração
sudo nft -f arquivo.conf                # Carrega um conjunto de regras
# -c, --check
# -f, --file

sudo nft list tables
sudo nft list table familia tabela      # Lista todas as regras de uma tabela

sudo nft add table familia tabela

sudo nft delete table familia tabela

sudo nft flush table familia tabela     # Remove todas as regras de uma tabela
```

Desative o seu [front-end](https://wiki.archlinux.org/title/Nftables#Front-ends), edite a configuração `/etc/nftables.conf` e habilite o serviço.
Exemplo:

```bash
sudo systemctl enable --now nftables
```

```conf
destroy table inet workstation_firewall

table inet workstation_firewall {
  chain input {
    # type filter       Chain do tipo filter
    # hook input        Pacotes destinados ao dispositivo
    # priority filter   Igual a prioridade 0, se nenhuma regra for atendida essa regra é aplicada
    type filter hook input priority filter; policy drop;

    # ct                Connection Tracking (rastreamento de conexões)
    # state invalid     ct para pacotes que não pertencem a uma conexão rastreável válida
    ct state invalid drop comment "Drop invalid connections"

    # state             ct para pacotes de conexões já iniciadas e ativas, e pacotes relacionados a conexões existentes
    ct state { established, related } accept comment "Accept traffic originated from us"

    # iif lo            input interface loopback
    iif lo accept comment "Accept any localhost traffic"

    # fib               Forwarding Information Base
    # daddr . iif       Consulta na fib se o endereço de destino (daddr) do pacote está configurado na iif
    # type !=           Regra aplicada a todos os pacotes cujo tipo do destino não seja igual aos valores
    fib daddr . iif type != { local, broadcast, multicast } drop comment "Strong Host Model: Drop packets if the destination IP address is not configured on the incoming interface"

    # meta l4proto      Metadados do protocolo de camada 4 icmp ou ipv6-icmp
    meta l4proto { icmp, ipv6-icmp } accept comment "Accept ICMP"

    # ip protocol igmp  Tráfego de pacotes ipv4 utilizando o protocolo IGMP
    ip protocol igmp accept comment "Accept IGMP"

    # udp               Pacotes UDP
    # dport mdns        Cuja porta de destino seja mDNS (5353)
    # ip6 daddr         Utilizando IPv6 cujo endereço de destino seja ff02::fb
    # ff02::fb          Endereço reservado para mDNS
    udp dport mdns ip6 daddr ff02::fb accept comment "Accept mDNS"

    # 224.0.0.251       Endereço reservado para mDNS
    udp dport mdns ip daddr 224.0.0.251 accept comment "Accept mDNS"

    # pkttype host      Package Type host, pacotes destinados diretamente ao host (não multicast ou broadcast)
    # limit rate        Permite no máximo 5 pacotes por segundo
    # counter           Registra o número de pacotes que excederem o limite
    # reject            Rejeita os pacotes
    # with icmpx        Utilizando uma mensagem ICMPX (ICMP Estendida) do tipo admin-prohibited
    pkttype host limit rate 5/second counter reject with icmpx type admin-prohibited

    counter comment "Count any other traffic"
  }

  chain forward {
    # hook forward      Pacotes encaminhados pelo dispositivo, que entram por uma interface e saem por outra
    type filter hook forward priority filter; policy drop;
  }

  chain output {
    # hook output       Pacotes originados no dispositivo
    type filter hook output priority filter; policy accept;
  }
}
```

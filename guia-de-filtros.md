# Guia de filtros

Filtros que usei no Wireshark pra analisar as capturas deste projeto, com o que cada um mostra.

## Filtro de captura (tcpdump)

O tcpdump usa uma sintaxe diferente da do Wireshark. Esse filtro foi aplicado na hora de gravar, pra não encher a captura com a minha própria sessão SSH:

| Filtro | O que faz |
|---|---|
| `not (host 192.168.56.1 and port 22)` | Ignora o tráfego entre o meu PC e a Ubuntu na porta 22, que é a sessão SSH que eu estava usando |

## Filtros de exibição (Wireshark)

### Captura normal (`captura-normal.pcap`)

| Filtro | O que mostra | Print |
|---|---|---|
| `tcp.flags.syn==1` | Pacotes com a flag SYN, ou seja, os SYN e os SYN-ACK. O ACK final do handshake não aparece porque não tem SYN | |
| `tcp.stream eq 0` | Uma conexão TCP inteira, do handshake ao FIN. Aparece sozinho quando uso *Filtro de Conversação → TCP* | `02-handshake.png` |
| `dns.qry.name == "example.com"` | Perguntas e respostas DNS sobre o example.com. Apareceram as do `dig` (pro 1.1.1.1) e as do `curl` (pro OPNsense), incluindo A e AAAA | `03-dns.png` |
| `http` | Só o pedido `GET` e a resposta `200 OK`, legíveis | `04-http.png` |
| `tls` | O Client Hello, o Server Hello e depois só Application Data, tudo cifrado | `06-tls.png` |
| `udp.port==123` | Tráfego NTP. Usei pra testar se o firewall bloqueava o relógio da VM, e não apareceu nada | |
| `tcp.port==4460` | Tráfego NTS (sincronização de relógio segura). Também não apareceu nada | |

### Captura do scan (`captura-scan.pcap`)

| Filtro | O que mostra | Print |
|---|---|---|
| `tcp.flags.syn==1 && tcp.flags.ack==0` | Só os SYN "puros", de quem está iniciando conexão. No scan são milhares, cada um pra uma porta | `08-scan-syns.png` |
| `tcp.flags.syn==1 && tcp.flags.ack==1` | Só os SYN-ACK, as respostas de porta aberta. Foram poucas, só das portas 53, 80 e 443 | `09-scan-synack.png` |
| `tcp.port==80` | Tudo o que aconteceu na porta 80: SYN, SYN-ACK e RST. É o half-open | `10-scan-half-open.png` |

## Outras ferramentas do Wireshark que usei

| Onde fica | Pra que serve | Print |
|---|---|---|
| Botão direito → *Filtro de Conversação → TCP* | Isola uma conexão inteira | `02-handshake.png` |
| Botão direito → *Seguir → Fluxo HTTP* | Mostra a conversa HTTP inteira em texto | `05-http-stream.png` |
| Botão direito → *Seguir → Fluxo TLS* | Mostra que o conteúdo do HTTPS é ilegível | |
| *Estatísticas → Conversas* | Lista todas as conversas por protocolo. No scan apareceram 2000 conversas TCP entre o mesmo par de IPs | `11-scan-conversations.png`, `12-scan-ethernet.png` |

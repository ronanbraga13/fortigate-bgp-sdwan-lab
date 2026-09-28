# Endereçamento

## Sistemas autônomos e LANs

| Unidade | AS | Redes LAN |
| --- | --- | --- |
| Matriz | 65001 | 10.0.10.0/24; 10.0.20.0/24; 10.0.30.0/24 |
| Rio | 65000 | 10.10.10.0/24; 10.10.20.0/24; 10.10.30.0/24 |



## Underlay

| Operadora | Unidade | Endereço do FortiGate | Gateway | Sub-rede |
| --- | --- | --- | --- | --- |
| CLARO | Matriz | 172.16.10.2/30 | 172.16.10.1 | 172.16.10.0/30 |
| CLARO | Rio | 172.16.10.6/30 | 172.16.10.5 | 172.16.10.4/30 |
| VIVO | Matriz | 172.16.20.2/30 | 172.16.20.1 | 172.16.20.0/30 |
| VIVO | Rio | 172.16.20.6/30 | 172.16.20.5 | 172.16.20.4/30 |

Máscara /30: 255.255.255.252. O roteamento entre as duas sub-redes de cada operadora precisa ser completado conforme a infraestrutura real.

## Overlay e peers BGP

| Caminho | IP Matriz | IP Rio | Peer na Matriz | Peer no Rio |
| --- | --- | --- | --- | --- |
| VIVO | 1.1.1.2 | 1.1.1.1 | 1.1.1.1 / AS 65000 | 1.1.1.2 / AS 65001 |
| CLARO | 2.2.2.2 | 2.2.2.1 | 2.2.2.1 / AS 65000 | 2.2.2.2 / AS 65001 |

Os backups configuram os IPs locais dos túneis com máscara 255.255.255.255 e remote-ip com o endereço do peer e máscara 255.255.255.252. Router-id: 1.1.1.9 na Matriz e 1.1.1.10 no Rio. Os túneis CLARO usam port1; VIVO usa port2.

Os IPs de overlay reproduzem o LAB, mas são endereços públicos: não anuncie esses prefixos para redes externas nem use esses IPs como alvos públicos de DNS neste ambiente.

## Alvos de Performance SLA

| Origem da medição | Destino |
| --- | --- |
| Matriz → Rio | 10.10.10.1 |
| Rio → Matriz | 10.0.10.1 |

Origem das sondas: 10.0.10.1 na Matriz e 10.10.10.1 no Rio. Membros do SLA: 4 e 3 na Matriz; 2 e 1 no Rio. O protocolo não aparece explicitamente nos blocos exportados. A medição deve alcançar o destino pelo membro avaliado e ter retorno adequado.

# Configurações FortiGate

Trechos dasa configuraçãoes realizadas.

| Unidade | Arquivo |
| --- | --- |
| Matriz | [fortigate-matriz.conf](fortigate-matriz.conf) |
| Rio | [fortigate-rio.conf](fortigate-rio.conf) |

Cada arquivo contém somente as VPNs IPsec (Phase 1 e Phase 2), as interfaces de túnel que endereçam os peers, a zona e os membros SD-WAN das VPNs, a regra entre unidades e seu Performance SLA, configuração do BGP simples, sem utilização de prefix list e route map por se tratar de uma comunicação simulado entre Matriz e Filial.
Nas evidências relatadas não foi apresentada neste lab, as políticas do firewall para comunicação, em caso de reproduzação, se atentar a essa observação.

As PSKs foram removidas. As portas WAN, LANs, grupos `GRP_ADDR_MATRIZ` e `GRP_ADDR_RIO`, roteamento e políticas sd-wan são dependências do ambiente e não fazem parte destes recortes. Os arquivos não são backups completos para restauração, somente para visualização.

Foram preservados nomes, IDs e parâmetros explícitos das configurações. Alguns valores foram omitidos por não ser o foco do lab, como configurações de switches, roteadores, gerenciamento, SD-WAN de Internet e SLAs padrão não estão incluídas.

O BGP permanece explicado em [docs/bgp.md](../docs/bgp.md). 

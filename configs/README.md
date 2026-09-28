# Configurações FortiGate

Trechos reais dos backups fornecidos, de **FortiOS 7.2.8 build 1639**.

| Unidade | Arquivo |
| --- | --- |
| Matriz | [fortigate-matriz.conf](fortigate-matriz.conf) |
| Rio | [fortigate-rio.conf](fortigate-rio.conf) |

Cada arquivo contém somente as VPNs IPsec (Phase 1 e Phase 2), as interfaces de túnel que endereçam os peers, a zona e os membros SD-WAN das VPNs, a regra entre unidades e seu Performance SLA.

As PSKs foram removidas. As portas WAN, LANs, grupos `GRP_ADDR_MATRIZ` e `GRP_ADDR_RIO`, roteamento e políticas são dependências do ambiente e não fazem parte destes recortes. Os arquivos não são backups completos para restauração.

Foram preservados nomes, IDs e parâmetros explícitos dos backups. Valores omitidos no export, como intervalo das sondas, não foram acrescentados. Configurações de switches, roteadores, gerenciamento, SD-WAN de Internet e SLAs padrão não estão incluídas.

O BGP permanece explicado em [docs/bgp.md](../docs/bgp.md). 

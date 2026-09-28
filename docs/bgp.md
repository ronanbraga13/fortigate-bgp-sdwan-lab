# BGP Multipath e ECMP

## Adjacências

São duas sessões eBGP por FortiGate, uma por túnel.

| FortiGate | AS local | Neighbor VIVO | Neighbor CLARO | AS remoto |
| --- | --- | --- | --- | --- |
| Matriz | 65001 | 1.1.1.1 | 2.2.2.1 | 65000 |
| Rio | 65000 | 1.1.1.2 | 2.2.2.2 | 65001 |

A Matriz anuncia 10.0.10.0/24, 10.0.20.0/24 e 10.0.30.0/24. O Rio anuncia 10.10.10.0/24, 10.10.20.0/24 e 10.10.30.0/24. Os backups fornecidos confirmam a originação desses prefixos por blocos `network`, com router-id 1.1.1.9 na Matriz e 1.1.1.10 no Rio.

## Multipath

O histórico do teste registra `ebgp-multipath` habilitado nos dois FortiGates e as capturas mostram dois next-hops. Os backups fornecidos posteriormente não contêm `set ebgp-multipath enable`; portanto, não devem ser tratados como prova de que esse parâmetro estava habilitado naquele export. A Fortinet documenta sua necessidade para instalar múltiplos caminhos eBGP na tabela de roteamento: [Equal cost multi-path](https://docs.fortinet.com/document/fortigate/7.4.5/administration-guide/25967).

A habilitação não torna quaisquer rotas equivalentes automaticamente. Os caminhos precisam atender aos critérios de seleção do BGP. No LAB, a instalação de dois next-hops por prefixo foi confirmada.

| Local da consulta | Prefixos remotos | Next-hops confirmados |
| --- | --- | --- |
| Matriz | As três LANs do Rio | 1.1.1.1 e 2.2.2.1 |
| Rio | As três LANs da Matriz | 1.1.1.2 e 2.2.2.2 |

ECMP na tabela de rotas não comprova distribuição 50/50 de tráfego. Regras SD-WAN e o tratamento das sessões também participam do encaminhamento.

## Validação

Comandos de consulta usados no roteiro; conferir disponibilidade na versão instalada:

```text
get router info bgp summary
get router info routing-table bgp
get router info routing-table details 10.10.10.0
```

No Rio, substituir o prefixo da última consulta por 10.0.10.0. Repetir para as demais LANs.

Critérios: dois peers estabelecidos, três prefixos recebidos por peer e dois next-hops instalados por prefixo remoto. Na saída de resumo, a coluna final pode mostrar a quantidade de prefixos quando a sessão está estabelecida.

## Relação com SD-WAN

Nos testes de degradação, o BGP permaneceu estabelecido mesmo com perdas de 22% e 17%. A disponibilidade da sessão BGP, isoladamente, não representa a qualidade do caminho para a aplicação. Não é necessário afirmar que a rota BGP foi removida para explicar uma preferência SD-WAN por outro caminho.

Recortes de VPN, interfaces de túnel e SD-WAN/SLA (sem o bloco de configuração BGP): [Matriz](../configs/fortigate-matriz.conf) e [Rio](../configs/fortigate-rio.conf).

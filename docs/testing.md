# Testes e validação

## Escopo dos testes

Os testes foram executados para validar o comportamento do BGP, ECMP e SD-WAN em condições normais, durante degradação controlada dos links, em cenário de indisponibilidade e após a recuperação.

As perdas foram induzidas intencionalmente no ambiente de laboratório para simular degradação de WAN e validar a reação do SD-WAN com base no Performance SLA. Foram realizados testes separados com degradação na VIVO e na CLARO.

As evidências utilizadas nesta documentação são capturas reais do LAB. As imagens foram apenas recortadas para remover elementos de gerenciamento que não faziam parte da validação.

## Matriz de resultados

| ID | Cenário | Resultado |
| --- | --- | --- |
| T01 | Baseline BGP | Dois neighbors em `Established` por FortiGate e três prefixos por neighbor |
| T02 | ECMP | Dois next-hops disponíveis para os prefixos remotos |
| T03 | Degradação induzida na VIVO, Matriz → Rio | 22% de perda induzida, mantendo as sessões BGP estabelecidas |
| T04 | Degradação induzida na CLARO, Rio → Matriz | 17% de perda induzida na CLARO, com BGP mantido |
| T05 | Indisponibilidade de caminho | VIVO marcada como indisponível pelo SLA e continuidade pela CLARO; duas perdas ICMP consecutivas durante a convergência |
| T06 | Recuperação | Retorno automático do SLA, BGP e ECMP ao estado saudável |

Os testes tiveram como objetivo validar convergência, disponibilidade de rotas e comportamento do SD-WAN. Não foram utilizados como benchmark de throughput ou latência de aplicação.

## Capturas dos testes

### T01 / T02 — Baseline BGP e ECMP

No estado normal, os dois caminhos estavam disponíveis, com os peers BGP estabelecidos e dois next-hops para os prefixos remotos.

![Rio: BGP e ECMP](../images/bgp/rio-neighbors-ecmp.png)

![Matriz: BGP e ECMP](../images/bgp/matriz-recuperacao-neighbors-ecmp.png)

### T03 — VIVO: 22% de perda induzida

Foi aplicada degradação controlada no caminho VIVO para simular perda de pacotes e observar o comportamento do ambiente.

Durante o teste foi registrada perda de 22%, mantendo o BGP estabelecido. A captura específica que mostrava os 22% não foi preservada; a evidência disponível desse momento registra o estado do BGP da Matriz durante a degradação.

![Matriz: BGP durante a degradação](../images/bgp/matriz-bgp-durante-degradacao.png)

### T04 — CLARO: 17% de perda induzida

Em um teste separado, a degradação foi aplicada no caminho CLARO no sentido Rio → Matriz. Foi registrada perda de 17% na CLARO, enquanto a VIVO permaneceu saudável e as adjacências BGP continuaram estabelecidas.

![Rio: 17% de perda na CLARO e BGP mantido](../images/tests/rio-claro-perda-17-bgp.png)

Também foi registrada uma medição intermediária de 12% durante a degradação:

![Rio: medição intermediária de perda](../images/sdwan/rio-sla-perda-12.png)

O ping contínuo foi utilizado para acompanhar o comportamento da comunicação durante o teste:

![Rio → Matriz: ping contínuo](../images/tests/rio-matriz-ping-continuo.png)

### T05 — Indisponibilidade e convergência

Após os testes de degradação, um dos caminhos foi tornado indisponível de forma controlada para validar o failover.

O Performance SLA identificou a VIVO como indisponível, enquanto a CLARO permaneceu disponível.

![Matriz: VIVO indisponível no SLA](../images/tests/matriz-vivo-indisponivel.png)

Durante a convergência, o ping registrou timeout nas sequências 237 e 238, retomando as respostas a partir da sequência 239.

![Duas perdas ICMP durante convergência](../images/tests/failover-duas-perdas-icmp.png)

Esse teste confirmou a continuidade da comunicação pelo caminho remanescente durante a indisponibilidade simulada.

### T06 — Recuperação

Após a remoção da condição de falha, o ambiente retornou automaticamente ao estado saudável.

O SLA voltou a apresentar os links em condição normal:

![Matriz: SLA recuperado](../images/sdwan/matriz-recuperacao-sla.png)

As adjacências BGP e os dois caminhos ECMP também foram restabelecidos:

![Matriz: BGP e ECMP recuperados](../images/bgp/matriz-recuperacao-neighbors-ecmp.png)

## Procedimento utilizado nos testes

### 1. Baseline

Antes de aplicar qualquer degradação, foram verificadas as VPNs, adjacências BGP, rotas aprendidas e o estado do SD-WAN.

Comandos utilizados para validação:

```text
get router info bgp summary
get router info routing-table bgp
diagnose sys sdwan health-check
diagnose sys sdwan service
```

O baseline considerado saudável possuía os dois peers BGP estabelecidos, os prefixos remotos aprendidos e dois next-hops disponíveis por ECMP.

### 2. Degradação Matriz → Rio

Foi mantido tráfego ICMP entre as localidades e aplicada perda controlada somente no caminho VIVO.

Durante a degradação foram acompanhados:

- Performance SLA;
- adjacências BGP;
- rotas BGP;
- ECMP;
- continuidade do tráfego.

O teste registrou 22% de perda induzida na VIVO.

### 3. Degradação Rio → Matriz

Após restaurar o ambiente ao baseline, foi realizado um segundo teste no sentido Rio → Matriz, desta vez degradando o caminho CLARO.

O teste registrou 17% de perda induzida na CLARO, mantendo o BGP estabelecido.

### 4. Indisponibilidade

Com o ambiente novamente em estado normal, um único caminho foi tornado indisponível de forma controlada.

Foram acompanhados simultaneamente:

- ping entre as localidades;
- Performance SLA;
- IPsec;
- BGP;
- tabela de roteamento;
- serviço SD-WAN.

Durante a convergência foram observadas duas perdas ICMP consecutivas, com retomada imediata da comunicação pelo caminho disponível.

### 5. Recuperação

A condição de falha foi removida e o retorno do ambiente foi acompanhado até a normalização.

A recuperação ocorreu automaticamente, com:

- SLA saudável;
- BGP restabelecido;
- dois next-hops novamente disponíveis por ECMP;
- continuidade da comunicação entre as localidades.

## Evidências publicadas

As imagens utilizadas nos testes estão organizadas na [galeria com legendas](../images/README.md).

| Testes | Evidência |
| --- | --- |
| T01 / T02 | [BGP e ECMP no Rio](../images/bgp/rio-neighbors-ecmp.png); [BGP e ECMP na Matriz](../images/bgp/matriz-recuperacao-neighbors-ecmp.png) |
| T03 | [BGP da Matriz durante a degradação](../images/bgp/matriz-bgp-durante-degradacao.png) |
| T04 | [Perda de 17% e BGP](../images/tests/rio-claro-perda-17-bgp.png); [medição intermediária de 12%](../images/sdwan/rio-sla-perda-12.png); [ping contínuo](../images/tests/rio-matriz-ping-continuo.png) |
| T05 | [VIVO indisponível no SLA](../images/tests/matriz-vivo-indisponivel.png); [duas perdas ICMP](../images/tests/failover-duas-perdas-icmp.png) |
| T06 | [SLA recuperado na Matriz](../images/sdwan/matriz-recuperacao-sla.png); [BGP/ECMP recuperados](../images/bgp/matriz-recuperacao-neighbors-ecmp.png) |

A [captura de baseline do Rio](../images/sdwan/rio-baseline-ecmp-sla.png) complementa as evidências do funcionamento normal do ambiente.

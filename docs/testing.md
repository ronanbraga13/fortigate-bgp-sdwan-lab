# Testes e validação

## Origem das informações

Os resultados abaixo foram confirmados pelo autor e recuperados do histórico do LAB. Esta publicação não executou novamente testes nos FortiGates. Dez capturas reais enviadas pelo autor foram recuperadas e publicadas após revisão visual e recorte local para remover barras e dados de gerenciamento. Os pixels mantidos foram preservados, sem reconstrução por IA. Logs brutos não foram adicionados.

**Condição experimental:** as perdas foram induzidas intencionalmente em testes separados: primeiro 22% na VIVO e, depois, 17% na CLARO para simular a degradação de um caminho WAN e verificar a reação do SD-WAN ao Performance SLA. Os percentuais observados não descrevem uma falha espontânea da operadora. A prova do caminho efetivo de cada fluxo ainda requer correlação de sessão e captura.

## Matriz de resultados

| ID | Cenário | Resultado registrado | Limite da conclusão |
| --- | --- | --- | --- |
| T01 | Baseline BGP | Dois neighbors Established por FortiGate; três prefixos por neighbor | Conferir novamente ao reproduzir |
| T02 | ECMP | Dois next-hops por prefixo remoto | Não mede divisão de banda ou sessões |
| T03 | Degradação induzida na VIVO, Matriz → Rio | 22% de perda com BGP mantido | Print de BGP incluído; 22% na VIVO conforme relato do autor, sem captura da medição |
| T04 | Degradação induzida na CLARO, Rio → Matriz | 17% de perda com BGP mantido | Ambos ainda apareciam selected; caminho por sessão não comprovado pelo indicador |
| T05 | Indisponibilidade | Duas perdas ICMP consecutivas durante convergência | Histórico identifica caminho VIVO indisponível e continuidade pela CLARO; não comprova falha física |
| T06 | Recuperação | Automática; retorno de BGP/ECMP e qualidade ao baseline | Tempo exato de recuperação não medido |

Não há benchmark de throughput, latência de aplicação ou garantia de perda máxima. A perda medida pelo SLA não é necessariamente idêntica à perda de um fluxo ICMP de usuário.

## Capturas dos testes

### T03 — VIVO: 22% de perda induzida

*Obs.: No momento do teste, a evidência dos 22% acabou se perdendo... rsrs. O resultado fica registrado pelo relato do autor, mas sem o print dessa medição.*

O print disponível registra o BGP da Matriz com dois neighbors e três prefixos por neighbor. Não exibe os 22% nem comprova o encaminhamento de uma sessão.

![Matriz: BGP durante a degradação](../images/bgp/matriz-bgp-durante-degradacao.png)

### T04 — CLARO: 17% de perda induzida

Teste separado do anterior: CLARO com 17%, VIVO com 0% e BGP mantido. Ambos aparecem como `selected`.

![Rio: 17% de perda na CLARO e BGP mantido](../images/tests/rio-claro-perda-17-bgp.png)

### T05 — Indisponibilidade e convergência

Este é o teste de indisponibilidade, distinto da medição de 22%. O SLA mostra VIVO indisponível e CLARO com 0% de perda.

![Matriz: VIVO indisponível no SLA](../images/tests/matriz-vivo-indisponivel.png)

O ping registra timeout nas sequências 237 e 238, retomando as respostas na 239.

![Duas perdas ICMP durante convergência](../images/tests/failover-duas-perdas-icmp.png)

### T06 — Recuperação

Retorno ao estado saudável após a retirada da falha induzida.

![Matriz: SLA recuperado](../images/sdwan/matriz-recuperacao-sla.png)

![Matriz: BGP e ECMP recuperados](../images/bgp/matriz-recuperacao-neighbors-ecmp.png)

## Roteiro de reprodução

### 1. Registrar baseline

Anotar versão/build do FortiOS, horário, interfaces, origem/destino e intervalo do ping. Coletar nos dois FortiGates:

```text
get router info bgp summary
get router info routing-table bgp
diagnose sys sdwan health-check
diagnose sys sdwan service
```

Conferir três redes remotas, dois peers, dois next-hops por prefixo e métricas saudáveis. Validar VPNs e políticas antes de prosseguir.

### 2. Degradação Matriz → Rio

Manter ping de um host da Matriz para um host confirmado do Rio. Induzir perda somente no caminho VIVO com o mecanismo do ambiente de laboratório (TODO: documentar ferramenta e comando reais). Registrar a perda efetivamente medida, BGP, ECMP e serviço SD-WAN.

O valor histórico é 22%; não é um comando de configuração nem uma garantia para futuras execuções. Correlacionar sessão/captura com a saída do serviço para demonstrar o caminho utilizado.

### 3. Restaurar e testar Rio → Matriz

Remover a degradação, confirmar baseline e executar outro teste, agora induzindo perda na CLARO no sentido Rio → Matriz. Registrar o valor medido; o resultado final documentado foi 17% na CLARO. Não usar apenas selected/unselected como prova de encaminhamento.

### 4. Indisponibilidade

Após restaurar o baseline, tornar um único caminho indisponível de forma controlada no LAB. Registrar exatamente o mecanismo: interrupção do enlace, bloqueio de sondas e queda do túnel são falhas diferentes.

Acompanhar ping, SLA, IPsec, BGP e rotas. Contar perdas consecutivas e registrar timestamps. O teste histórico apresentou duas perdas; o intervalo do ping não foi confirmado, portanto não há tempo exato de failover derivado desse número.

### 5. Recuperação

Restaurar o caminho e verificar retorno automático das métricas, adjacências e dois next-hops. Registrar duração medida e qualquer intervenção necessária; no LAB original a recuperação foi automática.

## Evidências publicadas

As dez imagens estão na [galeria com legendas](../images/README.md).

| Testes | Evidência |
| --- | --- |
| T01 / T02 | [BGP e ECMP no Rio](../images/bgp/rio-neighbors-ecmp.png); [BGP e ECMP na Matriz](../images/bgp/matriz-recuperacao-neighbors-ecmp.png) |
| T03 | [BGP da Matriz durante a degradação](../images/bgp/matriz-bgp-durante-degradacao.png): o valor de 22% não aparece no print |
| T04 | [Perda de 17% e BGP](../images/tests/rio-claro-perda-17-bgp.png); [medição intermediária de 12%](../images/sdwan/rio-sla-perda-12.png); [ping contínuo](../images/tests/rio-matriz-ping-continuo.png) |
| T05 | [VIVO indisponível no SLA](../images/tests/matriz-vivo-indisponivel.png); [duas perdas ICMP](../images/tests/failover-duas-perdas-icmp.png) |
| T06 | [SLA recuperado na Matriz](../images/sdwan/matriz-recuperacao-sla.png); [BGP/ECMP recuperados](../images/bgp/matriz-recuperacao-neighbors-ecmp.png) |

A [captura de baseline do Rio](../images/sdwan/rio-baseline-ecmp-sla.png) complementa os testes. As imagens são instantes distintos; o contexto temporal vem da conversa original. Não foram recuperadas neste conjunto capturas de topologia, configuração de IPsec ou a tela mostrando diretamente os 22%.

## Testes ainda pendentes

Falha física de operadora com rastreamento completo de IPsec/BGP, tempos medidos por timestamps, throughput, distribuição de sessões e comprovação do caminho por fluxo em ambos os sentidos.

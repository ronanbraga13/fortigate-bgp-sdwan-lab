# Testes e validação

## Origem das informações

Os resultados abaixo foram confirmados pelo autor e recuperados do histórico do LAB. Esta publicação não executou novamente testes nos FortiGates. Capturas originais e logs brutos não foram incorporados, evitando exposição de dados de gerenciamento; não há imagens fabricadas representando saídas reais.

## Matriz de resultados

| ID | Cenário | Resultado registrado | Limite da conclusão |
| --- | --- | --- | --- |
| T01 | Baseline BGP | Dois neighbors Established por FortiGate; três prefixos por neighbor | Conferir novamente ao reproduzir |
| T02 | ECMP | Dois next-hops por prefixo remoto | Não mede divisão de banda ou sessões |
| T03 | Degradação CLARO Matriz → Rio | 22% de perda com BGP mantido | Histórico relata preferência pela VIVO; captura não incluída |
| T04 | Degradação CLARO Rio → Matriz | 17% de perda com BGP mantido | Ambos ainda apareciam selected; caminho por sessão não comprovado pelo indicador |
| T05 | Indisponibilidade | Duas perdas ICMP consecutivas durante convergência | Histórico identifica caminho VIVO indisponível e continuidade pela CLARO; não comprova falha física |
| T06 | Recuperação | Automática; retorno de BGP/ECMP e qualidade ao baseline | Tempo exato de recuperação não medido |

Não há benchmark de throughput, latência de aplicação ou garantia de perda máxima. A perda medida pelo SLA não é necessariamente idêntica à perda de um fluxo ICMP de usuário.

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

Manter ping de um host da Matriz para um host confirmado do Rio. Induzir perda somente no caminho CLARO com o mecanismo do ambiente de laboratório (TODO: documentar ferramenta e comando reais). Registrar a perda efetivamente medida, BGP, ECMP e serviço SD-WAN.

O valor histórico é 22%; não é um comando de configuração nem uma garantia para futuras execuções. Correlacionar sessão/captura com a saída do serviço para demonstrar o caminho utilizado.

### 3. Restaurar e testar Rio → Matriz

Remover a degradação, confirmar baseline e repetir no sentido inverso. Registrar o valor medido; o resultado final documentado foi 17% na CLARO. Não usar apenas selected/unselected como prova de encaminhamento.

### 4. Indisponibilidade

Após restaurar o baseline, tornar um único caminho indisponível de forma controlada no LAB. Registrar exatamente o mecanismo: interrupção do enlace, bloqueio de sondas e queda do túnel são falhas diferentes.

Acompanhar ping, SLA, IPsec, BGP e rotas. Contar perdas consecutivas e registrar timestamps. O teste histórico apresentou duas perdas; o intervalo do ping não foi confirmado, portanto não há tempo exato de failover derivado desse número.

### 5. Recuperação

Restaurar o caminho e verificar retorno automático das métricas, adjacências e dois next-hops. Registrar duração medida e qualquer intervenção necessária; no LAB original a recuperação foi automática.

## Evidências a adicionar

Capturas sanitizadas de topologia, túneis, neighbors, tabela de rotas, SLA, serviço SD-WAN, ping e recuperação. Os diretórios em [images](../images/README.md) organizam esse material futuro. Remover credenciais, dados de gerenciamento e identificadores sensíveis antes de incluir imagens ou logs.

## Testes ainda pendentes

Falha física de operadora com rastreamento completo de IPsec/BGP, tempos medidos por timestamps, throughput, distribuição de sessões e comprovação do caminho por fluxo em ambos os sentidos.

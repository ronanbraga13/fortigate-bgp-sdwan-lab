# SD-WAN e Performance SLA

## Estratégia confirmada

O LAB utiliza **Best Quality**, com **Packet Loss** como critério, e Performance SLA até a unidade remota.

Best Quality compara a métrica escolhida entre os caminhos. A avaliação de metas SLA e a comparação de qualidade não são conceitos intercambiáveis: ultrapassar 8% não comprova, isoladamente, que o membro foi excluído. Referências: [Best quality strategy](https://docs.fortinet.com/document/fortigate/7.4.5/administration-guide/022371/best-quality-strategy) e [Fields for configuring WAN intelligence](https://docs.fortinet.com/document/fortigate/7.4.5/administration-guide/582798/fields-for-configuring-wan-intelligence).

## Parâmetros observados

| Parâmetro | Valor |
| --- | --- |
| SLA Matriz → Rio | 10.10.10.1 |
| SLA Rio → Matriz | 10.0.10.1 |
| Latency threshold | 40 ms |
| Jitter threshold | 40 ms |
| Packet loss threshold | 8% |
| Check interval | 500 ms |
| Failures before inactive | 5 |
| Restore | 5 checks |

Não se deduz um tempo exato de convergência pela multiplicação de intervalo e contagem: temporizadores, sondas, sessões e o instante da falha afetam o resultado observado.

## Interpretação dos testes

A degradação na CLARO foi **induzida de propósito** no ambiente controlado para simular perda no link e verificar a atuação do SD-WAN com base no Performance SLA. Não se tratou de degradação espontânea da operadora. O histórico disponível sustenta a avaliação da reação da regra, com os limites de comprovação do caminho por sessão descritos abaixo.

- **Matriz → Rio:** CLARO atingiu 22% de perda e o BGP permaneceu estabelecido. O histórico relata preferência pelo caminho VIVO durante a degradação.
- **Rio → Matriz:** CLARO atingiu 17% de perda e o BGP permaneceu estabelecido. O diagnóstico registrado ainda mostrava os dois membros como `selected`; não há base para documentar CLARO como `unselected` nessa captura.
- **Recuperação:** retorno automático ao estado saudável.

A documentação da Fortinet mostra que ambos os membros podem aparecer como `selected` em Best Quality. Para comprovar o caminho efetivo de um fluxo, correlacione ordem/prioridade do serviço, sessão e captura de tráfego. O status isolado não é prova suficiente.

## Consultas

```text
diagnose sys sdwan health-check
diagnose sys sdwan service
```

Algumas versões usam variantes como `service4`; confirmar pela ajuda da CLI. Registrar métricas por membro, ordem de preferência, regra correspondente ao fluxo e estado dos neighbors.

## TODOs

Confirmar nomes e IDs de membros/regras, zona, escopo de origem/destino/serviço, protocolo e origem das sondas, ordem de preferência, valor de link-cost-threshold e integração com políticas. Esses itens não foram transformados em configuração executável.

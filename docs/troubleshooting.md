# Troubleshooting

Investigue por camada e compare os dois sentidos. Os comandos abaixo são consultas; a sintaxe pode variar com o FortiOS.

| Sintoma | Verificação |
| --- | --- |
| Endpoint remoto inacessível | IP/máscara e gateway do underlay; roteamento entre as sub-redes /30 da operadora |
| IPsec não estabelece | Reachability dos endpoints, propostas/IKE, seletores e autenticação; valores reais ainda são TODO |
| BGP Idle/Active | Túnel, endereços dos peers, AS remoto, alcance pelo overlay e políticas aplicáveis |
| BGP recebe menos de três prefixos | Rotas locais, método de originação e filtros de anúncio/recebimento |
| Apenas um next-hop | Multipath nos dois equipamentos, atributos dos caminhos e disponibilidade dos dois peers |
| SLA sem resposta | Destino, protocolo, origem da sonda, políticas e retorno por cada caminho |
| Perda alta com BGP Established | Pode ocorrer: BGP ativo não garante qualidade suficiente para aplicações |
| Ambos os membros selected | Correlacionar ordem do serviço e sessão; não concluir que há divisão igual ou que o caminho degradado foi excluído |
| Tráfego não segue a regra esperada | Correspondência de origem/destino/serviço, ordem das regras, rotas e políticas |
| Falha em apenas um sentido | Rota de retorno, políticas, estado da sessão e diferenças de configuração entre unidades |

## Consultas iniciais

```text
get router info bgp summary
get router info routing-table bgp
diagnose sys sdwan health-check
diagnose sys sdwan service
get vpn ipsec tunnel summary
```

Conferir comandos disponíveis com a ajuda local da CLI e o guia correspondente à versão instalada. Evitar publicar saídas completas de configuração: elas podem conter informações sensíveis.

## Método de isolamento

1. Confirmar conectividade local ao gateway de cada operadora.
2. Confirmar alcance entre endpoints do mesmo caminho.
3. Validar cada túnel individualmente.
4. Validar neighbors e prefixos BGP.
5. Validar instalação de rotas ECMP.
6. Conferir qualidade medida e regra SD-WAN correspondente.
7. Correlacionar o fluxo de teste com sessão/captura e rota de retorno.
8. Remover a falha induzida e repetir o baseline.

Não alterar timers, habilitar multihop ou mudar NAT apenas para eliminar um sintoma sem identificar a causa. Esses parâmetros não foram confirmados no LAB.

## Registro de incidente

Anotar sentido, caminho, horário, alteração aplicada, métricas antes/depois, estado BGP e efeito no fluxo. Sanitizar evidências antes de enviá-las ao repositório.

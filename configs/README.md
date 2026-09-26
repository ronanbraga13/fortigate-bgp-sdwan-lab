# Configurações parciais

Estes arquivos **não são backups**, não foram aplicados aos equipamentos nesta publicação e **não estão prontos para importação**.

| Arquivo | Conteúdo |
| --- | --- |
| [fortigate-matriz.conf](fortigate-matriz.conf) | Exemplo BGP com AS/peers confirmados; originação por network proposta |
| [fortigate-rio.conf](fortigate-rio.conf) | Exemplo equivalente para o Rio |
| [router-claro.conf](router-claro.conf) | Inventário dos gateways e TODOs de roteamento |
| [router-vivo.conf](router-vivo.conf) | Inventário dos gateways e TODOs de roteamento |
| [switch-matriz.conf](switch-matriz.conf) | Redes confirmadas e TODOs de portas/VLANs |
| [switch-rio.conf](switch-rio.conf) | Redes confirmadas e TODOs de portas/VLANs |

## Antes de usar os exemplos

- Confirmar versão/build do FortiOS, contexto VDOM e compatibilidade da sintaxe.
- Definir interfaces, máscaras de túnel, router-id e políticas a partir do ambiente real.
- Validar presença das redes locais e método de originação BGP; network é apenas a proposta dos exemplos.
- Completar IPsec, incluindo propostas, seletores e autenticação, diretamente no ambiente seguro.
- Completar membros, regra e SLA conforme [SD-WAN](../docs/sdwan.md).
- Revisar filtros BGP, políticas e comportamento de NAT conforme os requisitos do LAB.

Não há PSKs, senhas ou tokens nestes exemplos. Não adicionar segredos, mesmo cifrados, nem exportações integrais. Comentários TODO indicam lacunas intencionais.

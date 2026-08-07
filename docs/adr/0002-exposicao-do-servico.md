# ADR 0002: Exposição do Serviço via LoadBalancer

- **Status:** Aprovado
- **Data:** 2026-08-05

## Contexto
A API precisa ser acessível externamente pelos clientes. As opções consideradas foram usar um Ingress Controller (ex.: Nginx) com roteamento por regras de host/path, ou expor o serviço diretamente via um Service do tipo LoadBalancer.

## Decisão
Optou-se por expor a aplicação através de um Service Kubernetes do tipo `LoadBalancer`, sem Ingress Controller, roteando diretamente a porta 80 para a porta 8000 do container.

## Consequências
- **Positivas:** configuração mais simples para um cenário com um único serviço exposto; menos componentes para operar e manter (sem Ingress Controller adicional); adequado ao estágio atual do projeto, com uma única API.
- **Negativas:** menos flexível caso o projeto cresça para múltiplos serviços/domínios, já que um LoadBalancer dedicado por serviço tende a ser mais caro e menos gerenciável do que um Ingress compartilhado; ausência de recursos de roteamento avançado (path-based routing, TLS termination centralizada) que um Ingress ofereceria.
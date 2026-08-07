# ADR 0003: Granularidade da Aplicação — Monolito Modular

- **Status:** Aprovado
- **Data:** 2026-08-05

## Contexto
Era necessário decidir se a API de Pedidos deveria ser estruturada como um monolito único, organizado em camadas internas, ou dividida em múltiplos microsserviços (ex.: serviço de pedidos separado do serviço de itens).

## Decisão
Optou-se por manter a aplicação como um Monolito Modular em Camadas — uma única API FastAPI, com responsabilidades de pedidos e itens organizadas em módulos internos (rotas, modelos, acesso a dados), implantada como um único Deployment no Kubernetes.

## Consequências
- **Positivas:** menor complexidade operacional e de deploy para o estágio atual do projeto; comunicação entre "pedidos" e "itens" ocorre em memória/processo, sem latência de rede entre serviços; mais simples de escalar horizontalmente como uma unidade (via HPA).
- **Negativas:** menor isolamento de falhas — um problema em qualquer parte do código afeta toda a API; caso o domínio cresça significativamente, uma futura migração para microsserviços exigiria refatoração para separar contextos (ex.: pedidos vs. itens vs. catálogo).
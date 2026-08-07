# ADR 0001: Banco de Dados Gerenciado Fora do Cluster Kubernetes

- **Status:** Aprovado
- **Data:** 2026-08-05

## Contexto
A API de Pedidos exige persistência relacional (pedidos e itens) com confiabilidade e backup. Era necessário decidir entre rodar o PostgreSQL como um Pod/StatefulSet dentro do cluster Kubernetes, ou consumir o banco como serviço gerenciado (DBaaS) fora do cluster.

## Decisão
Optou-se por utilizar o PostgreSQL como serviço gerenciado (DBaaS) da Magalu Cloud, externo ao cluster. A aplicação se conecta via variável `DATABASE_URL`, injetada por um Secret do Kubernetes (`db-secret`), sem que o banco seja provisionado como recurso do próprio cluster.

## Consequências
- **Positivas:** elimina a complexidade de gerenciar volumes persistentes e StatefulSets no Kubernetes; backups e failover ficam sob responsabilidade do provedor; menor risco operacional para um time pequeno.
- **Negativas:** custo direto do serviço gerenciado (em vez de usar apenas armazenamento local); dependência de disponibilidade e latência de rede até o serviço externo; menor controle sobre configurações finas do PostgreSQL.
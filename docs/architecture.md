**Aluno:** Elbia Simone Buglio
**Repositório do Projeto:** https://github.com/Elbiabuglio/move-tech-cloud-application-comp-6-cloud-architecture-documentation

# Documentação de Arquitetura da Solução

## 1. Mapeamento de Recursos (Cluster & Cloud)

**Recursos no Cluster Kubernetes (Magalu Cloud):**
- **API de Pedidos** (`cloud-application`) — Deployment FastAPI, 2 a 6 réplicas, porta do container 8000
- **Service** (`cloud-application`) — tipo LoadBalancer, expõe a porta 80 externamente, roteando para 8000
- **HorizontalPodAutoscaler** (`cloud-application`) — escala por CPU (alvo 70% de utilização, min 2 / max 6 réplicas)
- **ServiceMonitor** (`cloud-application`) — integração com Prometheus Operator, coleta métricas em `/metrics` a cada 15s

**Serviços Externos / Gerenciados:**
- **Banco de Dados PostgreSQL Gerenciado** (Magalu Cloud DBaaS) — acessado via `DATABASE_URL` injetada por Secret (`db-secret`)
- **Magalu Cloud Container Registry (MCR)** — armazena as imagens Docker publicadas pelo pipeline
- **GitHub Actions (CI/CD)** — pipeline `deploy.yml`: testa (pytest) → builda a imagem → publica no registry → aplica os manifests via `kubectl`

## 2. Diagrama C2 (Nível de Containers)

```mermaid
graph TD
    User([Usuário / Cliente]) -->|HTTP/HTTPS| LB[Service - LoadBalancer<br/>Porta 80]
    LB -->|TCP / Porta 8000| API[API de Pedidos - FastAPI<br/>Deployment - 2 a 6 réplicas]
    API -->|TCP / TLS / Porta 5432| DB[(PostgreSQL Gerenciado<br/>Magalu Cloud DBaaS)]
    Prometheus[Prometheus Operator] -->|HTTP Scrape / Porta 8000<br/>path /metrics a cada 15s| API
    HPA[HorizontalPodAutoscaler] -.->|Monitora uso de CPU| API
    CI[GitHub Actions - CI/CD] -->|Push de imagem| Registry[(Container Registry<br/>Magalu Cloud)]
    Registry -->|Pull da imagem| API
```

## 3. Estilo Arquitetural e RNFs

**Estilo Arquitetural:** Monolito Modular em Camadas, com implantação Cloud-Native em contêineres (Kubernetes), escalonamento horizontal automático via HPA e observabilidade integrada via Prometheus.

- **Disponibilidade Alvo (SLA):** 99.5%
- **Latência P95:** ≤ 500 ms
- **Vazão Alvo (RPS):** compatível com 20 VUs simultâneos (padrão do teste de carga k6), escalável via HPA até 6 réplicas
- **Teto de Custo (FinOps):** R$ 150,00/mês (estimativa de referência para a operação da solução)
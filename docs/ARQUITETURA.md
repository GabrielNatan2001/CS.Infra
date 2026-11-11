# Diagrama de Arquitetura — Conexão Solidária

```mermaid
flowchart LR
  Client[Postman_Swagger] --> Kong[Kong_Gateway]
  Kong -->|/identity| Identity[CS.Identity_API]
  Kong -->|/campaigns| Campaigns[CS.Campaigns_API]
  Identity --> PgId[(PostgreSQL_cs_identity)]
  Campaigns --> PgCamp[(PostgreSQL_cs_campaigns)]
  Campaigns -->|DoacaoRecebidaEvent| RMQ[(RabbitMQ)]
  RMQ --> Worker[CS.Donations_Worker]
  Worker --> PgCamp
  Identity --> Metrics["/metrics_/health"]
  Campaigns --> Metrics
  Metrics --> Prom[Prometheus]
  Prom --> Grafana[Grafana]
  Zabbix[Zabbix] --> Hosts[CPU_Memoria]
```

## Microsserviços

| Serviço | Responsabilidade |
|---|---|
| CS.Identity | Cadastro de doador, login JWT, RBAC |
| CS.Campaigns | Campanhas, painel público, intenção de doação |
| CS.Donations | Worker que atualiza ValorTotalArrecadado |
| CS.Infra | Compose, K8s, Kong, observabilidade |

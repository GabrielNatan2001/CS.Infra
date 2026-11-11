# Justificativa de escolha dos bancos de dados

## PostgreSQL — CS.Identity (`cs_identity`)

Armazena usuários (doadores e gestores), credenciais com hash BCrypt, CPF e papéis RBAC.

**Motivos:**
- Dados relacionais com restrições de unicidade (email)
- Integridade ACID para autenticação
- Excelente suporte via EF Core / Npgsql no .NET 8
- Adequado a consultas transacionais simples e previsíveis

## PostgreSQL — CS.Campaigns (`cs_campaigns`)

Armazena campanhas, doações e o valor total arrecadado atualizado pelo Worker.

**Motivos:**
- Consistência entre intenção de doação (`Pendente`/`Processada`) e saldo da campanha
- Database-per-service: isolamento do bounded context de arrecadação em relação à identidade
- O Worker CS.Donations acessa a mesma base para aplicar o incremento de forma idempotente

## RabbitMQ (broker de mensageria)

Não é banco de dados de negócio, mas é o barramento obrigatório do fluxo assíncrono:

1. API Campaigns publica `DoacaoRecebidaEvent`
2. Worker consome a fila e atualiza `ValorTotalArrecadado`

**Motivos:**
- Desacopla a API do processamento
- UI Management facilita demonstração
- Topologia exchange/queue/routing key clara e durável

## Resumo

| Store | Papel |
|---|---|
| PostgreSQL Identity | Usuários e autenticação |
| PostgreSQL Campaigns | Campanhas e doações |
| RabbitMQ | Eventos assíncronos de doação |

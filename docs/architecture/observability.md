# Observability — HabilitaGO

## Status Atual

A plataforma está em fase inicial de observabilidade. O foco atual é ter logs estruturados e health checks. Métricas avançadas e alertas são roadmap da Phase 3.

---

## Health Checks

Endpoints públicos para monitoramento:

| Endpoint | Descrição |
|----------|-----------|
| `GET /health` | Status básico da aplicação |
| `GET /api/health` | Status da API + conectividade com DB |

Resposta padrão:
```json
{
  "status": "ok",
  "timestamp": "2026-03-15T12:00:00Z",
  "db": "connected",
  "version": "1.0.0"
}
```

---

## Logging

### Estrutura de Log

Logs estruturados (JSON) com campos padrão:

```json
{
  "level": "info",
  "timestamp": "2026-03-15T12:00:00Z",
  "requestId": "uuid",
  "userId": "uuid-or-null",
  "action": "booking.created",
  "target": "lesson:uuid",
  "duration_ms": 45,
  "ip": "[REDACTED]"
}
```

### Eventos Auditados

Todos os eventos críticos são logados para trilha de auditoria:

| Evento | Dados Logados |
|--------|--------------|
| Autenticação (login/logout) | userId, IP, resultado |
| Mudança de papel/permissão | actor, target, mudança |
| Booking criado/cancelado | lessonId, student, instructor |
| Pagamento processado | paymentId, provider, status, amount |
| Saque solicitado | instructorId, amount |
| Instructor aprovado/reprovado | adminId, instructorId, decision |
| Ticket de suporte atualizado | ticketId, actor, status |

### Política de Redação

**Nunca logamos:**
- Senhas ou hashes de senha
- Tokens de sessão
- Dados de cartão (PAN, CVV)
- CPF/documentos em plaintext
- Dados pessoais sensíveis em produção

---

## Roadmap de Observabilidade (Phase 3)

### Métricas

Implementação planejada com **Prometheus + Grafana** ou plataforma gerenciada:

- Latência por endpoint (p50, p95, p99)
- Taxa de erros por rota
- Throughput (req/s)
- Tamanho das filas de workers
- Tempo de processamento de notificações/e-mails

### Alertas

| Alert | Condição | Severidade |
|-------|----------|-----------|
| High error rate | >5% erros em 5min | Critical |
| DB latency | p95 > 500ms | Warning |
| Worker queue backlog | >1000 itens PENDING | Warning |
| Payment webhook failure | 3+ falhas consecutivas | Critical |
| Health check failing | `/health` retorna erro | Critical |

### Distributed Tracing

Implementação futura com **OpenTelemetry** para rastrear requests através de:
- API → DB queries
- API → Stripe/MercadoPago
- Worker → e-mail provider

---

## Infraestrutura de Deploy

| Componente | Plataforma | Monitoramento |
|-----------|-----------|--------------|
| API + Web | Railway | Railway metrics + logs |
| DB | Neon (PostgreSQL) | Neon dashboard |
| E-mail | Resend | Resend dashboard |
| Pagamentos | Stripe | Stripe dashboard |

# Notifications — HabilitaGO

## Overview

Sistema de notificações assíncrono baseado em padrão **Outbox**: eventos são gravados no banco e processados por worker em background.

---

## Arquitetura

```
Evento de domínio (ex: booking confirmado)
  └── Grava registro em `notifications` (status: PENDING)
      └── Notification Worker (polling a cada N segundos)
          └── Processa notificações PENDING
              ├── Push notification (FCM / APNs) → stub atual
              └── Marca como SENT ou FAILED
```

### Por que Outbox?

- **Atomicidade**: Notificação gravada na mesma transação do evento
- **At-least-once delivery**: Worker reprocessa falhas
- **Sem perda**: Mesmo com restart do worker, eventos pendentes são reprocessados
- **Idempotência**: `FOR UPDATE SKIP LOCKED` evita processamento duplicado

---

## Tipos de Notificação

| Tipo | Gatilho | Destinatário |
|------|---------|-------------|
| `BOOKING_CONFIRMED` | Aluno confirma aula | Aluno + Instrutor |
| `BOOKING_CANCELLED` | Aula cancelada | Aluno + Instrutor |
| `PAYMENT_CONFIRMED` | Webhook de pagamento | Aluno |
| `LESSON_REMINDER` | 24h antes da aula | Aluno + Instrutor |
| `REVIEW_REQUEST` | Aula concluída | Aluno |
| `INSTRUCTOR_APPROVED` | Admin aprova instrutor | Instrutor |
| `PAYOUT_PROCESSED` | Saque processado | Instrutor |
| `SUPPORT_UPDATE` | Ticket atualizado | Usuário |

---

## Canais

| Canal | Status | Detalhes |
|-------|--------|---------|
| **In-app (inbox)** | ✅ Produção | Notificações visíveis no app |
| **E-mail** | ✅ Produção | Via Resend (sistema separado) |
| **Push (FCM/APNs)** | 🔄 Stub | Implementação completa na Phase 2 |
| **SMS** | 📅 Roadmap | OTP e lembretes críticos (Phase 2) |

---

## Worker de Notificações

```typescript
// Polling interval: configurável por variável de ambiente
// Batch size: até 50 notificações por ciclo
// Retry: até 3 tentativas com backoff exponencial
// Dead letter: após 3 falhas → status FAILED, alertado para admin
```

### Processamento

```sql
-- Seleciona próximas notificações para processar (sem lock contention)
SELECT * FROM notifications
WHERE status = 'PENDING'
ORDER BY created_at ASC
LIMIT 50
FOR UPDATE SKIP LOCKED;
```

---

## E-mail Transacional (Sistema Separado)

O sistema de e-mail usa uma tabela `email_messages` própria com lifecycle completo:

```
PENDING → (worker tenta envio) → SENT
                               → FAILED (com retry)
                               → CANCELLED (se usuário desativou)
```

Templates disponíveis:
- `booking_confirmation`
- `payment_receipt`
- `password_reset`
- `email_verification`
- `instructor_approved`
- `lesson_reminder`

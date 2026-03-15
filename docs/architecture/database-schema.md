# Database Schema — HabilitaGO

## Overview

PostgreSQL via Neon (serverless). ORM: Drizzle (type-safe, schema-first).

Todas as migrações são explícitas — sem `db:push` em produção exceto bootstrap inicial.

---

## Entidades Principais

### `users`

Tabela central. Papéis: `STUDENT`, `INSTRUCTOR`, `ADMIN`, `SUPER_ADMIN`.

```
users
├── id (uuid, PK)
├── email (unique, not null)
├── password_hash
├── role: enum(STUDENT, INSTRUCTOR, ADMIN, SUPER_ADMIN)
├── email_verified_at
├── created_at / updated_at
└── deleted_at (soft delete)
```

### `instructor_profiles`

Extensão de `users` para instrutores.

```
instructor_profiles
├── id (uuid, PK)
├── user_id (FK → users)
├── display_name, bio, photo_url
├── approval_status: enum(PENDENTE_ENVIO_DADOS, AGUARDANDO_ANALISE, APROVADO, REPROVADO, SUSPENSO)
├── city, state
├── price_per_lesson (integer, centavos)
├── vehicle_types: array(AUTOMATICO, MANUAL, MOTO)
├── rating_average, rating_count
├── stripe_account_id (Connect)
└── documents_submitted_at, approved_at
```

### `lessons`

Agendamentos e histórico de aulas.

```
lessons
├── id (uuid, PK)
├── student_id (FK → users)
├── instructor_id (FK → users)
├── status: enum(PENDING, CONFIRMED, COMPLETED, CANCELLED, REFUNDED)
├── scheduled_at (timestamptz)
├── duration_minutes
├── price_charged (integer, centavos)
├── vehicle_type
├── pickup_location
└── completed_at, cancelled_at
```

### `payments`

Registro de transações financeiras.

```
payments
├── id (uuid, PK)
├── lesson_id (FK → lessons)
├── provider: enum(STRIPE, MERCADOPAGO)
├── provider_reference (ID externo)
├── amount, currency
├── status: enum(PENDING, CAPTURED, FAILED, REFUNDED)
├── webhook_received_at
└── idempotency_key
```

### `wallet_transactions`

Ledger financeiro por instrutor.

```
wallet_transactions
├── id (uuid, PK)
├── instructor_id (FK → users)
├── type: enum(CREDIT, DEBIT, PAYOUT)
├── amount (integer, centavos)
├── reference_payment_id
├── stripe_transfer_id
└── created_at
```

### `reviews`

Avaliações de alunos sobre instrutores.

```
reviews
├── id (uuid, PK)
├── lesson_id (FK → lessons, unique)
├── student_id (FK → users)
├── instructor_id (FK → users)
├── rating (1–5)
├── comment
└── created_at
```

### `notifications`

Inbox de notificações dos usuários.

```
notifications
├── id (uuid, PK)
├── user_id (FK → users)
├── type (string)
├── title, body
├── data (jsonb)
├── read_at
└── created_at
```

### `email_messages`

Fila de e-mail transacional com lifecycle completo.

```
email_messages
├── id (uuid, PK)
├── to_email, subject
├── template_key, template_data (jsonb)
├── status: enum(PENDING, SENT, FAILED, CANCELLED)
├── provider_message_id
├── attempts, last_attempted_at
└── sent_at, failed_at
```

### `cities` / `states`

Dados geográficos para SEO.

```
cities
├── id, slug (unique)
├── name, state_id
├── latitude, longitude
└── instructor_count (denormalizado)
```

### `support_tickets`

Suporte ao usuário.

```
support_tickets
├── id (uuid, PK)
├── user_id (FK → users)
├── subject, description
├── status: enum(OPEN, IN_PROGRESS, RESOLVED, CLOSED)
├── priority
└── resolved_at
```

---

## Padrões de Design

- **UUIDs** como PKs em todas as tabelas (sem integer sequencial exposto)
- **Soft delete** em `users` (deleted_at)
- **Valores monetários** em centavos (integer) — nunca float
- **Timestamps** com timezone (`timestamptz`)
- **JSONB** para dados flexíveis (notificações, templates de e-mail)
- **FOR UPDATE SKIP LOCKED** nos workers de outbox

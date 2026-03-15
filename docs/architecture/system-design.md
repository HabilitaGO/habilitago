# System Design — HabilitaGO

## Visão Geral

HabilitaGO é um monorepo TypeScript com três camadas principais: frontend web, API backend e banco de dados. A experiência principal do usuário (aluno e instrutor) é mobile-first via app React Native/Expo.

---

## Diagrama de Arquitetura

```mermaid
graph TB
    subgraph Clientes
        APP[📱 App Mobile<br/>React Native/Expo]
        WEB[🌐 Web Admin<br/>React 18]
        SEO[🔍 Landing/SEO Pages<br/>React 18 + SSR-friendly]
    end

    subgraph API["API Layer (Node.js + Express)"]
        V1["/api/v1/* <br/> Mobile API"]
        LEGACY["/api/* <br/> Legacy Compat"]
        ADMIN["/api/admin/* <br/> Backoffice"]
        HEALTH["/health <br/> /api/health"]
    end

    subgraph Modules["Módulos de Domínio"]
        AUTH[Auth & Session]
        BOOKING[Booking & Lessons]
        PAYMENTS[Payments]
        NOTIF[Notifications]
        EMAIL[Email]
        SUPPORT[Support & Tickets]
        SEO_MOD[SEO & Cities]
        ADMIN_MOD[Admin & Moderation]
    end

    subgraph Workers["Background Workers"]
        NOTIF_W[Notification Worker<br/>outbox polling]
        EMAIL_W[Email Worker<br/>DB queue]
    end

    subgraph Infra["Infraestrutura"]
        DB[(PostgreSQL<br/>Neon)]
        STRIPE[Stripe<br/>+ Connect]
        MP[MercadoPago<br/>PIX]
        RESEND[Resend<br/>Email]
        S3[S3-compatible<br/>Storage]
    end

    APP -->|HTTPS| V1
    WEB -->|HTTPS| ADMIN
    SEO -->|HTTPS| V1
    SEO -->|HTTPS| LEGACY

    V1 --> Modules
    LEGACY --> Modules
    ADMIN --> ADMIN_MOD

    Modules --> DB
    PAYMENTS --> STRIPE
    PAYMENTS --> MP
    EMAIL --> RESEND
    SUPPORT --> S3

    NOTIF_W --> DB
    EMAIL_W --> DB
    EMAIL_W --> RESEND
```

---

## Componentes

### Client Layer

| Componente | Tecnologia | Responsabilidade |
|-----------|-----------|-----------------|
| App Mobile | React Native + Expo | Experiência principal do aluno e instrutor |
| Web Admin | React 18 + TypeScript | Dashboard de operações e moderação |
| Landing/SEO | React 18 + SSR-friendly | Páginas públicas e SEO por cidade/estado |

### API Layer

```
server/
├── index.ts              # Entry point, Express setup
├── routes.ts             # Router principal
├── routes/
│   ├── v1/               # API mobile versionada
│   ├── admin/            # Rotas do backoffice
│   └── mobile-compat.ts  # Compatibilidade mobile legada
├── modules/
│   ├── notifications/    # Controller + Service + Repo + Worker
│   └── email/            # Controller + Service + Repo + Worker
├── services/
│   └── storage/          # Adapter: Local + S3
├── config/               # Configuração centralizada
└── env/                  # Validação de variáveis de ambiente
```

### Database Layer

PostgreSQL via Neon (serverless), acessado com Drizzle ORM (type-safe, sem raw SQL fora de migrações).

**Principais entidades:**
- `users` — alunos, instrutores, admins
- `instructor_profiles` — perfis públicos e privados de instrutores
- `lessons` — agendamentos e histórico de aulas
- `payments` / `wallet_transactions` — ledger financeiro
- `reviews` — avaliações dos alunos
- `notifications` — inbox e campanhas
- `email_messages` — fila de e-mail com lifecycle
- `cities` / `states` — dados geográficos para SEO
- `support_tickets` / `ticket_attachments` — suporte

---

## Fluxos Críticos

### Booking (Aluno → Aula)

```mermaid
sequenceDiagram
    participant Aluno
    participant API
    participant DB
    participant Stripe

    Aluno->>API: POST /api/v1/lessons (booking intent)
    API->>DB: Valida disponibilidade do instrutor
    API->>Stripe: Cria PaymentIntent
    Stripe-->>API: client_secret
    API-->>Aluno: client_secret + lesson_id

    Aluno->>Stripe: Confirma pagamento (client-side SDK)
    Stripe->>API: Webhook: payment_intent.succeeded
    API->>DB: Confirma lesson, cria wallet_transaction
    API->>DB: Enfileira notificação para aluno e instrutor
```

### Onboarding do Instrutor

```mermaid
stateDiagram-v2
    [*] --> PENDENTE_ENVIO_DADOS: Cadastro
    PENDENTE_ENVIO_DADOS --> AGUARDANDO_ANALISE: Envia documentação
    AGUARDANDO_ANALISE --> APROVADO: Admin aprova
    AGUARDANDO_ANALISE --> REPROVADO: Admin reprova
    REPROVADO --> PENDENTE_ENVIO_DADOS: Reinicia com correções
    APROVADO --> [*]: Ativo na plataforma
```

---

## Decisões de Arquitetura

### Por que monorepo?
Compartilhamento de tipos, schemas Zod e validações entre frontend e backend sem duplicação. Garante contratos de API type-safe.

### Por que Drizzle ORM?
Type-safety completa com inferência de tipos do schema. SQL legível, sem magic. Migrations explícitas.

### Por que PostgreSQL (Neon)?
Transações ACID para pagamentos e bookings. Neon serverless reduz overhead operacional com escala automática.

### Por que workers separados?
Notificações e e-mails são assíncronos por natureza. Workers com outbox polling + `FOR UPDATE SKIP LOCKED` garantem at-least-once delivery sem duplicação, mesmo com múltiplas instâncias.

### Por que Stripe Connect?
Modelo de marketplace: HabilitaGO captura o pagamento e repassa para o instrutor. Stripe Connect gerencia compliance financeiro (KYC) do lado do instrutor.

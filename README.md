<div align="center">

# 🚗 HabilitaGO

**O marketplace que conecta alunos e instrutores de autoescola no Brasil**

[![Website](https://img.shields.io/badge/website-habilitago.com-orange?style=for-the-badge)](https://www.habilitago.com)
[![Platform](https://img.shields.io/badge/platform-live-brightgreen?style=for-the-badge)](https://www.habilitago.com)
[![License](https://img.shields.io/badge/license-Proprietary-red?style=for-the-badge)](./LICENSE)

[![Node.js](https://img.shields.io/badge/Node.js-20.x-brightgreen?logo=node.js)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue?logo=typescript)](https://www.typescriptlang.org/)
[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react)](https://react.dev/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Neon-4169E1?logo=postgresql)](https://neon.tech/)
[![Stripe](https://img.shields.io/badge/Payments-Stripe%20%2B%20PIX-635BFF?logo=stripe)](https://stripe.com/)

</div>

---

## O que é o HabilitaGO?

HabilitaGO é um **marketplace mobile-first** que conecta alunos que querem tirar a carteira de motorista com instrutores autônomos certificados em todo o Brasil — de forma simples, segura e transparente.

Pense no modelo do Uber ou iFood: o aluno abre o app, encontra instrutores próximos com avaliações reais, escolhe um horário, paga pelo app (cartão ou PIX) e vai para a aula. O instrutor gerencia sua agenda, recebe pela plataforma e constrói sua reputação digital.

> **Produto em produção.** A plataforma está no ar em [habilitago.com](https://www.habilitago.com), com app mobile em desenvolvimento e instrutores cadastrados.

---

## O Problema

Tirar carteira de motorista no Brasil é um processo caro, opaco e fragmentado:

- **Nenhuma transparência de preços** — cada autoescola cobra o que quer, sem comparação
- **Difícil encontrar instrutores de confiança** — o mercado é informal, sem avaliações públicas
- **Agendamento arcaico** — telefonemas, WhatsApp, papel
- **Pagamento em dinheiro** — sem rastreabilidade, sem proteção ao consumidor
- **Mercado concentrado** — autoescolas grandes controlam a oferta, excluindo instrutores independentes

Segundo o DETRAN, mais de **4 milhões de brasileiros** fazem o processo de habilitação a cada ano. É um mercado de R$ 20 bilhões/ano ainda sem um player digital dominante.

### O Momento Regulatório

A abertura do mercado para **instrutores autônomos** é a grande oportunidade do HabilitaGO. Com as mudanças regulatórias recentes do CTB, instrutores certificados pelo DETRAN podem atuar de forma independente — criando um enorme pool de oferta que precisa de uma plataforma para se conectar à demanda.

---

## A Solução

HabilitaGO cria a infraestrutura digital que este mercado nunca teve:

| Pilar | O que entregamos |
|-------|-----------------|
| **Descoberta** | Alunos encontram instrutores por cidade, com fotos, avaliações, preços e disponibilidade |
| **Agendamento** | Fluxo de booking multi-step com seleção de veículo, horário e confirmação instantânea |
| **Pagamentos** | Stripe (cartão) + MercadoPago PIX — dinheiro digital, sem fricção |
| **Confiança** | Perfis verificados, avaliações reais, histórico de aulas |
| **Gestão** | Painel completo para o instrutor: agenda, pagamentos, alunos, perfil |
| **Operação** | Dashboard admin com controle total da plataforma |

---

## Como Funciona

### Para o Aluno

```
1. Baixa o app  →  2. Busca instrutores na cidade  →  3. Vê perfil + avaliações + preço
       ↓
4. Escolhe horário disponível  →  5. Paga pelo app (cartão ou PIX)  →  6. Vai para a aula
       ↓
7. Avalia o instrutor  →  8. Histórico disponível para próximas contratações
```

### Para o Instrutor

```
1. Cadastra-se e envia documentação  →  2. Aprovado pela equipe HabilitaGO
       ↓
3. Configura perfil, preço e disponibilidade  →  4. Recebe solicitações de alunos
       ↓
5. Confirma agendamentos  →  6. Ministra as aulas
       ↓
7. Recebe pagamento na carteira digital  →  8. Saque via Stripe Connect
```

---

## Funcionalidades

### App Mobile (Aluno & Instrutor)
- Descoberta de instrutores com filtros por cidade, preço e disponibilidade
- Perfis completos com avaliações, fotos e certificações
- Agendamento multi-step com seleção de veículo
- Pagamentos: cartão de crédito (Stripe) e PIX (MercadoPago)
- Chat em tempo real com WebSocket
- Notificações push (FCM/APNs)
- Histórico de aulas e pagamentos
- Suporte com tickets

### Painel do Instrutor
- Gerenciamento de disponibilidade e agenda
- Controle de pacotes e precificação
- Relatório de pagamentos e saldo via Stripe Connect

### Admin Dashboard
- Moderação e aprovação de instrutores
- Monitoramento de transações e webhooks
- Observabilidade de e-mails e notificações
- Controles de segurança e auditoria
- SEO e gestão de conteúdo por cidade/estado

---

## Arquitetura

```
┌──────────────────────────────────────────────────────────────┐
│                        CLIENTES                               │
│   📱 App Mobile (React Native/Expo)  🌐 Web Admin (React 18) │
└────────────────────────┬─────────────────────────────────────┘
                         │ HTTPS / WebSocket
┌────────────────────────▼─────────────────────────────────────┐
│                    API (Node.js + Express)                     │
│  /api/v1/*  ·  /api/*  ·  /api/admin/*  ·  /health           │
│  Auth · Booking · Payments · Notifications · Email · SEO      │
└────┬──────────────┬──────────────┬──────────────┬────────────┘
     │              │              │              │
┌────▼────┐  ┌──────▼──────┐ ┌────▼────┐  ┌──────▼────┐
│PostgreSQL│  │   Stripe    │ │MercadoPg│  │  Resend   │
│  (Neon) │  │ + Connect   │ │  (PIX)  │  │  (Email)  │
└─────────┘  └─────────────┘ └─────────┘  └───────────┘

Workers: 📬 Notification Worker  ·  📧 Email Worker
```

> Arquitetura detalhada: [`docs/architecture/system-design.md`](./docs/architecture/system-design.md)

---

## Stack Tecnológica

| Camada | Tecnologias |
|--------|-------------|
| Frontend | React 18, TypeScript, TanStack Query, Tailwind CSS, Shadcn/UI |
| Backend | Node.js 20, Express, TypeScript strict, Drizzle ORM, Zod |
| Banco | PostgreSQL (Neon serverless) |
| Pagamentos | Stripe + Stripe Connect, MercadoPago (PIX) |
| Email | Resend (transacional + fila + webhooks) |
| Infra | Railway, S3-compatible storage |

---

## Segurança & Compliance

- **Criptografia em repouso** — dados sensíveis criptografados (modo local e KMS)
- **LGPD** — coleta mínima, políticas públicas, direito de exclusão em [`/excluir-conta`](https://www.habilitago.com/excluir-conta)
- **Auth segura** — sessões HttpOnly, verificação de e-mail, OTP para reset
- **RBAC** — STUDENT, INSTRUCTOR, ADMIN, SUPER_ADMIN
- **PCI DSS** — dados de cartão nunca armazenados (Stripe)
- **Audit trail** — eventos críticos com ator, alvo e timestamp
- **Rate limiting** — proteção em endpoints sensíveis

Reporte vulnerabilidades: [`SECURITY.md`](./SECURITY.md)

---

## Roadmap

| Fase | Status | Objetivos |
|------|--------|-----------|
| Phase 1 — MVP | ✅ Em produção | Marketplace, booking, pagamentos, chat |
| Phase 2 — Hardening | 🔄 Em andamento | Segurança, compliance, criptografia |
| Phase 3 — Escala | 📅 Planejado | Rate limiting, observabilidade, cache |
| Phase 4 — Mobile Native | 📅 Planejado | App iOS/Android, push nativo |
| Phase 5 — Expansão | 📅 Planejado | Nacional, integrações DETRAN |

> [`ROADMAP.md`](./ROADMAP.md) — roadmap completo

---

## Setup para Desenvolvedores

```bash
git clone https://github.com/HabilitaGO/habilitago.git
cd habilitago
npm install
cp .env.example .env   # configure suas credenciais
npm run db:push
npm run dev            # → http://localhost:5000
```

---

## Links

| | |
|--|--|
| 🌐 Website | [habilitago.com](https://www.habilitago.com) |
| 📧 Contato | [contato@habilitago.com](mailto:contato@habilitago.com) |
| 💼 Investidores | [pedro.amigoos@gmail.com](mailto:pedro.amigoos@gmail.com) |
| 🔒 Segurança | [security@habilitago.com](mailto:security@habilitago.com) |

---

## Licença

© 2025-2026 HabilitaGO. Todos os direitos reservados.
Software proprietário — veja [`LICENSE`](./LICENSE).

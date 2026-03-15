# Pagamentos — HabilitaGO

## Provedores

| Provedor | Método | Região |
|---------|--------|--------|
| Stripe | Cartão de crédito/débito | Global |
| Stripe Connect | Repasse para instrutores | Global |
| MercadoPago | PIX | Brasil |

## Fluxo Stripe (Cartão)

```
1. Cliente inicia pagamento
2. Backend cria PaymentIntent (server-side)
3. Frontend usa Stripe SDK para confirmar (client-side)
4. Stripe envia webhook: payment_intent.succeeded
5. Backend confirma pagamento, credita carteira do instrutor
6. Instrutor solicita saque → Stripe Connect → conta bancária
```

## Fluxo PIX (MercadoPago)

```
1. Backend cria preference MercadoPago
2. Frontend exibe QR Code ou copia-e-cola
3. Aluno paga via app do banco
4. MercadoPago envia webhook: payment.approved
5. Backend confirma, credita carteira do instrutor
```

## Segurança

- Dados de cartão **nunca passam pelo backend** — Stripe processa diretamente
- Webhooks validados por assinatura (`stripe.webhooks.constructEvent`)
- Webhook UUIDs internos para idempotência
- Reconciliação de status via webhooks (nunca confiar só no frontend)

## Regras Financeiras

- Pagamento capturado no momento do booking
- Cancelamento: política definida operacionalmente (em desenvolvimento)
- Reembolso: processado via Stripe/MercadoPago API pelo admin

## Auditoria

Todos os eventos financeiros geram registros imutáveis com:
- `provider_reference` (ID do Stripe/MercadoPago)
- `amount`, `currency`, `status`
- `timestamp` e `actor`

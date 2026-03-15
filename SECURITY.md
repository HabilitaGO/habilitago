# Security Policy — HabilitaGO

## Reporte de Vulnerabilidades

**Não abra issues públicas para vulnerabilidades de segurança.**

Se você descobriu uma vulnerabilidade, por favor reporte de forma responsável:

- **E-mail:** [security@habilitago.com](mailto:security@habilitago.com)
- **GitHub:** [Security Advisories](https://github.com/HabilitaGO/habilitago/security/advisories/new) (privado)

Inclua no reporte:
- Descrição detalhada da vulnerabilidade
- Passos para reproduzir
- Impacto potencial
- Sugestão de correção (opcional)

Respondemos em até **72 horas** e trabalhamos com disclosure responsável (geralmente 90 dias).

---

## Postura de Segurança

### Autenticação e Acesso
- Sessões seguras com cookies `HttpOnly` + `SameSite`
- Verificação de e-mail obrigatória no cadastro
- OTP via e-mail para reset de senha
- RBAC com papéis: `STUDENT`, `INSTRUCTOR`, `ADMIN`, `SUPER_ADMIN`
- Todas as rotas protegidas por autenticação + verificação de papel e ownership

### Dados e Criptografia
- Dados sensíveis criptografados em repouso (`CRYPTO_MODE=local` ou `kms`)
- Dados de cartão **nunca armazenados** na plataforma (delegado ao Stripe — PCI DSS Level 1)
- HTTPS obrigatório em produção
- Segredos gerenciados via variáveis de ambiente, nunca hardcoded

### Pagamentos
- Stripe para cartão: Payment Intents, sem armazenar PAN
- MercadoPago para PIX: preferências geradas server-side
- Webhooks com verificação de assinatura (`stripe.webhooks.constructEvent`)
- Identificadores internos de webhook para idempotência

### Proteções de API
- Rate limiting por endpoint em rotas sensíveis (auth, reset, webhooks)
- Validação de input com Zod em todas as entradas da API
- CORS configurado com allowlist explícita (sem wildcard em produção)
- Headers de segurança padrão

### Auditoria
- Eventos críticos registrados: auth, mudanças de papel, transições de pagamento, ações de moderação
- Logs contêm: ator, alvo, timestamp, tipo de ação, metadados redatados
- Dados sensíveis nunca logados em plaintext

### Compliance
- LGPD: coleta mínima, finalidade declarada, políticas públicas
- Páginas públicas: `/privacidade`, `/termos`, `/cookies`, `/excluir-conta`
- Direito de exclusão: usuários podem solicitar exclusão de conta
- Consentimento de cookies gerenciado client-side com versionamento

---

## Versões Suportadas

| Versão | Suporte de Segurança |
|--------|----------------------|
| Produção (main) | ✅ Ativo |
| Versões anteriores | ❌ Sem suporte |

---

## Divulgação

Agradecemos publicamente pesquisadores que reportam vulnerabilidades válidas, mediante concordância.

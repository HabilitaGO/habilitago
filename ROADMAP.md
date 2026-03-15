# Roadmap — HabilitaGO

> Última atualização: Março 2026

Este roadmap é público e reflete a direção atual do produto. Detalhes de implementação e timelines exatos são internos.

---

## Status Atual

**Fase 1 completa — Plataforma em produção.**

O marketplace está no ar com booking, pagamentos (Stripe + PIX), chat em tempo real, painel admin e páginas SEO para cidades brasileiras.

---

## Fases

### ✅ Phase 1 — MVP em Produção

- [x] Marketplace de instrutores com busca e filtros
- [x] Booking multi-step com seleção de veículo e horário
- [x] Pagamentos via cartão (Stripe) e PIX (MercadoPago)
- [x] Chat em tempo real (WebSocket)
- [x] Perfis verificados de instrutores
- [x] Avaliações de alunos
- [x] Admin dashboard (moderação, operação, auditoria)
- [x] SEO local: páginas por cidade e estado
- [x] Sistema de notificações (outbox worker + push stub)
- [x] Sistema de e-mail transacional (Resend + fila + webhooks)
- [x] Autenticação segura (sessão, e-mail, OTP reset)
- [x] Criptografia em repouso
- [x] LGPD: Termos, Privacidade, Cookies, Exclusão de Conta
- [x] Deploy em Railway com domínio próprio

---

### 🔄 Phase 2 — Hardening de Segurança e Confiança

- [ ] Rollout completo da criptografia KMS em produção
- [ ] Telemetria de segurança expandida
- [ ] Rastreabilidade de compliance end-to-end
- [ ] Validação de credenciais de instrução (DETRAN) automatizada
- [ ] Notificações push nativas via FCM (Android) e APNs (iOS)
- [ ] OTP por SMS como segundo fator
- [ ] Relatórios financeiros para instrutores
- [ ] Gestão de cancelamentos e reembolsos

---

### 📅 Phase 3 — Escala e Confiabilidade

- [ ] Rate limiting avançado por endpoint e usuário
- [ ] Observabilidade: dashboards de alertas e métricas de SLA
- [ ] Pipelines de reporting para crescimento
- [ ] Migração para workers dedicados (fila de notificações e e-mail)
- [ ] CDN para assets e uploads
- [ ] Cache de API (Redis)

---

### 📅 Phase 4 — App Mobile Nativo (React Native/Expo)

- [ ] App aluno: iOS e Android na loja
- [ ] App instrutor: versão nativa com agenda completa
- [ ] Deep links para booking e pagamento
- [ ] Notificações push nativas integradas
- [ ] Modo offline para consulta de agenda

---

### 📅 Phase 5 — Expansão e Integrações Regulatórias

- [ ] Integração com sistemas DETRAN (consulta de CNH)
- [ ] Validação de certificações de instrutores em tempo real
- [ ] Expansão para 50+ cidades com instrutores cadastrados
- [ ] Programa de fidelidade para alunos
- [ ] API pública para parceiros (autoescolas, franquias)
- [ ] Modelo B2B: instrutores de frotas e empresas

---

## O Que Não Está no Roadmap (Por Enquanto)

- Simulado teórico integrado (foco é na parte prática)
- Plataforma própria de streaming de video aulas
- Marketplace de veículos

---

## Feedback e Sugestões

Abra uma [feature request](https://github.com/HabilitaGO/habilitago/issues/new?template=feature_request.md) com sua sugestão.

Para parcerias estratégicas: [contato@habilitago.com](mailto:contato@habilitago.com)

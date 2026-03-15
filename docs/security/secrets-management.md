# Secrets Management — HabilitaGO

## Princípios

1. **Zero secrets no código**: Nenhuma credencial, chave ou token no repositório
2. **Variáveis de ambiente**: Único mecanismo de injeção de segredos
3. **Rotação regular**: Chaves rotacionadas após qualquer suspeita de comprometimento
4. **Mínimo acesso**: Cada serviço recebe apenas as credenciais que precisa

---

## Categorias de Segredos

### Credenciais de Banco de Dados

```bash
DATABASE_URL=postgresql://user:pass@host:5432/db?sslmode=require
```

- Usuário dedicado com permissões mínimas (sem superuser)
- Conexão obrigatoriamente via SSL
- Rotação: trimestral ou após incidente

### Chaves de Pagamento

```bash
STRIPE_SECRET_KEY=sk_live_...         # Nunca exposta ao frontend
STRIPE_WEBHOOK_SECRET=whsec_...       # Exclusiva para validar webhooks
MERCADO_PAGO_ACCESS_TOKEN=APP_...     # Server-side apenas
```

- Chaves de produção nunca usadas em desenvolvimento
- Webhook secrets distintos por ambiente
- Acesso restrito ao módulo de pagamentos

### Chaves de Criptografia

```bash
CRYPTO_MODE=local|kms
CRYPTO_LOCAL_KEY=...                  # Apenas em dev/staging
KMS_KEY_ID=arn:aws:kms:...           # Apenas em produção
```

- `CRYPTO_LOCAL_KEY` nunca usada em produção
- KMS ARN configurado por ambiente

### Segredos de Sessão

```bash
SESSION_SECRET=...                    # Mínimo 32 caracteres, alta entropia
```

- Gerado com `openssl rand -hex 32`
- Distinto por ambiente
- Rotação invalida todas as sessões ativas

---

## Ambientes

| Ambiente | Fonte de Segredos | Nível de Acesso |
|----------|-----------------|----------------|
| Local (dev) | `.env` local (gitignored) | Desenvolvedor |
| Staging | Railway env vars | CI/CD + Lead |
| Produção | Railway env vars | Lead + Admin |

### O que NUNCA vai no `.env.example`

- Valores reais de qualquer chave
- Segredos de produção ou staging
- Tokens pessoais de qualquer desenvolvedor

O `.env.example` contém apenas **nomes e descrições** das variáveis necessárias.

---

## Detecção de Vazamentos

- **GitHub Secret Scanning** habilitado no repositório privado
- Pre-commit hooks para detectar padrões de chave antes do commit
- Revisão obrigatória de PR para arquivos `.env*`, `config/`, `*.yml` de CI

### O que fazer se um segredo vazar

```
1. Revogar imediatamente na plataforma (Stripe, AWS, etc.)
2. Gerar novo segredo
3. Atualizar Railway env vars
4. Verificar logs de acesso do período comprometido
5. Reportar internamente e documentar o incidente
6. Se dados de usuários foram expostos → LGPD exige notificação à ANPD
```

---

## Auditoria de Segredos

- Lista de segredos ativos documentada internamente (não pública)
- Revisão semestral de segredos com acesso a produção
- Remoção de segredos obsoletos de serviços descontinuados

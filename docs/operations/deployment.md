# Deployment — HabilitaGO

## Infraestrutura Atual

| Componente | Plataforma | Descrição |
|-----------|-----------|-----------|
| API + Web | Railway | Monorepo com auto-deploy no push para `main` |
| Banco de dados | Neon (PostgreSQL serverless) | Escala automática, branching por PR |
| E-mail transacional | Resend | Templates e webhooks de entrega |
| Pagamentos | Stripe + MercadoPago | Webhooks em produção |
| Storage | S3-compatible | Documentos e uploads |
| Domínio | habilitago.com | DNS gerenciado externamente |

---

## Processo de Deploy

### Deploy Contínuo

```
Push para `main` (GitHub)
  └── Railway detecta push
      └── Build da imagem Docker
          └── Health check passa?
              ├── ✅ Swap de tráfego (zero-downtime)
              └── ❌ Rollback automático para versão anterior
```

### Variáveis de Ambiente

Gerenciadas no painel Railway. Nunca em arquivos commitados.

Deploy requer todas as variáveis de `.env.example` configuradas em produção.

---

## Ambientes

| Ambiente | Branch | URL | Auto-deploy |
|----------|--------|-----|-------------|
| Produção | `main` | habilitago.com | ✅ Sim |
| Staging | `staging` | staging.habilitago.com | ✅ Sim |
| Preview | PR branches | pr-N.railway.app | ✅ Sim (Railway PR Environments) |
| Local | — | localhost:5000 | — |

---

## Migrações de Banco

```bash
# Gerar nova migração
npx drizzle-kit generate

# Aplicar em staging antes de produção
npx drizzle-kit migrate

# Verificar status
npx drizzle-kit status
```

**Regras:**
- Toda migração é revisada em PR antes de aplicar em produção
- Migrações destrutivas (DROP COLUMN, DROP TABLE) requerem aprovação explícita
- Backups automáticos do Neon antes de migrações em produção

---

## Rollback

### Aplicação

Railway mantém histórico de deploys. Rollback em 1 clique no painel.

```bash
# Via Railway CLI (se disponível)
railway rollback
```

### Banco de Dados

- Neon oferece point-in-time recovery
- Backups automáticos a cada hora em produção
- Para rollback de migração: script de reversão criado junto com cada migração

---

## Health Checks e Monitoramento

```bash
# Verificar status da aplicação
curl https://habilitago.com/health

# Resposta esperada
{
  "status": "ok",
  "db": "connected",
  "timestamp": "..."
}
```

Railway usa o endpoint `/health` para determinar se o deploy foi bem-sucedido.

---

## Checklist de Deploy em Produção

- [ ] PR revisado e aprovado
- [ ] CI passa (type check + tests + build)
- [ ] Variáveis de ambiente atualizadas se necessário
- [ ] Migração de banco revisada (se houver)
- [ ] Migração aplicada em staging primeiro
- [ ] Deploy em staging validado manualmente
- [ ] Merge para `main`
- [ ] Health check de produção confirma OK
- [ ] Monitorar logs por 10 minutos pós-deploy

---

## Segurança do Deploy

- Branch `main` protegida: push direto bloqueado, exige PR
- CI obrigatório para merge
- Secrets nunca em código ou logs de build
- Deploy keys do Railway com acesso mínimo necessário

# Auth & Permissions — HabilitaGO

## Autenticação

### Mecanismo
- **Sessões server-side** com cookies `HttpOnly` + `SameSite=Lax`
- Nenhum JWT armazenado no browser (evita XSS token theft)
- Verificação de e-mail obrigatória antes de acessar a plataforma

### Fluxos de Autenticação

```
Cadastro
└── POST /auth/register → cria user (role=STUDENT ou INSTRUCTOR)
    └── Envia e-mail de verificação (link com token de 24h)
        └── GET /auth/verify-email?token=... → ativa conta

Login
└── POST /auth/login → valida email/hash → cria sessão
    └── Retorna cookie de sessão (HttpOnly)

Reset de Senha
└── POST /auth/forgot-password → envia OTP por e-mail
    └── POST /auth/reset-password → valida OTP + hash nova senha

Logout
└── POST /auth/logout → destroi sessão server-side + limpa cookie
```

### Proteção de Rotas

Middleware de autenticação aplicado globalmente:

```typescript
// Toda rota protegida valida:
// 1. Cookie de sessão presente e válido
// 2. Usuário existe no banco
// 3. E-mail verificado
// 4. Conta não deletada (deleted_at IS NULL)
```

---

## Autorização (RBAC)

### Papéis

| Role | Acesso |
|------|--------|
| `STUDENT` | Busca instrutores, agenda aulas, paga, avalia |
| `INSTRUCTOR` | Gerencia perfil, agenda, aulas e carteira |
| `ADMIN` | Modera instrutores, visualiza operações, suporte |
| `SUPER_ADMIN` | Acesso total + configurações do sistema |

### Middleware de Papel

```typescript
// Exemplo de uso:
router.post('/admin/instructors/:id/approve',
  requireAuth,
  requireRole(['ADMIN', 'SUPER_ADMIN']),
  approveInstructor
);
```

### Verificação de Ownership

Além do papel, toda operação verifica ownership do recurso:

```typescript
// Instrutor só pode ver/editar suas próprias aulas
// Aluno só pode cancelar suas próprias aulas
// Admin pode ver todas, mas não pode se escalar
```

---

## Criptografia de Dados Sensíveis

Dados PII sensíveis (CPF, dados bancários, documentos) são criptografados em repouso.

### Modos

| Modo | Descrição |
|------|-----------|
| `CRYPTO_MODE=local` | Chave derivada de variável de ambiente (dev/staging) |
| `CRYPTO_MODE=kms` | AWS KMS + Data Encryption Keys por usuário (produção) |

### Envelope Encryption (KMS)
```
Dado bruto → DEK (Data Encryption Key por usuário) → cifrado com CMK do KMS
Armazenado: { kid, ciphertext } no banco
```

---

## Sessões

- Armazenamento: `express-session` com store PostgreSQL (tabela `sessions`)
- TTL configurável por ambiente
- Invalidação no logout, mudança de senha e após detecção de anomalia

---

## Rate Limiting

Aplicado em rotas sensíveis para prevenir brute force:

| Endpoint | Limite |
|----------|--------|
| `POST /auth/login` | 10 req/min por IP |
| `POST /auth/forgot-password` | 5 req/15min por e-mail |
| `POST /webhooks/*` | 100 req/min por IP |

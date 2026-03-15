# Authentication Policy — HabilitaGO

## Princípios

1. **Zero trust no client**: Toda validação de autenticação ocorre server-side
2. **Cookies seguros**: `HttpOnly` + `SameSite` eliminam acesso JS aos tokens de sessão
3. **Verificação obrigatória**: E-mail deve ser verificado antes de qualquer ação na plataforma
4. **Mínimo privilégio**: Usuários têm acesso apenas ao que seu papel permite

---

## Gerenciamento de Senhas

| Requisito | Política |
|-----------|---------|
| Comprimento mínimo | 8 caracteres |
| Hashing | bcrypt com fator de custo adequado |
| Armazenamento | Apenas o hash — senha nunca persiste |
| Reset | OTP via e-mail (expira em 15 minutos) |
| Histórico | Não reutilização das últimas 3 senhas (roadmap) |

---

## Sessões

| Parâmetro | Valor |
|-----------|-------|
| Armazenamento | PostgreSQL (server-side) |
| Cookie flags | `HttpOnly`, `SameSite=Lax`, `Secure` (produção) |
| TTL padrão | Configurável por ambiente |
| Invalidação | Logout, mudança de senha, suspeita de comprometimento |
| Rotação | ID de sessão rotacionado após login |

---

## Proteções Anti-Ataque

### Brute Force

```
Login:              10 tentativas / 15 min por IP
Forgot password:    5 tentativas / 15 min por e-mail  
Verify OTP:         3 tentativas / OTP (bloqueia e requer novo)
```

### CSRF

- Proteção via `SameSite=Lax` nos cookies (bloqueia cross-site requests)
- Tokens CSRF adicionais em operações críticas (pagamentos, mudança de senha)

### XSS

- Cookies `HttpOnly` inacessíveis ao JavaScript
- CSP headers configurados para bloquear scripts inline
- Sanitização de output em todos os campos renderizados

### Injection

- Todas as queries via Drizzle ORM (queries parametrizadas)
- Validação de input com Zod em todas as entradas de API
- Zero interpolação de string em queries SQL

---

## OTP (One-Time Password)

Usado para:
- Reset de senha
- Verificação de e-mail
- (Roadmap) Segundo fator via SMS

Características:
- Token de 6 dígitos numéricos
- Expira em 15 minutos
- Uso único (invalidado após verificação)
- Máximo 3 tentativas antes de invalidar

---

## Auditoria de Autenticação

Todos os eventos de autenticação são logados:

| Evento | Campos Logados |
|--------|---------------|
| Login bem-sucedido | userId, IP, timestamp |
| Login falho | email (hash), IP, motivo |
| Logout | userId, sessionId |
| Reset de senha | userId, IP |
| E-mail verificado | userId |
| Sessão expirada | userId, sessionId |

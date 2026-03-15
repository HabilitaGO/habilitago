# Data Protection — HabilitaGO

## Dados em Trânsito

- **HTTPS obrigatório** em produção (HSTS habilitado)
- TLS 1.2+ em todas as conexões externas
- Certificados gerenciados pela plataforma de deploy (Railway)
- Conexões com DB via SSL (Neon enforça TLS)

---

## Dados em Repouso

### Criptografia de PII Sensível

Dados pessoais sensíveis são criptografados antes de armazenar:

| Dado | Tratamento |
|------|-----------|
| CPF/CNPJ | Criptografado (AES-256-GCM) |
| Dados bancários | Criptografado + delegado ao Stripe Connect |
| Documentos de identidade | Criptografado + storage seguro |
| Dados de cartão | **Nunca armazenados** — delegado ao Stripe |

### Modos de Criptografia

**Local (desenvolvimento/staging):**
```
Chave derivada de variável de ambiente CRYPTO_LOCAL_KEY
AES-256-GCM com IV aleatório por operação
```

**KMS (produção):**
```
Envelope Encryption:
  1. AWS KMS gera/armazena CMK (Customer Master Key)
  2. DEK (Data Encryption Key) gerada por usuário
  3. DEK cifrada com CMK
  4. Dado cifrado com DEK
  Armazenado: { kid, encrypted_dek, ciphertext, iv }
```

---

## Minimização de Dados

Coletamos apenas o necessário para cada funcionalidade:

| Funcionalidade | Dados Coletados |
|---------------|----------------|
| Cadastro básico | Nome, e-mail, telefone |
| Perfil instrutor | + documentos DETRAN, foto, dados bancários |
| Agendamento | + localização de pickup |
| Pagamento | → delegado ao Stripe/MercadoPago |
| Avaliação | Texto e nota apenas |

---

## Retenção e Exclusão

| Categoria | Retenção | Motivo |
|-----------|---------|--------|
| Dados de conta ativa | Durante uso | Contrato de serviço |
| Dados financeiros | 5 anos | Obrigação fiscal (CTN) |
| Logs de segurança | 12 meses | Detecção de fraude |
| Dados após exclusão | Anonimizados em 30 dias | LGPD Art. 16 |

### Fluxo de Exclusão de Conta

```
Usuário solicita exclusão em /excluir-conta
  └── Conta desativada imediatamente (deleted_at)
      └── Worker de exclusão (30 dias):
          ├── Anonimiza nome, e-mail, telefone, documentos
          ├── Mantém registros financeiros (obrigação legal)
          └── Remove dados de sessão e preferências
```

---

## Controle de Acesso a Dados

- Banco de dados **não acessível publicamente** (IP allowlist no Neon)
- Credenciais de DB apenas em variáveis de ambiente (nunca no código)
- Acesso ao DB de produção apenas via VPN/bastion (operações)
- Admin dashboard com trilha de auditoria de cada acesso

---

## Segurança de Uploads

- Arquivos de documentos armazenados em bucket privado (S3-compatible)
- URLs de acesso com prazo de expiração (pre-signed URLs)
- Validação de tipo MIME e tamanho antes de aceitar upload
- Scan de malware planejado para Phase 2

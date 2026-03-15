# Modelo de Marketplace — HabilitaGO

## Tipo de Marketplace

HabilitaGO é um **marketplace de serviços two-sided**:

- **Lado da oferta:** Instrutores autônomos certificados pelo DETRAN
- **Lado da demanda:** Alunos que precisam de aulas práticas para habilitação

## Dinâmica de Supply & Demand

### Oferta (Instrutores)
- Cadastro com validação de credenciais (DETRAN, CNH especial)
- Aprovação manual pela equipe HabilitaGO
- Configuração de disponibilidade, preço e veículo
- Construção de reputação por avaliações dos alunos

### Demanda (Alunos)
- Descoberta por cidade/bairro, filtros de preço e disponibilidade
- Perfis completos com avaliações e histórico verificado
- Booking direto sem intermediários

## Modelo de Receita

### Take-Rate sobre Transação
A plataforma retém uma comissão sobre cada pagamento de aula. O instrutor recebe o valor líquido via Stripe Connect (transferência para conta bancária).

```
Valor pago pelo aluno
  - Comissão HabilitaGO (%)
  - Taxa do provedor de pagamento (Stripe/MercadoPago)
  = Valor líquido para o instrutor
```

> Os percentuais exatos são definidos operacionalmente e não são públicos.

### Outros Modelos Potenciais (Roadmap)
- Plano de destaque para instrutores (visibilidade premium)
- Pacotes pré-pagos de aulas com desconto
- Modelo B2B para frotas e empresas

## Fluxo Financeiro

```
Aluno → Stripe/MercadoPago → Plataforma HabilitaGO → Stripe Connect → Instrutor
```

1. Aluno paga via cartão (Stripe) ou PIX (MercadoPago)
2. Pagamento confirmado via webhook
3. Valor entra na carteira HabilitaGO do instrutor
4. Instrutor solicita saque via Stripe Connect
5. Dinheiro cai na conta bancária do instrutor

## Governança de Qualidade

### Aprovação de Instrutores
- Envio de documentação (CNH especial, certificação DETRAN)
- Análise pela equipe (fluxo: `PENDENTE_ENVIO_DADOS` → `AGUARDANDO_ANALISE` → `APROVADO`)
- Apenas instrutores aprovados aparecem na busca

### Reputação
- Avaliações de alunos após cada aula (sistema de rating)
- Histórico de aulas visível no perfil
- Instrutores com baixa avaliação podem ser desativados

## Diferencial Competitivo

| Fator | Autoescola Tradicional | HabilitaGO |
|-------|----------------------|------------|
| Preço | Opaco, balcão | Transparente, competitivo |
| Avaliações | Boca a boca | Verificadas e públicas |
| Agendamento | Telefone/WhatsApp | App, 24/7 |
| Pagamento | Dinheiro/TED | Cartão, PIX, rastreável |
| Instrutor | Funcionário de autoescola | Autônomo certificado |
| Escolha | Limitada | Múltiplos instrutores |

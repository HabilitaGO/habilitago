# Pricing & Take-Rate — HabilitaGO

## Modelo de Precificação

HabilitaGO opera no modelo **take-rate sobre transação**: a plataforma retém uma comissão percentual de cada pagamento processado, repassando o valor líquido ao instrutor.

```
Valor pago pelo aluno
  - Take-rate HabilitaGO (%)
  - Taxa do provedor de pagamento (Stripe/MercadoPago)
  = Valor líquido recebido pelo instrutor
```

> Os percentuais exatos são confidenciais e definidos operacionalmente.

## Por que Take-Rate?

- **Alinhamento de incentivos**: HabilitaGO só ganha quando os instrutores ganham.
- **Escalabilidade**: Receita cresce com volume sem custo marginal adicional.
- **Simplicidade**: Nenhuma assinatura ou taxa fixa para instrutores.
- **Formalização**: Cada transação gera registro rastreável para o instrutor.

## Benchmark de Mercado

| Plataforma | Segmento | Take-Rate Típico |
|-----------|---------|----------------|
| Airbnb | Hospedagem | 3% host + 14% guest |
| Uber | Transporte | 20–25% |
| GetNinjas | Serviços | 10–20% por lead |
| Workana | Freelance | 6–15% |

HabilitaGO opera em range competitivo para o segmento de serviços B2C no Brasil.

## Estrutura de Pagamento ao Instrutor

1. Aluno paga → capturado pela plataforma
2. Take-rate calculado automaticamente
3. Valor líquido creditado na carteira digital do instrutor
4. Instrutor solicita saque → Stripe Connect → conta bancária

## Modelos Futuros (Roadmap)

| Modelo | Descrição | Fase |
|--------|-----------|------|
| Destaque Premium | Instrutor paga por visibilidade aumentada na busca | Phase 3 |
| Pacotes Pré-pagos | Aluno compra pacote de aulas com desconto | Phase 3 |
| Plano B2B | Taxa fixa mensal para frota/empresa + take-rate reduzido | Phase 5 |
| Assinatura Pro | Instrutores com volume alto pagam fee menor | Phase 4 |

## Considerações de Pricing para Investidores

- **Unit Economics**: Ticket médio R$ 800–2.000 por pacote de aulas práticas
- **LTV estimado por instrutor**: Depende de volume mensal e recorrência de alunos
- **CAC**: Marketing de performance + SEO orgânico por cidade
- **Payback Period**: Definido com base em dados de produção (fase inicial)

# Jornada do Aluno — HabilitaGO

## Etapas

### 1. Descoberta
O aluno acessa o app ou o site e busca instrutores na sua cidade. Pode filtrar por preço, disponibilidade, avaliação e tipo de veículo.

```
App Home → "Buscar instrutores" → Seleciona cidade → Lista de instrutores
```

### 2. Avaliação
O aluno visita o perfil do instrutor: foto, descrição, avaliações de outros alunos, preços por aula e disponibilidade da agenda.

### 3. Agendamento
Fluxo multi-step:
1. Escolhe o instrutor
2. Seleciona o tipo de veículo (carro automático, manual, moto)
3. Escolhe data e horário disponível
4. Confirma os dados da aula

### 4. Pagamento
- **Cartão de crédito/débito** via Stripe
- **PIX** via MercadoPago (QR Code ou copia-e-cola)

Pagamento confirmado em tempo real via webhook.

### 5. Aula
O instrutor vai buscar o aluno no local combinado (ou o aluno vai ao ponto de encontro definido). A aula acontece.

### 6. Avaliação Pós-Aula
Após a aula, o aluno recebe uma notificação para avaliar o instrutor (1–5 estrelas + comentário). A avaliação fica pública no perfil do instrutor.

### 7. Histórico
O aluno pode consultar no app:
- Todas as aulas realizadas
- Pagamentos e notas fiscais
- Instrutores contratados anteriormente

---

## Notificações

O aluno recebe notificações em:
- Confirmação de agendamento
- Lembrete 24h antes da aula
- Confirmação de pagamento
- Solicitação de avaliação pós-aula
- Status de suporte (se abrir ticket)

---

## Suporte

O aluno pode abrir tickets de suporte diretamente pelo app, com upload de evidências (fotos, comprovantes).

# Jornada do Instrutor — HabilitaGO

## Etapas de Onboarding

### 1. Cadastro
O instrutor se cadastra no app com nome, e-mail e senha. E-mail é verificado antes de prosseguir.

### 2. Envio de Documentação
O instrutor envia via app:
- Foto do documento de identidade
- CNH especial (categoria B ou D, conforme regulamentação DETRAN)
- Certificado de instrutor de trânsito
- Foto de perfil profissional

**Status:** `PENDENTE_ENVIO_DADOS`

### 3. Análise
A equipe HabilitaGO revisa a documentação e valida as credenciais.

**Status:** `AGUARDANDO_ANALISE`

### 4. Aprovação
Após aprovação, o instrutor aparece na busca dos alunos e pode configurar seu perfil.

**Status:** `APROVADO`

> Em caso de reprovação (`REPROVADO`), o instrutor recebe feedback e pode reenviar a documentação.

---

## Operação Diária

### Gerenciamento de Agenda
- O instrutor define os horários disponíveis por dia da semana
- Pode bloquear períodos específicos
- Alunos só conseguem agendar nos horários abertos

### Recebimento de Agendamentos
- Notificação quando um aluno agenda
- Pode confirmar ou recusar (com justificativa)
- Após confirmação, o pagamento é processado

### Execução da Aula
- Buscar o aluno no local combinado
- Ministrar a aula prática
- Marcar como concluída no app

### Pagamento
- Valor da aula entra na carteira digital do instrutor
- Pode solicitar saque a qualquer momento
- Transferência via Stripe Connect para conta bancária

---

## Perfil Público

O instrutor gerencia:
- Foto, bio e descrição profissional
- Tipos de veículos disponíveis (automático, manual, moto)
- Preço por aula
- Área de atuação (cidades/bairros)
- Certificações

---

## Métricas do Instrutor (Painel)

- Total de aulas realizadas
- Avaliação média (estrelas)
- Histórico de pagamentos
- Alunos recorrentes
- Solicitações pendentes

# 🏥 Agente Clínico - DaiLaser

Agente de IA especializado em atendimento para clínica de depilação a laser.

## 📋 Visão Geral

O agente "Dai" é uma atendente virtual que realiza o primeiro contato com clientes da clínica DaiLaser, fornecendo informações sobre procedimentos, esclarecendo dúvidas e guiando até o agendamento.

## 🎯 Funcionalidades Principais

### 1. Processamento de Mensagens Multimídia
- **Texto**: Processamento direto
- **Áudio**: Transcrição automática usando Whisper
- **Imagem**: Análise e descrição usando GPT-4 Vision

### 2. Sistema de Fila de Mensagens
- Agrupa múltiplas mensagens recebidas rapidamente
- Evita respostas duplicadas
- Aguarda 8 segundos antes de processar para capturar mensagens múltiplas

### 3. Cadastro de Clientes
- Consulta se cliente já existe no sistema
- Coleta informações: nome, email, telefone, data de nascimento, endereço
- Cadastra na plataforma QuickAI Agenda

### 4. Geração de Links de Agendamento
- Gera link único para cada cliente
- Permite agendamento e reagendamento
- Link personalizado com dados do cliente

### 5. Consulta de Agendamentos
- Consulta agendamentos existentes do cliente
- Retorna data/hora e descrição dos procedimentos

## 🔄 Fluxo de Funcionamento

```
1. Recebe Mensagem (Webhook)
   ↓
2. Filtra Dados (extrai nome, número, tipo de mensagem)
   ↓
3. Verifica Cliente no Banco de Dados
   ↓
4. Tipagem de Mensagem (texto/áudio/imagem)
   ↓
5. Processamento de Mídia (se necessário)
   ↓
6. Registra na Fila Redis
   ↓
7. Verifica se há mais mensagens chegando
   ↓
8. Organiza Mensagens (inverte ordem)
   ↓
9. Processa com Agente de IA
   ↓
10. Separa Resposta em Partes
    ↓
11. Envia Mensagens Sequencialmente
```

## 🛠️ Configuração

### Credenciais Necessárias

1. **OpenAI API**
   - Modelo: GPT-4o-mini
   - Usado para: Processamento de linguagem natural, análise de imagens, transcrição de áudio

2. **PostgreSQL**
   - Tabela `messages`: Armazena dados dos clientes
   - Tabela `n8n_chat_histories`: Armazena histórico de conversas

3. **Redis**
   - Gerenciamento de filas de mensagens
   - Chave: `daiaralaser:queue:{numero_cliente}`

4. **Evolution API**
   - Instância: "DaiaraLaser"
   - Endpoints:
     - `/chat/getBase64FromMediaMessage/DaiaraLaser`
     - `/message/sendText/DaiaraLaser`

5. **QuickAI Agenda API**
   - Endpoints:
     - `POST /api/clients` - Cadastro de cliente
     - `POST /api/client/phone` - Consulta cliente
     - `POST /api/appointment-hash/generate` - Gera link de agendamento
     - `POST /api/client/appointments` - Consulta agendamentos

### Variáveis de Ambiente

Nenhuma variável de ambiente específica necessária.

## 📊 Estrutura de Dados

### Dados do Cliente (PostgreSQL)

```sql
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    phone VARCHAR(20) NOT NULL UNIQUE,
    message INTEGER DEFAULT 1,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### Fila Redis

- **Chave**: `daiaralaser:queue:{numero_cliente}`
- **Tipo**: Lista
- **Conteúdo**: Mensagens concatenadas

## 🤖 Personalidade do Agente

O agente "Dai" possui as seguintes características:

- **Tom**: Leve, claro e humano
- **Comunicação**: Amigável, empática
- **Feedback**: Usa expressões como "entendo bem", "compreendo", "sei bem como é"
- **Emojis**: Usa 1 emoji a cada 5 mensagens
- **Linguagem**: Natural, variação para parecer menos robótico

### Fluxo de Atendimento

1. **Cliente diz a área**
   - Responde com valor estimado
   - Pergunta: "Você já fez laser antes ou essa vai ser a primeira vez?"

2. **Cliente pede tabela completa**
   - Envia link do Canvas com tabela de valores
   - Pergunta sobre experiência anterior

3. **Primeira vez**
   - Mensagem: "Aaah perfeito, você vai AMAR, tá!?"

4. **Pergunta sobre dor**
   - Explica que o aparelho é ajustado conforme sensibilidade

5. **Agendamento**
   - Consulta se cliente existe
   - Se não existe: coleta dados e cadastra
   - Gera link de agendamento

## 🔧 Tools Disponíveis

### 1. `consulta_cliente`
Consulta se o cliente já está cadastrado no sistema.

**Uso**: Automático antes de qualquer cadastro

**Parâmetros**:
```json
{
  "phone": "numero_do_cliente"
}
```

### 2. `Cadastro`
Cadastra novo cliente na plataforma.

**Uso**: Quando cliente não existe e quer agendar

**Parâmetros**:
```json
{
  "establishment_id": 1,
  "name": "Nome do cliente",
  "email": "email@exemplo.com",
  "phone": "numero_do_cliente",
  "birth_date": "YYYY/MM/DD",
  "address": "Endereço completo"
}
```

### 3. `gerarLink`
Gera link de agendamento para cliente cadastrado.

**Uso**: Após cadastro ou para reagendamento

**Parâmetros**:
```json
{
  "establishment_id": 1,
  "phone": "numero_do_cliente"
}
```

**Resposta**: Retorna `login_url` para o cliente

### 4. `consultar_agendamentos`
Consulta agendamentos do cliente.

**Uso**: Quando cliente solicita seus agendamentos

**Parâmetros**:
```json
{
  "phone": "numero_do_cliente",
  "establishment_id": 1
}
```

**Resposta**: Lista com `start_datetime` e `description`

## 📝 Exemplos de Uso

### Exemplo 1: Cliente Novo Perguntando sobre Depilação

**Cliente**: "Oi, quero saber sobre depilação a laser"

**Agente**: "Oi! Tudo bem? 😊 Sou a Dai, atendente da clínica DaiLaser. Fico feliz em ajudar! Qual área você tem interesse em fazer a depilação?"

**Cliente**: "Axila"

**Agente**: "Perfeito! Para axila, o valor é R$ X. Você já fez laser antes ou essa vai ser a primeira vez?"

### Exemplo 2: Cliente Solicitando Agendamento

**Cliente**: "Quero agendar uma consulta"

**Agente**: [Consulta se cliente existe]
**Agente**: "Ótimo! Para agendar, preciso de algumas informações. Qual seu nome completo?"

**Cliente**: "Maria Silva"

**Agente**: "Perfeito, Maria! Qual seu email?"

[... coleta todos os dados ...]

**Agente**: "Pronto! Seu cadastro foi realizado. Aqui está seu link de agendamento: [link]"

## ⚠️ Observações Importantes

1. **Nunca diga que é IA**: O agente nunca deve revelar que é uma assistente virtual
2. **Não transfira sem motivo**: Só transfere atendimento quando realmente necessário
3. **Não invente valores**: Sempre consulte a tabela de preços
4. **Link do Canvas**: Sempre enviar quando solicitarem tabela de preços
5. **Cadastro único**: Não perguntar dados mais de uma vez

## 🐛 Troubleshooting

### Problema: Cliente não está sendo cadastrado

**Verificar**:
1. API Key da QuickAI Agenda está correta?
2. Dados estão sendo coletados corretamente?
3. Formato da data está correto (YYYY/MM/DD)?

### Problema: Link de agendamento não está sendo gerado

**Verificar**:
1. Cliente está cadastrado no sistema?
2. API Key está configurada?
3. `establishment_id` está correto?

### Problema: Mensagens duplicadas

**Verificar**:
1. Redis está funcionando?
2. Tempo de espera (8 segundos) está adequado?
3. Fila está sendo limpa após processamento?

## 📈 Melhorias Futuras

- [ ] Integração com sistema de pagamento
- [ ] Notificações de lembrete de consulta
- [ ] Sistema de avaliação pós-atendimento
- [ ] Relatórios de conversão


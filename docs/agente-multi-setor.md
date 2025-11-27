# 🏢 Agente Multi-Setor

Agente de IA com sistema de múltiplos setores, tickets e base de conhecimento dinâmica.

## 📋 Visão Geral

Este agente é projetado para empresas que precisam de atendimento em múltiplos setores, com capacidade de alternar entre diferentes contextos e manter base de conhecimento específica para cada setor.

## 🎯 Funcionalidades Principais

### 1. Sistema de Setores Dinâmicos
- Múltiplos prompts/configurações por setor
- Alteração automática de prompt baseado no contexto
- Consulta de prompts disponíveis

### 2. Sistema de Tickets
- Abertura automática de tickets
- Fechamento de tickets
- Rastreamento de status

### 3. Base de Conhecimento
- Base de conhecimento específica por setor
- Consulta automática quando necessário
- Respostas baseadas em conhecimento estruturado

### 4. Limpeza de Memória Inteligente
- Limpa memória ao alternar entre setores
- Mantém contexto durante conversa no mesmo setor
- Evita confusão entre diferentes contextos

### 5. Geração de Links de Cadastro
- Gera links únicos para cadastro de novos clientes
- Integração com sistema de cadastro externo

## 🔄 Fluxo de Funcionamento

```
1. Recebe Mensagem (Webhook)
   ↓
2. Filtra Dados
   ↓
3. Verifica Cliente no Banco
   ↓
4. Consulta Cliente (API Externa)
   ↓
5. Se cliente não existe: Gera Link de Cadastro
   ↓
6. Tipagem de Mensagem
   ↓
7. Processamento de Mídia
   ↓
8. Registra na Fila Redis
   ↓
9. Verifica Fila (aguarda 8 segundos)
   ↓
10. Define Prompt Baseado no Cliente
    ↓
11. Carrega Prompt do Setor
    ↓
12. Consulta Base de Conhecimento (se necessário)
    ↓
13. Processa com Agente de IA
    ↓
14. Separa e Envia Respostas
```

## 🛠️ Configuração

### Credenciais Necessárias

1. **OpenAI API**
   - Modelo: GPT-4o-mini
   - Usado para: Processamento de linguagem natural

2. **PostgreSQL**
   - Tabela `messages`: Dados dos clientes e prompt_id
   - Tabela `prompts`: Prompts de cada setor
   - Tabela `n8n_chat_histories`: Histórico de conversas

3. **Redis**
   - Gerenciamento de filas
   - Chave: `newMusic:queue:{numero_cliente}`

4. **Evolution API**
   - Instância: "newmusic"
   - Variável: `URL_EVOLUTION`

5. **API de Tickets**
   - Endpoint para abrir/fechar tickets
   - Autenticação via Header Auth

6. **Base de Conhecimento API**
   - API Key específica
   - Consulta por setor

### Estrutura do Banco de Dados

```sql
-- Tabela de mensagens com prompt_id
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    phone VARCHAR(20) NOT NULL,
    message INTEGER DEFAULT 1,
    prompt_id INTEGER NOT NULL,
    created_at TIMESTAMP DEFAULT NOW(),
    FOREIGN KEY (prompt_id) REFERENCES prompts(id)
);

-- Tabela de prompts por setor
CREATE TABLE prompts (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    prompt TEXT NOT NULL,
    setor VARCHAR(100),
    created_at TIMESTAMP DEFAULT NOW()
);
```

## 🤖 Sistema de Prompts Dinâmicos

### Como Funciona

1. **Consulta Prompt Atual**: O sistema consulta qual `prompt_id` está associado ao cliente
2. **Carrega Prompt**: Busca o prompt correspondente na tabela `prompts`
3. **Aplica ao Agente**: O prompt é usado como system message do agente
4. **Alteração**: Quando necessário, o agente pode alterar o `prompt_id` usando a tool `alterar_prompts`

### Tool: `consultar_prompts`

Consulta todos os prompts disponíveis para identificar IDs.

**Uso**: Sempre antes de usar `alterar_prompts`

**Retorno**: Lista de prompts com IDs

### Tool: `alterar_prompts`

Altera o prompt_id do cliente para mudar de setor.

**Parâmetros**:
```json
{
  "phone": "numero_cliente",
  "prompt_id": 2
}
```

**Efeito**: 
- Atualiza `prompt_id` na tabela `messages`
- Próxima mensagem usará o novo prompt

## 🎫 Sistema de Tickets

### Abertura Automática

Tickets são abertos automaticamente quando:
- Cliente envia primeira mensagem
- Mensagem é processada e unificada

### Tool: `fechar_ticket`

Fecha um ticket de atendimento.

**Uso**: Quando atendimento é concluído

**Parâmetros**: Definidos pela API de tickets

## 📚 Base de Conhecimento

### Tool: `baseDeConhecimento`

Consulta a base de conhecimento do setor atual.

**Uso**: 
- Quando cliente faz pergunta específica
- Quando agente precisa de informações técnicas
- Automaticamente pelo agente quando necessário

**Comportamento**:
- Não informa ao cliente que está consultando
- Usa informações para responder
- Se não encontrar, informa que não pode ajudar

## 🧹 Limpeza de Memória

### Tool: `limpa_memoria`

Limpa o histórico de conversa do cliente.

**Uso**: 
- Quando cliente alterna de setor
- Quando necessário resetar contexto
- NUNCA durante conversa normal

**Efeito**:
- Remove registros da tabela `n8n_chat_histories`
- Cliente começa conversa "do zero"
- Mantém dados do cliente em `messages`

## 🔗 Sistema de Cadastro

### Geração de Link

Quando cliente novo é detectado:
1. Sistema consulta se cliente existe
2. Se não existe, gera link de cadastro
3. Link é enviado ao cliente
4. Após cadastro, cliente pode continuar conversa

### Link de Cadastro

Gerado via API externa, contém:
- Hash único
- Dados pré-preenchidos (quando possível)
- Redirecionamento após cadastro

## 📝 Exemplos de Uso

### Exemplo 1: Cliente Alternando de Setor

**Cliente**: "Quero falar sobre vendas"
**Agente**: [Consulta prompts, identifica ID do setor de vendas]
**Agente**: [Altera prompt_id usando `alterar_prompts`]
**Agente**: [Limpa memória usando `limpa_memoria`]
**Agente**: "Olá! Como posso ajudar com vendas?"

### Exemplo 2: Consulta à Base de Conhecimento

**Cliente**: "Qual o prazo de entrega?"
**Agente**: [Usa `baseDeConhecimento` automaticamente]
**Agente**: "O prazo de entrega é de 5 a 7 dias úteis para a sua região."

### Exemplo 3: Fechamento de Ticket

**Cliente**: "Obrigado, problema resolvido!"
**Agente**: [Usa `fechar_ticket`]
**Agente**: "Fico feliz em ter ajudado! Se precisar de mais alguma coisa, estou à disposição."

## ⚠️ Observações Importantes

1. **Sempre consultar prompts**: Antes de alterar, sempre usar `consultar_prompts`
2. **Limpar memória ao alternar**: Sempre limpar quando mudar de setor
3. **Não limpar durante conversa**: Não limpar memória em conversa normal
4. **Base de conhecimento silenciosa**: Não informar ao cliente sobre consulta
5. **Tickets automáticos**: Tickets são abertos automaticamente

## 🐛 Troubleshooting

### Problema: Prompt não está sendo alterado

**Verificar**:
1. Tool `consultar_prompts` foi executada?
2. `prompt_id` existe na tabela `prompts`?
3. Cliente existe na tabela `messages`?

### Problema: Memória não está sendo limpa

**Verificar**:
1. Tool `limpa_memoria` está sendo chamada?
2. `session_id` está correto (número do cliente)?
3. Tabela `n8n_chat_histories` existe?

### Problema: Base de conhecimento não retorna dados

**Verificar**:
1. API Key está configurada?
2. Setor está correto?
3. Base de conhecimento tem dados para o setor?

## 📈 Melhorias Futuras

- [ ] Dashboard de métricas por setor
- [ ] Análise de satisfação por setor
- [ ] Integração com CRM
- [ ] Sistema de escalação por setor
- [ ] Relatórios de performance


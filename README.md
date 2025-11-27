# 🤖 Agentes de IA para n8n

Coleção de agentes de IA desenvolvidos para automação de atendimento via WhatsApp usando n8n. Cada agente é especializado em diferentes setores e possui funcionalidades específicas para atender clientes de forma automatizada e inteligente.

## 📋 Índice

- [Visão Geral](#visão-geral)
- [Agentes Disponíveis](#agentes-disponíveis)
- [Pré-requisitos](#pré-requisitos)
- [Instalação](#instalação)
- [Configuração](#configuração)
- [Como Usar](#como-usar)
- [Documentação Detalhada](#documentação-detalhada)
- [Estrutura de Dados](#estrutura-de-dados)
- [Troubleshooting](#troubleshooting)

## 🎯 Visão Geral

Estes agentes utilizam tecnologias de IA (GPT-4o-mini) integradas com n8n para criar sistemas de atendimento automatizado via WhatsApp. Eles são capazes de:

- ✅ Receber e processar mensagens de texto, áudio e imagem
- ✅ Manter contexto de conversas usando memória persistente
- ✅ Integrar com APIs externas para consultas e cadastros
- ✅ Gerenciar filas de mensagens para evitar respostas duplicadas
- ✅ Escalar conversas para atendimento humano quando necessário
- ✅ Realizar follow-ups automáticos

## 🤖 Agentes Disponíveis

### 1. **Agente Clínico - DaiLaser** (`AI-agent_for_clinical_service.json`)
Agente especializado em atendimento para clínica de depilação a laser.

**Principais Funcionalidades:**
- Atendimento personalizado com a personalidade "Dai"
- Cadastro de clientes na plataforma de agendamento
- Geração de links de agendamento
- Consulta de agendamentos existentes
- Envio de tabela de preços via Canvas

**Documentação:** [docs/agente-clinico-dailaser.md](docs/agente-clinico-dailaser.md)

### 2. **Agente Multi-Setor** (`multi-agent.automation.json`)
Agente com sistema de múltiplos setores, tickets e base de conhecimento.

**Principais Funcionalidades:**
- Sistema de tickets de atendimento
- Base de conhecimento por setor
- Alteração dinâmica de prompts por setor
- Limpeza de memória ao alternar setores
- Geração de links de cadastro

**Documentação:** [docs/agente-multi-setor.md](docs/agente-multi-setor.md)

### 3. **Agente Concessionária** (`multi-worflow_garage.automation.json`)
Agente especializado em atendimento para concessionárias de veículos.

**Principais Funcionalidades:**
- Consulta de catálogo de veículos com filtros
- Cadastro de veículos (apenas para admins)
- Sistema de avaliação de intenção de compra
- Encaminhamento automático para vendedores
- Sistema de follow-up automático (2h, 6h, 24h)
- Bloqueio de automação quando há interferência humana

**Documentação:** [docs/agente-concessionaria.md](docs/agente-concessionaria.md)

### 4. **Agente Clínico - Stella Amorim** (`simple_agent-service.json`)
Agente especializado em atendimento para clínica de cirurgia plástica.

**Principais Funcionalidades:**
- Atendimento personalizado com a personalidade "Amora"
- Informações sobre procedimentos cirúrgicos
- Envio de links de vídeos explicativos
- Envio de e-book com resultados
- Agendamento de consultas
- Sistema de escalação após 15 mensagens
- Bloqueio de automação quando há interferência humana

**Documentação:** [docs/agente-clinico-stella.md](docs/agente-clinico-stella.md)

## 📦 Pré-requisitos

Antes de começar, você precisará de:

1. **n8n** instalado e configurado
   - Versão recomendada: 1.0+
   - Acesso via webhook

2. **Credenciais Necessárias:**
   - OpenAI API Key (para GPT-4o-mini)
   - Evolution API (para integração WhatsApp)
   - PostgreSQL (para memória persistente)
   - Redis (para gerenciamento de filas)

3. **Serviços Externos:**
   - Evolution API configurada e rodando
   - Banco de dados PostgreSQL
   - Instância Redis

## 🚀 Instalação

### Passo 1: Importar Workflows

1. Acesse seu n8n
2. Vá em **Workflows** → **Import from File**
3. Selecione o arquivo JSON do agente desejado
4. O workflow será importado automaticamente

### Passo 2: Configurar Credenciais

Cada agente requer credenciais específicas. Configure-as no n8n:

#### Credenciais Comuns:
- **OpenAI API**: Para processamento de linguagem natural
- **PostgreSQL**: Para armazenamento de memória e dados
- **Redis**: Para gerenciamento de filas
- **Evolution API**: Para integração WhatsApp

#### Credenciais Específicas por Agente:

**DaiLaser:**
- API Key da plataforma de agendamento (QuickAI Agenda)
- Evolution API para instância "DaiaraLaser"

**Multi-Setor:**
- API Key do sistema de tickets
- Base de conhecimento API

**Concessionária:**
- API Key do catálogo de veículos (BancoAI)
- Evolution API para instância "garage_automation"

**Stella Amorim:**
- Evolution API para instância "stellaamorim"
- Gmail OAuth2 (para notificações de erro)

### Passo 3: Configurar Variáveis de Ambiente

Alguns workflows utilizam variáveis de ambiente. Configure no n8n:

```bash
URL_EVOLUTION=https://sua-evolution-api.com
```

## ⚙️ Configuração

### 1. Configurar Webhook

Cada workflow possui um webhook único. Configure no Evolution API para enviar mensagens recebidas:

1. Copie a URL do webhook do n8n
2. Configure no Evolution API como destino de webhooks
3. Teste enviando uma mensagem

### 2. Configurar Banco de Dados

Crie as tabelas necessárias no PostgreSQL:

#### Tabela de Mensagens (comum a todos):
```sql
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    phone VARCHAR(20) NOT NULL,
    message INTEGER DEFAULT 0,
    contexto TEXT,
    ultimo_envio TIMESTAMP,
    status_followup INTEGER DEFAULT 0,
    encaminha_vendedor BOOLEAN DEFAULT false,
    name VARCHAR(255),
    created_at TIMESTAMP DEFAULT NOW()
);
```

#### Tabela de Histórico de Chat (para memória):
```sql
CREATE TABLE n8n_chat_histories (
    session_id VARCHAR(255) PRIMARY KEY,
    messages JSONB,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);
```

### 3. Configurar Redis

O Redis é usado para gerenciar filas de mensagens. Certifique-se de que está acessível e configurado.

## 📖 Como Usar

### Fluxo Básico de Funcionamento

1. **Recebimento de Mensagem**: O webhook recebe a mensagem do WhatsApp via Evolution API
2. **Processamento**: A mensagem é filtrada e tipada (texto, áudio, imagem)
3. **Processamento de Mídia**: 
   - Áudios são transcritos usando Whisper
   - Imagens são analisadas usando GPT-4 Vision
4. **Fila de Mensagens**: Mensagens múltiplas são agrupadas para evitar respostas duplicadas
5. **Processamento pela IA**: O agente de IA processa a mensagem usando contexto e memória
6. **Resposta**: A resposta é dividida em partes e enviada sequencialmente

### Exemplo de Uso - Agente Clínico

1. Cliente envia: "Olá, gostaria de saber sobre depilação a laser"
2. Agente responde: "Oi! Tudo bem? 😊 Sou a Dai, atendente da clínica..."
3. Cliente pergunta sobre valores
4. Agente envia link da tabela de preços
5. Cliente solicita agendamento
6. Agente coleta dados e gera link de agendamento




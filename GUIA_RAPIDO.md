# 🚀 Guia Rápido de Início

Este guia vai te ajudar a começar rapidamente com os agentes de IA.

## ⚡ Início Rápido (5 minutos)

### 1. Importar Workflow no n8n

1. Acesse seu n8n
2. Clique em **Workflows** → **Import from File**
3. Selecione um dos arquivos JSON:
   - `AI-agent_for_clinical_service.json` (DaiLaser)
   - `multi-agent.automation.json` (Multi-Setor)
   - `multi-worflow_garage.automation.json` (Concessionária)
   - `simple_agent-service.json` (Stella Amorim)

### 2. Configurar Credenciais Básicas

No n8n, configure estas credenciais:

#### OpenAI API
- Tipo: OpenAI
- API Key: Sua chave da OpenAI
- Modelo: gpt-4o-mini

#### PostgreSQL
- Host: Seu servidor PostgreSQL
- Database: Nome do banco
- User: Usuário
- Password: Senha

#### Redis
- Host: Seu servidor Redis
- Port: 6379 (padrão)
- Password: (se necessário)

#### Evolution API
- URL: URL da sua Evolution API
- API Key: Sua chave da Evolution API

### 3. Ativar Webhook

1. No workflow importado, encontre o node "Recebe Mensagem" (Webhook)
2. Clique nele e copie a URL do webhook
3. Configure no Evolution API para enviar mensagens para essa URL

### 4. Criar Tabelas no PostgreSQL

Execute os scripts SQL conforme o agente escolhido (veja documentação específica).

### 5. Testar

1. Ative o workflow no n8n
2. Envie uma mensagem de teste via WhatsApp
3. Verifique se a resposta foi gerada

## 📋 Checklist de Configuração

- [ ] Workflow importado no n8n
- [ ] Credenciais OpenAI configuradas
- [ ] Credenciais PostgreSQL configuradas
- [ ] Credenciais Redis configuradas
- [ ] Credenciais Evolution API configuradas
- [ ] Tabelas criadas no PostgreSQL
- [ ] Webhook configurado no Evolution API
- [ ] Workflow ativado
- [ ] Teste realizado com sucesso

## 🔧 Configurações Específicas por Agente

### DaiLaser
- [ ] API Key QuickAI Agenda configurada
- [ ] Link do Canvas da tabela de preços atualizado

### Multi-Setor
- [ ] Tabela `prompts` criada e populada
- [ ] API de tickets configurada
- [ ] Base de conhecimento configurada

### Concessionária
- [ ] API Key BancoAI configurada
- [ ] Cron job de follow-up configurado
- [ ] Número do vendedor configurado

### Stella Amorim
- [ ] Gmail OAuth2 configurado (para notificações)
- [ ] Links de vídeos atualizados
- [ ] Link de agendamento atualizado

## 🆘 Problemas Comuns

### "Mensagem não está sendo recebida"
- Verifique se o webhook está ativo
- Confirme a URL no Evolution API
- Veja os logs do n8n

### "IA não está respondendo"
- Verifique créditos da OpenAI
- Confirme API Key está correta
- Veja logs de erro no n8n

### "Erro de conexão com banco"
- Verifique credenciais do PostgreSQL
- Confirme que as tabelas existem
- Teste conexão manualmente

## 📚 Próximos Passos

1. Leia a documentação completa do agente escolhido em `docs/`
2. Personalize os prompts conforme necessário
3. Configure integrações adicionais
4. Monitore o desempenho e ajuste

## 💡 Dicas

- **Teste sempre em desenvolvimento primeiro**
- **Monitore o uso da API OpenAI** para controlar custos
- **Ajuste os prompts** conforme sua necessidade
- **Mantenha backups** dos workflows

---

Precisa de ajuda? Consulte a documentação completa em `README.md` ou nos arquivos em `docs/`.


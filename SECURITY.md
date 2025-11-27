# 🔒 Segurança - Informações Sensíveis

Este documento lista todas as informações sensíveis que foram removidas dos arquivos JSON antes da publicação.

## ⚠️ Informações Removidas

### API Keys Substituídas

Todos os arquivos JSON tiveram suas chaves de API substituídas por placeholders:

#### Evolution API Keys
- **Placeholder**: `SUA_EVOLUTION_API_KEY_AQUI`
- **Arquivos afetados**: Todos os workflows
- **Onde configurar**: No n8n, nas credenciais do Evolution API

#### QuickAI Agenda API Key
- **Placeholder**: `SUA_QUICKAI_AGENDA_API_KEY_AQUI`
- **Arquivo**: `AI-agent_for_clinical_service.json`
- **Onde configurar**: No node que faz requisições para QuickAI Agenda

#### BancoAI API Key
- **Placeholder**: `SUA_BANCOAI_API_KEY_AQUI`
- **Arquivo**: `multi-worflow_garage.automation.json`
- **Onde configurar**: Nos nodes de consulta de catálogo e cadastro de veículos

#### Base de Conhecimento API Key
- **Placeholder**: `SUA_BASE_CONHECIMENTO_API_KEY_AQUI`
- **Arquivo**: `multi-agent.automation.json`
- **Onde configurar**: No node de consulta à base de conhecimento

### Números de Telefone Substituídos

- **Placeholder**: `SEU_NUMERO_AQUI` ou `SEU_NUMERO_VENDEDOR_AQUI`
- **Onde configurar**: Nos nodes que enviam mensagens ou fazem referência a números

### URLs Mantidas

Algumas URLs foram mantidas porque são endpoints públicos ou exemplos:
- URLs da Evolution API (substitua pela sua instância)
- URLs de APIs públicas (ajuste conforme necessário)

## 🔧 Como Configurar Após Importar

### 1. Evolution API

Em cada workflow, procure por nodes HTTP Request que fazem chamadas à Evolution API e configure:

```json
{
  "name": "apikey",
  "value": "SUA_CHAVE_AQUI"
}
```

### 2. APIs Externas

Para cada API externa usada:

1. Identifique o node HTTP Request correspondente
2. Localize o header `X-API-Key` ou similar
3. Substitua o placeholder pela sua chave real

### 3. Números de Telefone

Procure por:
- `SEU_NUMERO_AQUI` → Substitua pelo número do cliente/teste
- `SEU_NUMERO_VENDEDOR_AQUI` → Substitua pelo número do vendedor

## 📝 Checklist de Segurança

Antes de usar os workflows em produção:

- [ ] Todas as API Keys foram substituídas
- [ ] Números de telefone foram atualizados
- [ ] URLs foram ajustadas para seus endpoints
- [ ] Credenciais estão configuradas no n8n (não hardcoded)
- [ ] Variáveis de ambiente estão configuradas (quando aplicável)
- [ ] Testes foram realizados com credenciais de teste

## 🚨 Boas Práticas

1. **Nunca commite chaves reais**: Sempre use placeholders ou variáveis de ambiente
2. **Use credenciais do n8n**: Configure as credenciais no n8n, não nos workflows
3. **Rotacione chaves regularmente**: Mude suas API keys periodicamente
4. **Monitore uso**: Acompanhe o uso das APIs para detectar vazamentos
5. **Use variáveis de ambiente**: Para valores que mudam entre ambientes

## 🔄 Se Você Expôs uma Chave

Se acidentalmente você commitou uma chave real:

1. **Revogue a chave imediatamente**: Gere uma nova na plataforma
2. **Remova do histórico Git**: Use `git filter-branch` ou ferramentas similares
3. **Force push**: Atualize o repositório remoto
4. **Monitore uso**: Verifique se houve uso não autorizado

## 📞 Suporte

Se encontrar informações sensíveis que não foram removidas, abra uma issue no repositório.

---

**Última atualização**: Novembro 2025


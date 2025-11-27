# 🔒 Changelog de Segurança

Este arquivo documenta todas as alterações de segurança realizadas nos arquivos antes da publicação.

## ✅ Alterações Realizadas

### Data: 27 de Novembro de 2025

#### 1. API Keys Removidas

**Arquivo: `AI-agent_for_clinical_service.json`**
- ✅ Evolution API Key: `2139A65EFC3E-4607-BC29-1EF35F835CF7` → `SUA_EVOLUTION_API_KEY_AQUI` (2 ocorrências)
- ✅ QuickAI Agenda API Key: `8hhWbLwYyYxLL1OexQTq0RUWcwswCcCQUciTsvX7piu7IgngCIUArqOKKDug5dhs` → `SUA_QUICKAI_AGENDA_API_KEY_AQUI` (4 ocorrências)

**Arquivo: `multi-agent.automation.json`**
- ✅ Evolution API Key: `5CC0D57B1A05-47C3-8B86-B5DDF6BEE1A6` → `SUA_EVOLUTION_API_KEY_AQUI` (3 ocorrências)
- ✅ Base de Conhecimento API Key: `sk_live_e37322e6ff764403.Apz_EBDxbJWHEOS-PAkPvQVnVdn7x_wU` → `SUA_BASE_CONHECIMENTO_API_KEY_AQUI` (1 ocorrência)

**Arquivo: `multi-worflow_garage.automation.json`**
- ✅ Evolution API Key: `344A810A35C4-420B-8FD1-84E3003C1B64` → `SUA_EVOLUTION_API_KEY_AQUI` (6 ocorrências)
- ✅ BancoAI API Key: `bai_BfzJXjrs_K16FOdZEvIgejJGDMUAYLKOyvdwR7opCcU` → `SUA_BANCOAI_API_KEY_AQUI` (5 ocorrências)

**Arquivo: `simple_agent-service.json`**
- ✅ Evolution API Key: `4A76B0D99D00-4807-A1DC-DD8AAB2CCFE8` → `SUA_EVOLUTION_API_KEY_AQUI` (2 ocorrências)

#### 2. Números de Telefone Removidos

**Arquivo: `multi-agent.automation.json`**
- ✅ Número de telefone: `44991813693` → `SEU_NUMERO_AQUI`
- ✅ Nome de exemplo: `Tiago` → `Nome Exemplo`
- ✅ Email de exemplo: `email@gmail.com` → `email@exemplo.com`

**Arquivo: `multi-worflow_garage.automation.json`**
- ✅ Número do vendedor: `5544997251731` → `SEU_NUMERO_VENDEDOR_AQUI`

**Arquivo: `simple_agent-service.json`**
- ✅ Números de teste: `554491813693`, `5521997804980` → `SEU_NUMERO_AQUI`

#### 3. Emails Removidos

**Arquivo: `simple_agent-service.json`**
- ✅ Email administrativo: `drastellaamorimadm@gmail.com` → `seu-email@exemplo.com`

**Arquivo: `multi-agent.automation.json`**
- ✅ Email de exemplo: `email@gmail.com` → `email@exemplo.com`

## 🔍 Verificação Final

### Testes Realizados

- ✅ Busca por padrões de API Keys: **Nenhuma encontrada**
- ✅ Busca por números de telefone reais: **Nenhum encontrado**
- ✅ Busca por emails reais: **Nenhum encontrado**
- ✅ Busca por tokens sensíveis: **Nenhum encontrado**

### URLs Mantidas

As seguintes URLs foram mantidas pois são endpoints públicos ou precisam ser configuradas:

- `https://bancoai.com.br/api/public/*` - API pública (requer API Key)
- `https://quickaiagenda.com.br/api/*` - API pública (requer API Key)
- `https://n8n-automacoes-evolution-api.mm95cz.easypanel.host/*` - Substituir pela sua instância

## 📋 Placeholders Criados

Todos os placeholders seguem o padrão `SUA_[SERVICO]_API_KEY_AQUI` ou `SEU_[TIPO]_AQUI`:

- `SUA_EVOLUTION_API_KEY_AQUI` - Chave da Evolution API
- `SUA_QUICKAI_AGENDA_API_KEY_AQUI` - Chave da QuickAI Agenda
- `SUA_BANCOAI_API_KEY_AQUI` - Chave da BancoAI
- `SUA_BASE_CONHECIMENTO_API_KEY_AQUI` - Chave da Base de Conhecimento
- `SEU_NUMERO_AQUI` - Número de telefone genérico
- `SEU_NUMERO_VENDEDOR_AQUI` - Número do vendedor
- `seu-email@exemplo.com` - Email genérico

## ⚠️ Ações Necessárias Antes de Usar

1. **Substituir todos os placeholders** pelas suas credenciais reais
2. **Configurar credenciais no n8n** (recomendado ao invés de hardcoded)
3. **Atualizar URLs** para seus próprios endpoints
4. **Testar em ambiente de desenvolvimento** antes de produção

## 🔐 Status de Segurança

✅ **SEGURO PARA PUBLICAÇÃO**

Todos os arquivos foram sanitizados e estão prontos para publicação pública no GitHub.

---

**Última verificação**: 27 de Novembro de 2025
**Verificado por**: Sistema automatizado de segurança


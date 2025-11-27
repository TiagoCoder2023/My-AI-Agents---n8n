# 📤 Como Publicar no GitHub

Guia passo a passo para publicar seus agentes de IA no GitHub.

## 📋 Pré-requisitos

- Conta no GitHub
- Git instalado no seu computador
- Terminal/Command Prompt aberto

## 🚀 Passo a Passo

### 1. Criar Repositório no GitHub

1. Acesse [github.com](https://github.com)
2. Clique no botão **+** (canto superior direito) → **New repository**
3. Preencha:
   - **Repository name**: `meus-agentes-ia-n8n` (ou outro nome)
   - **Description**: "Agentes de IA para automação de atendimento via WhatsApp usando n8n"
   - **Visibility**: Escolha Público ou Privado
   - **NÃO marque** "Initialize with README" (já temos arquivos)
4. Clique em **Create repository**

### 2. Inicializar Git no Projeto Local

Abra o terminal na pasta do projeto e execute:

```bash
# Inicializar repositório Git
git init

# Adicionar todos os arquivos
git add .

# Fazer primeiro commit
git commit -m "Initial commit: Agentes de IA para n8n"
```

### 3. Conectar com GitHub

```bash
# Adicionar remote (substitua SEU_USUARIO pelo seu usuário do GitHub)
git remote add origin https://github.com/SEU_USUARIO/meus-agentes-ia-n8n.git

# Verificar se foi adicionado corretamente
git remote -v
```

### 4. Enviar para GitHub

```bash
# Enviar arquivos para o GitHub
git branch -M main
git push -u origin main
```

### 5. Verificar no GitHub

1. Acesse seu repositório no GitHub
2. Você deve ver todos os arquivos:
   - README.md
   - GUIA_RAPIDO.md
   - Arquivos JSON dos workflows
   - Pasta `docs/` com documentação

## 🔒 Segurança - Remover Informações Sensíveis

⚠️ **IMPORTANTE**: Antes de publicar, certifique-se de remover:

### 1. API Keys e Credenciais

Verifique os arquivos JSON e remova ou substitua:
- API Keys da OpenAI
- API Keys da Evolution API
- Senhas de banco de dados
- Tokens de acesso

**Exemplo de substituição**:
```json
// ANTES (NÃO PUBLICAR)
"value": "sk-abc123def456..."

// DEPOIS (PUBLICAR)
"value": "SUA_API_KEY_AQUI"
```

### 2. URLs e Endpoints Sensíveis

Substitua URLs específicas por placeholders:
```json
// ANTES
"url": "https://minha-api-secreta.com/endpoint"

// DEPOIS
"url": "https://sua-api.com/endpoint"
```

### 3. Números de Telefone

Remova ou substitua números reais:
```json
// ANTES
"number": "5511999999999"

// DEPOIS
"number": "SEU_NUMERO_AQUI"
```

## 📝 Criar Arquivo de Exemplo

Crie um arquivo `.env.example` para mostrar quais variáveis são necessárias:

```bash
# .env.example
OPENAI_API_KEY=sua_chave_aqui
POSTGRES_HOST=localhost
POSTGRES_DB=n8n_db
POSTGRES_USER=usuario
POSTGRES_PASSWORD=senha
REDIS_HOST=localhost
REDIS_PORT=6379
EVOLUTION_API_URL=https://sua-evolution-api.com
EVOLUTION_API_KEY=sua_chave_aqui
```

## 🏷️ Adicionar Tags/Tópicos no GitHub

No repositório do GitHub, adicione tópicos relevantes:
- `n8n`
- `ai-agents`
- `whatsapp-automation`
- `openai`
- `chatbot`
- `automation`

## 📄 Adicionar Licença (Opcional)

Se quiser adicionar uma licença:

1. No GitHub, clique em **Add file** → **Create new file**
2. Nome do arquivo: `LICENSE`
3. Escolha uma licença (ex: MIT, Apache 2.0)
4. GitHub pode gerar automaticamente

## 🔄 Atualizar Repositório no Futuro

Quando fizer alterações:

```bash
# Ver o que mudou
git status

# Adicionar arquivos alterados
git add .

# Fazer commit
git commit -m "Descrição das mudanças"

# Enviar para GitHub
git push
```

## 📸 Adicionar Screenshots (Opcional)

Para deixar o README mais visual:

1. Tire screenshots dos workflows no n8n
2. Crie uma pasta `screenshots/`
3. Adicione as imagens
4. Referencie no README.md

## 🎯 Boas Práticas

1. **Commits Descritivos**: Use mensagens claras sobre o que mudou
2. **Branching**: Use branches para features novas
3. **Issues**: Use Issues para bugs e sugestões
4. **Releases**: Crie releases para versões importantes

## 🆘 Problemas Comuns

### "Permission denied"
- Verifique suas credenciais do GitHub
- Use SSH keys ou Personal Access Token

### "Repository not found"
- Verifique o nome do repositório
- Confirme que você tem permissão

### "Large files"
- GitHub tem limite de 100MB por arquivo
- Use Git LFS para arquivos grandes

## 📚 Recursos Úteis

- [Documentação do Git](https://git-scm.com/doc)
- [GitHub Guides](https://guides.github.com/)
- [Markdown Guide](https://www.markdownguide.org/)

---

Pronto! Seu repositório está no GitHub e outras pessoas podem usar seus agentes! 🎉


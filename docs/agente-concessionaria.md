# 🚗 Agente Concessionária

Agente de IA especializado em atendimento para concessionárias de veículos, com sistema avançado de avaliação de intenção de compra e follow-up automático.

## 📋 Visão Geral

Este agente foi desenvolvido para concessionárias que precisam de um atendimento automatizado capaz de consultar catálogo de veículos, avaliar intenção de compra dos clientes e realizar follow-ups automáticos.

## 🎯 Funcionalidades Principais

### 1. Consulta de Catálogo de Veículos
- Busca completa do catálogo
- Filtros avançados (marca, modelo, ano, valor, quilometragem)
- Envio de imagens e descrições detalhadas
- Comentários personalizados sobre cada veículo

### 2. Sistema de Avaliação de Intenção de Compra
- Análise automática da conversa
- Pontuação de 0 a 10 (probabilidade de compra)
- Encaminhamento automático para vendedor quando necessário
- Diferenciação entre admin e cliente

### 3. Sistema de Follow-up Automático
- Follow-up após 2 horas sem resposta
- Follow-up após 6 horas sem resposta
- Follow-up após 24 horas sem resposta
- Mensagens personalizadas geradas por IA

### 4. Bloqueio de Automação
- Detecta quando humano está atendendo
- Bloqueia automação por 5 minutos
- Comando para desbloquear manualmente

### 5. Gestão de Veículos (Admin)
- Cadastro de novos veículos (apenas admins)
- Consulta de permissões de usuário
- Validação de admin antes de cadastrar

## 🔄 Fluxo de Funcionamento

```
1. Recebe Mensagem (Webhook)
   ↓
2. Verifica se é mensagem própria (fromMe)
   ↓
3. Se fromMe = true: Bloqueia automação
   ↓
4. Verifica se está bloqueado
   ↓
5. Se bloqueado: Ignora mensagem
   ↓
6. Filtra Dados
   ↓
7. Verifica Cliente no Banco
   ↓
8. Cadastra se não existe
   ↓
9. Tipagem de Mensagem
   ↓
10. Processamento de Mídia
    ↓
11. Registra na Fila Redis
    ↓
12. Verifica Fila (aguarda 2 segundos)
    ↓
13. Processa com Agente de IA
    ↓
14. Avalia Intenção de Compra
    ↓
15. Se intenção alta: Encaminha para Vendedor
    ↓
16. Separa e Envia Respostas
    ↓
17. Atualiza Contexto no Banco
    ↓
18. Reseta Follow-up
```

## 🛠️ Configuração

### Credenciais Necessárias

1. **OpenAI API**
   - Modelo: GPT-4o-mini
   - Usado para: Processamento de linguagem, análise de imagens, transcrição, avaliação

2. **PostgreSQL**
   - Tabela `messages`: Dados dos clientes e contexto
   - Campos importantes:
     - `status_followup`: Status do follow-up (0, 1, 2, 3)
     - `encaminha_vendedor`: Se foi encaminhado
     - `contexto`: Última mensagem do cliente
     - `ultimo_envio`: Data da última mensagem

3. **Redis**
   - Gerenciamento de filas
   - Sistema de bloqueio
   - Chave fila: `garage:queue:{numero_cliente}`
   - Chave bloqueio: `garage:queue:{bot_name}_{numero}_block`

4. **Evolution API**
   - Instância: "garage_automation"
   - Endpoints para envio de texto e mídia

5. **BancoAI API**
   - Catálogo de veículos
   - Cadastro de veículos
   - Consulta de usuários
   - API Key específica

### Estrutura do Banco de Dados

```sql
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    phone VARCHAR(20) NOT NULL,
    message INTEGER DEFAULT 1,
    contexto TEXT,
    ultimo_envio TIMESTAMP,
    status_followup INTEGER DEFAULT 0,
    encaminha_vendedor BOOLEAN DEFAULT false,
    data_vendedor TIMESTAMP,
    name VARCHAR(255),
    created_at TIMESTAMP DEFAULT NOW()
);
```

## 🚗 Sistema de Veículos

### Tool: `consulta_catalogo`

Consulta o catálogo completo de veículos.

**Uso**: Quando cliente pede para ver carros ou catálogo

**Comportamento**:
- SEMPRE vem antes dos filtros
- Envia todas as fotos do carro
- Inclui descrição com: modelo, marca, valor, quilometragem, observações
- Adiciona comentário elogiando o carro

**Formato de Resposta**:
```
[Comentário sobre o carro] 🚗

Modelo: Hilux
Marca: Toyota
Quilometragem: 0 km
Valor: R$ 250.000
Descrição: Ano do modelo, excelente carro para qualquer uso!

[Imagem do carro]
```

### Tool: `Consulta_carros`

Consulta veículos com filtros específicos.

**Uso**: Após mostrar catálogo completo, quando cliente quer filtrar

**Parâmetros**:
```json
{
  "marca": "Toyota",
  "modelo": "Hilux",
  "anoMin": 2020,
  "anoMax": 2024,
  "valorMin": 0,
  "valorMax": 300000,
  "quilometragemMax": "50000"
}
```

**Comportamento**:
- SEMPRE enviar todas as fotos dos carros encontrados
- Incluir descrição completa de cada carro
- Comentário personalizado para cada modelo diferente

### Tool: `Criar_carros`

Cadastra novo veículo no catálogo (apenas admins).

**Uso**: Quando admin quer cadastrar veículo

**Pré-requisito**: 
1. Usar `consultar_usuario` para verificar se é admin
2. Coletar todas as informações do veículo

**Parâmetros**:
```json
{
  "placa": "ABC1234",
  "quilometragem": 50000,
  "modelo": "Hilux",
  "marca": "Toyota",
  "ano": 2022,
  "valor": 250000,
  "observacoes": "Carro em excelente estado"
}
```

### Tool: `consultar_usuario`

Consulta permissões do usuário.

**Uso**: Sempre antes de `Criar_carros`

**Parâmetros**:
```json
{
  "email": "admin@exemplo.com"
}
```

**Retorno**: Informações do usuário incluindo se é admin

## 📊 Sistema de Avaliação

### Como Funciona

Após cada resposta do agente, um sistema de avaliação analisa a conversa e atribui:

1. **Avaliação (0-10)**: Probabilidade de virar comprador
2. **Encaminhar Vendedor (true/false)**: Se deve encaminhar
3. **Comentário**: Justificativa com modelo do carro (se informado)

### Regras de Avaliação

- **0**: Admin ou mensagens administrativas
- **1-5**: Apenas perguntas, sem intenção de compra
- **6-10**: Demonstra intenção de compra
- **10**: Compra explícita ("quero comprar", "vou comprar")

### Encaminhamento para Vendedor

Encaminha quando:
- Avaliação >= 6 E intenção explícita de compra
- Frases como "quero comprar", "fechar negócio", "vou comprar"

**NÃO** encaminha para:
- Apenas orçamentos
- Consultas de modelo
- Mensagens administrativas

## 🔄 Sistema de Follow-up

### Como Funciona

Um cron job executa a cada 2 horas e verifica:

1. **Follow-up 2h**: Clientes com `status_followup = 0` e sem resposta há 2h
2. **Follow-up 6h**: Clientes com `status_followup = 1` e sem resposta há 6h
3. **Follow-up 24h**: Clientes com `status_followup = 2` e sem resposta há 24h

### Fluxo de Status

```
0 → [2h sem resposta] → Follow-up 2h → status = 1
1 → [6h sem resposta] → Follow-up 6h → status = 2
2 → [24h sem resposta] → Follow-up 24h → status = 3
```

### Reset de Follow-up

Quando cliente responde:
- `status_followup` volta para 0
- `ultimo_envio` é atualizado
- Follow-up recomeça do zero

### Cancelamento de Follow-up

Quando cliente é encaminhado para vendedor:
- `encaminha_vendedor = true`
- `data_vendedor = NOW()`
- Follow-up é cancelado

Após 48h encaminhado:
- `encaminha_vendedor` volta para `false`
- Follow-up pode recomeçar

## 🔒 Sistema de Bloqueio

### Detecção de Interferência Humana

Quando `fromMe = true`:
1. Cria chave de bloqueio no Redis
2. TTL de 5 minutos (300 segundos)
3. Próximas mensagens são ignoradas

### Verificação de Bloqueio

Antes de processar cada mensagem:
1. Verifica se existe chave de bloqueio
2. Se existe: ignora mensagem
3. Se não existe: processa normalmente

### Desbloqueio Manual

Comando especial (não implementado no workflow atual, mas pode ser adicionado):
- Mensagem específica para desbloquear
- Remove chave do Redis

## 📝 Exemplos de Uso

### Exemplo 1: Cliente Consultando Catálogo

**Cliente**: "Quero ver os carros disponíveis"
**Agente**: [Usa `consulta_catalogo`]
**Agente**: "Temos várias opções incríveis! 🚗"
**Agente**: [Envia informações e fotos de cada carro]
**Agente**: "Quer filtrar por marca, modelo, valor máximo ou quilometragem máxima?"

### Exemplo 2: Cliente com Intenção de Compra

**Cliente**: "Gostei desse Hilux, quero comprar"
**Agente**: [Processa mensagem]
**Sistema**: [Avalia: 10/10, encaminhar: true]
**Sistema**: [Encaminha para vendedor com comentário]
**Agente**: "Perfeito! Vou te conectar com nosso vendedor especializado. Ele vai te ajudar com todos os detalhes! 🚗"

### Exemplo 3: Admin Cadastrando Veículo

**Admin**: "Quero cadastrar um carro"
**Agente**: [Usa `consultar_usuario` - verifica se é admin]
**Agente**: "Claro! Preciso de algumas informações. Qual a placa?"
**Admin**: "ABC1234"
**Agente**: "Qual o modelo?"
[... coleta todas as informações ...]
**Agente**: [Usa `Criar_carros`]
**Agente**: "Veículo cadastrado com sucesso!"

## ⚠️ Observações Importantes

1. **Sempre enviar todas as fotos**: Quando usar filtros, enviar TODAS as fotos
2. **Descrição obrigatória**: Sempre incluir modelo, marca, valor, quilometragem, observações
3. **Comentário por modelo**: Cada modelo diferente precisa de comentário
4. **Não enviar placa**: Nunca enviar placa do veículo ao cliente
5. **Admin sempre avaliação 0**: Mensagens de admin não devem encaminhar vendedor
6. **Imagens separadas**: Enviar imagens em mensagens separadas do texto

## 🐛 Troubleshooting

### Problema: Catálogo não está sendo consultado

**Verificar**:
1. API Key do BancoAI está configurada?
2. `empresaId` está correto?
3. Endpoint está acessível?

### Problema: Follow-up não está funcionando

**Verificar**:
1. Cron job está ativo?
2. Tabela `messages` tem os campos corretos?
3. `ultimo_envio` está sendo atualizado?

### Problema: Avaliação sempre 0

**Verificar**:
1. Node "Avaliador" está funcionando?
2. Formatação JSON está correta?
3. Modelo GPT está configurado?

### Problema: Bloqueio não está funcionando

**Verificar**:
1. Redis está acessível?
2. Chave está sendo criada corretamente?
3. TTL está configurado?

## 📈 Melhorias Futuras

- [ ] Dashboard de métricas de vendas
- [ ] Análise de conversão por modelo
- [ ] Integração com CRM
- [ ] Sistema de agendamento de test drive
- [ ] Notificações de novos veículos
- [ ] Chatbot para financiamento


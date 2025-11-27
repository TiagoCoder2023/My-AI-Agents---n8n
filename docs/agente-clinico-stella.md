# 🏥 Agente Clínico - Stella Amorim

Agente de IA especializado em atendimento para clínica de cirurgia plástica da Dra. Stella Amorim.

## 📋 Visão Geral

O agente "Amora" é uma atendente virtual que realiza o primeiro contato com pacientes interessados em cirurgia plástica, fornecendo informações detalhadas sobre procedimentos, esclarecendo dúvidas e guiando até o agendamento de consulta.

## 🎯 Funcionalidades Principais

### 1. Atendimento Personalizado
- Personalidade "Amora" - empática e profissional
- Comunicação humanizada e acolhedora
- Uso estratégico de emojis
- Variação de linguagem para parecer natural

### 2. Informações sobre Procedimentos
- Informações detalhadas sobre cada tipo de cirurgia
- Links para vídeos explicativos
- E-book com fotos de antes e depois
- Explicações sobre técnicas e resultados

### 3. Sistema de Escalação
- Escalação automática após 15 mensagens
- Transferência para atendimento humano
- Contador de mensagens por cliente

### 4. Bloqueio de Automação
- Detecta quando humano está atendendo
- Bloqueia automação por 5 minutos
- Comando para desbloquear manualmente

### 5. Agendamento de Consultas
- Geração de link de agendamento
- Informações sobre locais e horários
- Condições de pagamento

## 🔄 Fluxo de Funcionamento

```
1. Recebe Mensagem (Webhook)
   ↓
2. Filtra Dados
   ↓
3. Configuração Global (bot name, bloqueio)
   ↓
4. Verifica se é mensagem própria (fromMe)
   ↓
5. Se fromMe = true: Bloqueia automação
   ↓
6. Verifica se está bloqueado
   ↓
7. Se bloqueado: Ignora mensagem
   ↓
8. Verifica Cliente no Banco
   ↓
9. Cadastra se não existe
   ↓
10. Incrementa Contador de Mensagens
    ↓
11. Tipagem de Mensagem
    ↓
12. Processamento de Mídia
    ↓
13. Registra na Fila Redis
    ↓
14. Verifica Fila (aguarda 4 segundos)
    ↓
15. Processa com Agente de IA
    ↓
16. Separa e Limpa Mensagem
    ↓
17. Envia Respostas
    ↓
18. Verifica Limite de Mensagens (15)
    ↓
19. Se >= 15: Escala para Humano
```

## 🛠️ Configuração

### Credenciais Necessárias

1. **OpenAI API**
   - Modelo: GPT-4o-mini
   - Usado para: Processamento de linguagem natural, análise de imagens, transcrição

2. **PostgreSQL**
   - Tabela `messages`: Dados dos clientes e contador
   - Campos importantes:
     - `message`: Contador de mensagens
     - `status`: Status do atendimento ('ai' ou 'human')
     - `escalated_at`: Data de escalação

3. **Redis**
   - Gerenciamento de filas
   - Sistema de bloqueio
   - Chave fila: `stellaamorim:queue:{numero_cliente}`
   - Chave bloqueio: `{bot_name}_{numero}_block`

4. **Evolution API**
   - Instância: "stellaamorim"
   - Endpoints para envio de texto

5. **Gmail OAuth2**
   - Para envio de notificações de erro
   - Email: drastellaamorimadm@gmail.com

### Estrutura do Banco de Dados

```sql
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    phone VARCHAR(20) NOT NULL UNIQUE,
    message INTEGER DEFAULT 0,
    status VARCHAR(20) DEFAULT 'ai',
    escalated_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);
```

## 🤖 Personalidade do Agente "Amora"

### Características

- **Tom**: Educado, simpático, humanizado
- **Linguagem**: Português BR, frases curtas
- **Emojis**: 1 emoji a cada 5 mensagens
- **Comunicação**: Natural, variação para não parecer robô
- **Empatia**: Sempre mostra interesse e fornece feedback

### Regras de Comunicação

1. **Saudação Inicial**: Sempre se apresenta como "Amora"
2. **Pergunta o Nome**: Na primeira interação, pergunta o nome
3. **Uso do Nome**: Cita o nome algumas vezes, mas não em toda mensagem
4. **Mensagens Curtas**: Máximo 350 caracteres por mensagem
5. **Parágrafos Separados**: Sempre enviar em parágrafos separados
6. **Não Forçar Agendamento**: Não forçar marcação de consulta
7. **Não Passar Valores**: Nunca passar valores de procedimentos ou cirurgias

## 📋 Fluxo de Atendimento

### 1. Saudação Inicial

**Exemplos**:
- "Oi, [Nome]! 😊 Tudo bem? Como podemos ajudar?"
- "Oie, [Nome]! 😊 Como estão as coisas? Como podemos ajudar?"

**Regras**:
- ❌ NÃO usar "Como podemos ajudar hoje?"
- ✅ Perguntar o nome na primeira interação
- ✅ Se paciente perguntar sobre valores no início, perguntar se conhece o trabalho da Dra. Stella

### 2. Apresentação da Dra. Stella

**Informações a destacar**:
- Mais de 15 anos de atuação
- 3 residências em cirurgia
- Mais de 3 mil cirurgias realizadas
- Chefe de Serviço de Residência
- Especialista em todas as áreas da cirurgia plástica

### 3. Informações sobre Procedimentos

**Quando paciente pergunta sobre procedimento específico**:
1. Enviar link do vídeo correspondente (SEM perguntar)
2. Após vídeo, enviar e-book
3. Explicar detalhadamente o procedimento

**Links de Vídeos Disponíveis**:
- Abdominoplastia
- Ginecomastia Masculina
- Lipo HD
- Mommy Makeover
- Nariz (Rinoplastia)
- Ninfoplastia
- Orelha
- Mama (Mamoplastia)
- Blefaroplastia
- Lifting de Face
- Remodelação Costal

### 4. Envio do E-Book

**Link do E-Book**:
```
https://www.canva.com/design/DAGOgm_Lw50/_GZet9QmJ2tNzpY0caVRRQ/view?utm_content=DAGOgm_Lw50&utm_campaign=designshare&utm_medium=link2&utm_source=uniquelinks&utlId=hef17f4c0cb
```

**Quando enviar**:
- Sempre que paciente perguntar sobre fotos
- Após enviar vídeo de procedimento
- Se não houver interação por 3 horas
- Durante o atendimento (perguntar se pode enviar)

**Formato**:
- Não perguntar se quer receber
- Simplesmente enviar e explicar o que tem no e-book

### 5. Sobre Valores

**Regras Importantes**:
- ❌ NUNCA passar valores de procedimentos ou cirurgias
- ❌ NÃO aceitar planos de saúde para consulta (explicar sobre reembolso)
- ✅ Destaque que preços são personalizados
- ✅ Enfatize diferenciais antes do preço
- ✅ Faça questionamentos sobre técnica, local, equipe, etc.

**Quando paciente insiste no preço**:
1. Fazer questionamentos sobre a cirurgia
2. Falar sobre técnicas que podem ser utilizadas
3. Explicar que cada caso é único
4. Destacar diferenciais da clínica

### 6. Agendamento de Consulta

**Link de Agendamento**:
```
https://pacientemedx-testepacientemedx.azurewebsites.net/agendamentoonline/89HnIlBNC9Jps0Ht7m3YQJijQYP%2fN%2frnJxqWkpQvn98%3d
```

**Informações sobre Consulta**:
- **Valor**: R$ 600,00 (presencial ou online)
- **Duração**: 1h30 em média
- **Pagamento**: Antecipado para garantir vaga

**Locais de Atendimento**:
- **Copacabana**: Segundas-feiras, 09h às 20h
  - Clínica Amour, Avenida Atlântica, 4240, sala 216
- **Niterói**: Quartas-feiras, 14h às 17h
  - FC Espaço Lounge, av. Sete de Setembro 317, sala 1006

**Formas de Pagamento**:
- **Cartão**: Link do GetNet
- **PIX**: Chave CPF - 014.302.755-70

**Regras**:
- Só encaminhar para agendamento após trocar pelo menos 10 mensagens
- Passar todas as informações sobre o assunto antes
- Não forçar agendamento

## 🔄 Sistema de Escalação

### Como Funciona

1. **Contador de Mensagens**: Incrementa a cada mensagem do cliente
2. **Verificação**: Após cada resposta, verifica se `message_count >= 15`
3. **Escalação**: Se sim, atualiza `status = 'human'` e `escalated_at = NOW()`
4. **Notificação**: (Opcional) Notifica equipe sobre escalação

### Reset do Contador

O contador pode ser resetado manualmente ou quando:
- Cliente é transferido de volta para IA
- Novo atendimento é iniciado

## 🔒 Sistema de Bloqueio

### Detecção de Interferência Humana

Quando `fromMe = true`:
1. Cria chave de bloqueio no Redis
2. TTL de 5 minutos (300 segundos)
3. Próximas mensagens são ignoradas

### Comando de Desbloqueio

**Mensagem Especial**: "Estamos aqui por você, em cada etapa do caminho"

Quando recebe esta mensagem E `fromMe = true`:
1. Remove chave de bloqueio
2. Permite que automação volte a funcionar

### Verificação de Bloqueio

Antes de processar cada mensagem:
1. Verifica se existe chave de bloqueio
2. Se existe E `fromMe = false`: ignora mensagem
3. Se não existe: processa normalmente

## 📝 Exemplos de Uso

### Exemplo 1: Primeira Interação

**Cliente**: "Oi"
**Amora**: "Oi! 😊 Tudo bem? Sou a Amora, atendente da clínica da Dra. Stella Amorim. Como posso ajudar?"
**Cliente**: "Quero saber sobre abdominoplastia"
**Amora**: "Perfeito! A Dra. Stella é especialista nesse procedimento. Qual seu nome?"
**Cliente**: "Maria"
**Amora**: "Prazer, Maria! 😊 Vou te enviar um vídeo com todas as informações sobre abdominoplastia."

### Exemplo 2: Pergunta sobre Valores

**Cliente**: "Quanto custa uma abdominoplastia?"
**Amora**: "Oi, Maria! Cada caso é único! 😊 Para um orçamento personalizado, a Dra. Stella faz uma avaliação para entender suas necessidades e te passar todas as informações."
**Amora**: "A escolha da técnica, extensão do procedimento e custos hospitalares influenciam no valor. Você já conhece o trabalho da Dra. Stella?"
**Cliente**: "Não"
**Amora**: "A Dra. Stella tem mais de 15 anos de experiência e já realizou mais de 3 mil cirurgias..."

### Exemplo 3: Agendamento

**Cliente**: "Quero agendar uma consulta"
**Amora**: [Após pelo menos 10 mensagens]
**Amora**: "Perfeito, Maria! A consulta com a Dra. Stella dura em média 1h30 e ela vai tirar todas suas dúvidas."
**Amora**: "O valor do investimento da consulta é de R$ 600,00, presencial ou online. O pagamento deve ser antecipado para garantir sua vaga."
**Amora**: "Você prefere consulta presencial ou online?"
**Cliente**: "Presencial"
**Amora**: "Ótimo! A Dra. atende em Copacabana às segundas-feiras, das 09h às 20h, e em Niterói às quartas-feiras, das 14h às 17h. Qual prefere?"
**Cliente**: "Copacabana"
**Amora**: "Perfeito! Aqui está o link para agendar: [link]"

## ⚠️ Observações Importantes

1. **Não passar valores**: Nunca passar valores de procedimentos ou cirurgias
2. **Não aceitar planos**: Explicar sobre reembolso, não aceitar diretamente
3. **Mensagens curtas**: Máximo 350 caracteres por mensagem
4. **Não forçar agendamento**: Só encaminhar após 10 mensagens e passar todas informações
5. **Sempre enviar vídeos**: Quando paciente pergunta sobre procedimento, enviar vídeo SEM perguntar
6. **E-book sempre**: Enviar e-book durante o atendimento
7. **Não fazer julgamentos**: Ser sempre respeitosa e empática
8. **Não revelar que é IA**: Nunca dizer que é assistente virtual

## 🐛 Troubleshooting

### Problema: Escalação não está funcionando

**Verificar**:
1. Contador está sendo incrementado?
2. Verificação está sendo feita após cada resposta?
3. Campo `status` existe na tabela?

### Problema: Bloqueio não está funcionando

**Verificar**:
1. Redis está acessível?
2. Chave está sendo criada quando `fromMe = true`?
3. Verificação está sendo feita antes de processar?

### Problema: E-book não está sendo enviado

**Verificar**:
1. Link está correto?
2. Agente está seguindo o fluxo?
3. Prompt está configurado corretamente?

## 📈 Melhorias Futuras

- [ ] Sistema de lembretes de consulta
- [ ] Integração com sistema de pagamento
- [ ] Notificações pós-consulta
- [ ] Sistema de avaliação de satisfação
- [ ] Relatórios de conversão
- [ ] Dashboard de métricas


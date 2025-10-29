# 🔐 Segurança e Melhores Práticas

## Visão Geral

Este documento contém diretrizes essenciais de segurança para o sistema Dalvonete IA Agent. Seguir estas práticas é fundamental para proteger dados de clientes e manter a integridade do sistema.

## 🚨 Checklist de Segurança Imediata

Antes de colocar o sistema em produção, complete TODOS os itens:

- [ ] Alterar TODAS as senhas padrão
- [ ] Ativar autenticação de dois fatores (quando disponível)
- [ ] Configurar HTTPS em todos os endpoints
- [ ] Revogar tokens antigos da Z-API
- [ ] Configurar IP whitelist no Easypanel
- [ ] Remover credenciais de arquivos commitados no Git
- [ ] Configurar backup automático
- [ ] Implementar monitoramento de erros
- [ ] Configurar alertas de segurança
- [ ] Revisar permissões de todas as integrações

## 1. Gestão de Credenciais

### 1.1. Senhas

#### ❌ Práticas RUINS:
```
- Senha: 123456
- Senha: Cliente4321@
- Usar mesma senha em múltiplos serviços
- Compartilhar senhas por email/WhatsApp
- Salvar senhas em arquivos de texto
```

#### ✅ Práticas BOAS:
```
- Senha: K9$mP2xL#vQ8wR5nF (aleatória, 16+ caracteres)
- Senha única para cada serviço
- Usar gerenciador de senhas (LastPass, 1Password, Bitwarden)
- Nunca compartilhar senhas
- Rotacionar senhas a cada 90 dias
```

#### Requisitos Mínimos de Senha:
- Mínimo 12 caracteres
- Letras maiúsculas e minúsculas
- Números
- Símbolos especiais
- Não usar palavras do dicionário
- Não usar informações pessoais

#### Onde Alterar Senhas:

**Easypanel:**
1. Login em `hjmnzb.easypanel.host`
2. Settings → Security → Change Password

**n8n:**
1. Login em n8n
2. Settings → Users → Seu perfil → Change Password

**Emails:**
1. Gmail: Configurações de conta Google
2. Ativar verificação em duas etapas

### 1.2. Tokens e API Keys

#### Z-API

**Rotacionar tokens regularmente:**
1. Acesse painel Z-API
2. Gere novo token
3. Atualize em TODOS os workflows
4. Revogue token antigo
5. Teste funcionamento

**Armazenamento seguro:**
```javascript
// ❌ NUNCA faça isso:
const token = "9F8363362C4A59C1CC7D5326";

// ✅ Use variáveis de ambiente no n8n:
const token = $env.ZAPI_TOKEN;
```

#### Google OAuth2

**Tokens de acesso:**
- Renovados automaticamente
- Verificar expiração regularmente
- Reconectar se necessário

**Client Secret:**
- Nunca commite no Git
- Armazene em local seguro
- Rotacione anualmente

### 1.3. Variáveis de Ambiente

**Configure no n8n:**
```env
# Arquivo .env (nunca comite!)
ZAPI_TOKEN=SEU_TOKEN_AQUI
ZAPI_CLIENT_TOKEN=SEU_CLIENT_TOKEN
GOOGLE_CALENDAR_ID=SEU_CALENDAR_ID
```

**Acesse nos workflows:**
```javascript
$env.ZAPI_TOKEN
$env.ZAPI_CLIENT_TOKEN
```

## 2. Segurança de Rede

### 2.1. HTTPS Obrigatório

**Todos os endpoints DEVEM usar HTTPS:**

```
✅ https://n8n-n8n-start.degwfm.easypanel.host
✅ https://api.z-api.io
✅ https://SEUDOMINIO.com.br/dalvonete/video.mp4
❌ http://... (NUNCA use HTTP)
```

**Verificar certificado SSL:**
```bash
# Teste SSL
openssl s_client -connect n8n-n8n-start.degwfm.easypanel.host:443

# Verificar expiração
echo | openssl s_client -servername SEUDOMINIO.com.br -connect SEUDOMINIO.com.br:443 2>/dev/null | openssl x509 -noout -dates
```

### 2.2. Firewall e IP Whitelist

#### Easypanel

**Configure IP whitelist:**
1. Acesse Easypanel
2. Settings → Security → IP Whitelist
3. Adicione IPs permitidos:
   ```
   123.456.789.0/24  # Sua rede
   ```

#### n8n Webhooks

**Proteja webhooks sensíveis:**
```javascript
// No webhook, adicione validação:
const validIPs = ['IP_DA_ZAPI', 'OUTRO_IP_CONFIAVEL'];
const requestIP = $('Webhook').item.json.headers['x-forwarded-for'];

if (!validIPs.includes(requestIP)) {
  throw new Error('IP não autorizado');
}
```

### 2.3. Rate Limiting

**Implemente limites de requisição:**

```javascript
// No n8n, rastreie requisições por IP
const redis = $('Redis').item.json;
const requestsInLastMinute = redis.get(`requests:${requestIP}`);

if (requestsInLastMinute > 60) {
  throw new Error('Rate limit excedido');
}
```

**Z-API já tem rate limit:**
- 100 mensagens/minuto por instância
- Respeite este limite

## 3. Proteção de Dados

### 3.1. LGPD / Privacidade

**Dados coletados:**
- Nome do cliente
- Telefone
- Cidade
- Data do evento
- Preferências

**Obrigações LGPD:**
1. **Consentimento:**
   - Informar cliente sobre coleta de dados
   - Obter consentimento explícito
   - Exemplo: "Ao continuar, você aceita nossa política de privacidade"

2. **Direito ao esquecimento:**
   - Cliente pode solicitar exclusão de dados
   - Implementar processo para deletar dados

3. **Segurança:**
   - Criptografar dados sensíveis
   - Acesso restrito a dados

4. **Transparência:**
   - Política de privacidade disponível
   - Informar como dados são usados

**Exemplo de mensagem inicial:**
```
Olá! Sou o assistente virtual da Dalvonete.

Ao conversar comigo, você concorda com nossa política de privacidade: [link]
Seus dados serão usados apenas para gerenciar sua reserva.

Tem alguma dúvida sobre disponibilidade?
```

### 3.2. Criptografia

**Dados em trânsito:**
- ✅ HTTPS em todos os endpoints
- ✅ TLS 1.2+ obrigatório

**Dados em repouso:**
```javascript
// Para dados muito sensíveis, criptografe:
const crypto = require('crypto');
const algorithm = 'aes-256-cbc';
const key = process.env.ENCRYPTION_KEY;

function encrypt(text) {
  const iv = crypto.randomBytes(16);
  const cipher = crypto.createCipheriv(algorithm, Buffer.from(key), iv);
  let encrypted = cipher.update(text);
  encrypted = Buffer.concat([encrypted, cipher.final()]);
  return iv.toString('hex') + ':' + encrypted.toString('hex');
}
```

### 3.3. Sanitização de Dados

**Sempre sanitize entrada do usuário:**

```javascript
// Sanitize telefone
function sanitizePhone(phone) {
  // Remove tudo exceto números
  const cleaned = phone.replace(/\D/g, '');
  
  // Valida formato
  if (cleaned.length !== 13 || !cleaned.startsWith('55')) {
    throw new Error('Telefone inválido');
  }
  
  return cleaned;
}

// Sanitize nome (previne XSS)
function sanitizeName(name) {
  return name
    .replace(/[<>]/g, '') // Remove < e >
    .trim()
    .slice(0, 100); // Limita tamanho
}

// Sanitize data
function sanitizeDate(date) {
  const regex = /^\d{4}-\d{2}-\d{2}$/;
  if (!regex.test(date)) {
    throw new Error('Data inválida');
  }
  return date;
}
```

### 3.4. Logs e Auditoria

**O que logar:**
- ✅ Tentativas de login (sucesso e falha)
- ✅ Alterações em workflows
- ✅ Acesso a dados sensíveis
- ✅ Erros e exceções
- ❌ Senhas ou tokens
- ❌ Dados completos de clientes

**Formato de log:**
```json
{
  "timestamp": "2025-10-29T10:30:00Z",
  "level": "INFO",
  "action": "lead_cadastrado",
  "user": "sistema",
  "details": {
    "telefone": "5537999***999", // Parcialmente mascarado
    "ocasiao": "chalé"
  }
}
```

**Retenção de logs:**
- Mínimo: 6 meses
- Máximo: 2 anos
- Após período, deletar automaticamente

## 4. Segurança de Workflows

### 4.1. Princípio do Menor Privilégio

**Credenciais Google:**
```
❌ Não dê acesso total: Editor de todos os arquivos
✅ Dê acesso específico: Apenas agenda CHALÉ e planilha específica
```

**Configurar scopes mínimos:**
```
Google Calendar:
- Scope: calendar.events.readonly
- Não use: calendar (full access)

Google Sheets:
- Scope: spreadsheets (específica)
- Não use: drive (full access)
```

### 4.2. Validação de Entrada

**SEMPRE valide dados antes de processar:**

```javascript
// No início de cada workflow
const input = $json;

// Validações
if (!input.telefone || typeof input.telefone !== 'string') {
  throw new Error('Telefone obrigatório');
}

if (!input.nome || input.nome.length < 2) {
  throw new Error('Nome inválido');
}

if (!input.data_do_evento || !/^\d{4}-\d{2}-\d{2}$/.test(input.data_do_evento)) {
  throw new Error('Data inválida');
}

// Data no passado?
const eventDate = new Date(input.data_do_evento);
const today = new Date();
if (eventDate < today) {
  throw new Error('Data deve ser futura');
}
```

### 4.3. Tratamento de Erros

**Nunca exponha detalhes internos:**

```javascript
// ❌ NUNCA faça isso:
try {
  // código
} catch (error) {
  return {
    error: error.stack,
    credentials: process.env
  };
}

// ✅ Faça isso:
try {
  // código
} catch (error) {
  // Log interno (detalhado)
  console.error('Erro ao cadastrar lead:', error);
  
  // Resposta ao cliente (genérica)
  return {
    success: false,
    message: 'Erro ao processar solicitação. Por favor, tente novamente.'
  };
}
```

### 4.4. Timeout e Limites

**Configure timeouts:**
```javascript
// No n8n, configure timeout em cada nó HTTP Request
{
  "timeout": 10000, // 10 segundos
  "retry": {
    "enabled": true,
    "maxRetries": 3,
    "waitBetween": 1000
  }
}
```

**Limites de dados:**
```javascript
// Limite tamanho de upload
const maxVideoSize = 16 * 1024 * 1024; // 16MB
const maxImageSize = 5 * 1024 * 1024;  // 5MB

if (videoSize > maxVideoSize) {
  throw new Error('Vídeo muito grande');
}
```

## 5. Segurança de Mídia

### 5.1. Servidor de Arquivos

**Configuração segura:**

```nginx
# nginx.conf
server {
  listen 443 ssl;
  server_name SEUDOMINIO.com.br;
  
  # SSL
  ssl_certificate /path/to/cert.pem;
  ssl_certificate_key /path/to/key.pem;
  ssl_protocols TLSv1.2 TLSv1.3;
  
  # Headers de segurança
  add_header X-Frame-Options "SAMEORIGIN" always;
  add_header X-Content-Type-Options "nosniff" always;
  add_header X-XSS-Protection "1; mode=block" always;
  
  # CORS (apenas para n8n)
  add_header Access-Control-Allow-Origin "https://n8n-n8n-start.degwfm.easypanel.host";
  
  location /dalvonete/ {
    root /var/www/html;
    
    # Previne listagem de diretório
    autoindex off;
    
    # Permite apenas GET
    limit_except GET {
      deny all;
    }
    
    # Cache
    expires 1d;
    add_header Cache-Control "public, immutable";
  }
}
```

### 5.2. Proteção contra Hotlinking

**Previna uso não autorizado:**
```nginx
# Bloqueia hotlinking
valid_referers none blocked 
  SEUDOMINIO.com.br 
  n8n-n8n-start.degwfm.easypanel.host;

if ($invalid_referer) {
  return 403;
}
```

### 5.3. Versionamento de Arquivos

**Use versionamento para evitar cache:**
```
❌ https://SEUDOMINIO.com.br/dalvonete/video.mp4
✅ https://SEUDOMINIO.com.br/dalvonete/video_v1.mp4
✅ https://SEUDOMINIO.com.br/dalvonete/video.mp4?v=2025-10-29
```

## 6. Monitoramento de Segurança

### 6.1. Alertas

**Configure alertas para:**

1. **Múltiplas falhas de login**
   ```javascript
   if (failedLoginCount > 5) {
     sendAlert('Possível ataque de força bruta');
   }
   ```

2. **Acesso de IP suspeito**
   ```javascript
   const knownBadIPs = ['IP1', 'IP2'];
   if (knownBadIPs.includes(requestIP)) {
     sendAlert('Acesso de IP suspeito');
     blockIP(requestIP);
   }
   ```

3. **Uso anormal de recursos**
   ```javascript
   if (cpuUsage > 90 || ramUsage > 90) {
     sendAlert('Uso anormal de recursos - possível DDoS');
   }
   ```

4. **Credenciais expirando**
   ```javascript
   const daysUntilExpiry = calculateDays(oauthToken.expires_at);
   if (daysUntilExpiry < 7) {
     sendAlert('Token OAuth expirando em ' + daysUntilExpiry + ' dias');
   }
   ```

### 6.2. Audit Log

**Implemente logging de ações críticas:**

```javascript
function auditLog(action, user, details) {
  const log = {
    timestamp: new Date().toISOString(),
    action: action,
    user: user,
    ip: $('Webhook').item.json.headers['x-forwarded-for'],
    details: details,
    severity: getSeverity(action)
  };
  
  // Salve em local seguro
  saveToSecureLog(log);
  
  // Para ações críticas, envie notificação
  if (log.severity === 'HIGH') {
    sendSecurityAlert(log);
  }
}

// Uso:
auditLog('workflow_modified', 'admin@dalvonete.com', {
  workflow: 'MCP DALVONETE',
  changes: 'Added new tool'
});
```

### 6.3. Testes de Segurança

**Realize regularmente:**

1. **Teste de penetração básico:**
   ```bash
   # Teste headers de segurança
   curl -I https://SEUDOMINIO.com.br
   
   # Teste vulnerabilidades conhecidas
   nikto -h https://SEUDOMINIO.com.br
   ```

2. **Scan de portas:**
   ```bash
   nmap -sV SEUDOMINIO.com.br
   ```

3. **Teste de SQL injection:**
   - Tente injetar SQL em campos de entrada
   - Exemplo: `'; DROP TABLE users; --`
   - Sistema deve rejeitar

4. **Teste de XSS:**
   - Tente injetar scripts
   - Exemplo: `<script>alert('XSS')</script>`
   - Sistema deve sanitizar

## 7. Backup e Recuperação

### 7.1. Estratégia de Backup

**Regra 3-2-1:**
- 3 cópias dos dados
- 2 tipos de mídia diferentes
- 1 cópia offsite

**O que fazer backup:**
```
✅ Workflows do n8n (export JSON)
✅ Credenciais (criptografadas)
✅ Configurações do Easypanel
✅ Arquivos de mídia (video.mp4, imagem.jpg)
✅ Planilhas Google Sheets (export)
✅ Eventos Google Calendar (export)
❌ Logs (muitos dados, fazer backup seletivo)
```

**Frequência:**
```
Diário:
- Workflows n8n
- Google Sheets

Semanal:
- Configurações Easypanel
- Arquivos de mídia

Mensal:
- Google Calendar
- Audit logs
```

### 7.2. Teste de Restauração

**Teste backup mensalmente:**

1. Crie ambiente de teste
2. Restaure último backup
3. Verifique funcionalidade
4. Documente tempo de restauração
5. Identifique melhorias

### 7.3. Plano de Disaster Recovery

**Cenário 1: n8n indisponível**
- Tempo de recuperação: 2 horas
- Ação: Restaurar container no Easypanel

**Cenário 2: Dados corrompidos**
- Tempo de recuperação: 4 horas
- Ação: Restaurar backup mais recente

**Cenário 3: Ataque ransomware**
- Tempo de recuperação: 8 horas
- Ação: Restaurar de backup offsite

## 8. Compliance e Regulamentação

### 8.1. LGPD

**Checklist LGPD:**
- [ ] Política de privacidade publicada
- [ ] Termo de consentimento implementado
- [ ] Processo para direito ao esquecimento
- [ ] Processo para portabilidade de dados
- [ ] DPO (Data Protection Officer) designado
- [ ] Registro de tratamento de dados
- [ ] Avaliação de impacto (DPIA)
- [ ] Contratos com processadores

### 8.2. Documentação Obrigatória

1. **Política de Privacidade**
2. **Termos de Uso**
3. **Registro de Atividades de Tratamento**
4. **Plano de Resposta a Incidentes**
5. **Política de Retenção de Dados**

## 9. Treinamento de Equipe

### 9.1. Conscientização

**Treine equipe sobre:**
- Phishing e engenharia social
- Gestão segura de senhas
- Identificação de atividades suspeitas
- Procedimentos de resposta a incidentes
- LGPD e privacidade de dados

### 9.2. Políticas Internas

**Implemente políticas:**

1. **Uso Aceitável:**
   - Nunca compartilhar credenciais
   - Reportar acessos suspeitos imediatamente
   - Não usar dispositivos pessoais não autorizados

2. **Clean Desk:**
   - Não deixar senhas anotadas
   - Fazer logout ao sair
   - Bloquear tela quando ausente

3. **Resposta a Incidentes:**
   - Quem notificar
   - Como isolar sistema
   - Documentação necessária

## 10. Checklist de Segurança Mensal

- [ ] Revisar logs de acesso
- [ ] Verificar expiração de certificados SSL
- [ ] Testar backups
- [ ] Revisar usuários com acesso
- [ ] Atualizar sistemas (n8n, Easypanel)
- [ ] Verificar alertas de segurança
- [ ] Revisar políticas de senha
- [ ] Testar procedimentos de disaster recovery
- [ ] Audit de permissões Google APIs
- [ ] Verificar conformidade LGPD

---

## 📞 Contato de Segurança

**Reportar incidente de segurança:**
- Email: security@dalvonete.com.br
- Tempo de resposta: < 24 horas
- Para incidentes críticos: Ligar imediatamente

---

**Última atualização:** Outubro 2025  
**Próxima revisão:** Janeiro 2026

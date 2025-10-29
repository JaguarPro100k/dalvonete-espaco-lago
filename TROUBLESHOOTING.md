# 🔧 Guia de Troubleshooting

## Problemas Comuns e Soluções

### 1. Vídeo não é enviado

#### Sintoma
O workflow `envia_video` executa, mas o vídeo não chega no WhatsApp.

#### Possíveis Causas e Soluções

**Causa 1: URL do vídeo inacessível**
```bash
# Teste a URL do vídeo
curl -I https://SEUDOMINIO.com.br/dalvonete/video.mp4

# Deve retornar:
HTTP/1.1 200 OK
Content-Type: video/mp4
```

✅ **Solução:**
- Verifique se o arquivo está no servidor
- Confirme permissões do arquivo (644)
- Teste acesso direto no navegador

**Causa 2: Arquivo muito grande**
```bash
# Verifique o tamanho
curl -sI https://SEUDOMINIO.com.br/dalvonete/video.mp4 | grep -i content-length

# Limite Z-API: 16MB (16777216 bytes)
```

✅ **Solução:**
```bash
# Comprimir vídeo com FFmpeg
ffmpeg -i video.mp4 -vcodec h264 -acodec aac -b:v 1M -b:a 128k video_compressed.mp4
```

**Causa 3: Token Z-API inválido/expirado**

✅ **Solução:**
1. Acesse painel da Z-API
2. Verifique status da instância
3. Gere novo token se necessário
4. Atualize no workflow

**Causa 4: Telefone em formato incorreto**

✅ **Formato correto:**
```
❌ Errado: +55 37 9935-1255
❌ Errado: (37) 99351-1255
✅ Correto: 553799351255
```

**Causa 5: Base64 mal formatado**

✅ **Solução:**
- Verifique o nó "Extract from File"
- Confirme que está gerando base64 válido
- Teste manualmente:
```bash
base64 video.mp4 > video_base64.txt
# Use este base64 em teste manual
```

#### Debug Passo a Passo

1. **Teste download do vídeo:**
   ```bash
   curl -o test_video.mp4 https://SEUDOMINIO.com.br/dalvonete/video.mp4
   # Deve baixar o arquivo sem erros
   ```

2. **Teste conversão base64:**
   - Execute apenas os nós HTTP Request e Extract from File
   - Verifique o output no n8n

3. **Teste API Z-API manualmente:**
   ```bash
   # Usando Postman ou curl
   curl -X POST https://api.z-api.io/instances/SEU_INSTANCE/token/SEU_TOKEN/send-video \
     -H "Client-Token: SEU_CLIENT_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{
       "phone": "553799999999",
       "video": "data:video/mp4;base64,BASE64_AQUI"
     }'
   ```

---

### 2. Lead não cadastrado no CRM

#### Sintoma
O workflow executa, mas o lead não aparece no CRM.

#### Possíveis Causas e Soluções

**Causa 1: Workflow de CRM não encontrado**

✅ **Solução:**
1. No n8n, verifique se existe workflow com ID: `5d3jpJxl5JjFhGNg`
2. Se não existir, crie ou importe
3. Atualize o ID no nó `cadastra_lead_CRM`

**Causa 2: Formato de data incorreto**

❌ Formatos errados:
```
15/12/2025
15-12-2025
2025/12/15
```

✅ Formato correto:
```
2025-12-15
```

**Solução:**
```javascript
// No n8n, use esta expressão para converter:
{{ $fromAI("data_do_evento").split('/').reverse().join('-') }}
```

**Causa 3: Telefone sem DDI/DDD**

✅ **Validação:**
```javascript
// Adicione validação no workflow
const phone = $json.telefone;
if (phone.length !== 13 || !phone.startsWith('55')) {
  throw new Error('Telefone inválido. Use formato: 553799999999');
}
```

**Causa 4: Campo "ocasião" com valor inválido**

✅ **Valores aceitos:**
- "chalé" (exatamente assim, com acento)
- "evento"

**Causa 5: Permissões do workflow de CRM**

✅ **Solução:**
1. Abra o workflow de CRM
2. Vá em Settings → Share
3. Garanta que está compartilhado ou público

#### Debug Passo a Passo

1. **Teste o workflow de CRM isoladamente:**
   - Execute manualmente com dados de teste
   - Verifique os logs de execução

2. **Verifique os logs do n8n:**
   - Vá em Executions
   - Encontre a execução falhada
   - Analise o erro exato

3. **Valide cada campo:**
   ```json
   {
     "nome": "João Teste",           // ✅ String
     "telefone": "5537999999999",    // ✅ 13 dígitos
     "ocasiao": "chalé",             // ✅ Exato
     "data_do_evento": "2025-12-15", // ✅ YYYY-MM-DD
     "cidade": "Divinópolis"         // ✅ String
   }
   ```

---

### 3. Calendar não retorna eventos

#### Sintoma
A tool `busca_evento_calendario` não retorna resultados ou retorna erro.

#### Possíveis Causas e Soluções

**Causa 1: Credenciais OAuth2 expiradas**

✅ **Solução:**
1. No n8n, vá em Credentials
2. Encontre "Google Calendar dalvonete"
3. Clique em "Reconnect"
4. Autorize novamente

**Causa 2: ID da agenda incorreto**

✅ **Solução:**
1. No Google Calendar, vá na agenda
2. Clique em ⋮ → "Configurações e compartilhamento"
3. Role até "Integrar agenda"
4. Copie o "ID da agenda"
5. Cole no workflow (deve ter formato: xxxxx@group.calendar.google.com)

**Causa 3: Agenda não compartilhada**

✅ **Solução:**
1. No Google Calendar, configurações da agenda
2. "Compartilhar com pessoas específicas"
3. Adicione o email usado no OAuth2
4. Permissão: "Fazer alterações em eventos"

**Causa 4: Formato de data inválido**

❌ Formato errado:
```
2025-11-01
01/11/2025
```

✅ Formato correto (ISO 8601):
```
2025-11-01T00:00:00Z
```

**Causa 5: API do Google Calendar não ativada**

✅ **Solução:**
1. Acesse [Google Cloud Console](https://console.cloud.google.com)
2. Selecione seu projeto
3. Vá em "APIs & Services" → "Library"
4. Busque "Google Calendar API"
5. Clique em "Enable"

#### Debug Passo a Passo

1. **Teste credencial:**
   ```bash
   # No n8n, execute o nó isoladamente
   # Ou teste via API do Google:
   curl "https://www.googleapis.com/calendar/v3/calendars/ID_DA_AGENDA/events?key=SUA_API_KEY"
   ```

2. **Verifique datas:**
   ```javascript
   // Use expressão no n8n para debug
   console.log('timeMin:', $json.timeMin);
   console.log('timeMax:', $json.timeMax);
   ```

3. **Teste permissões:**
   - Tente criar um evento manualmente na agenda
   - Se não conseguir, há problema de permissão

---

### 4. Erro 401 Unauthorized na Z-API

#### Sintoma
```
Error: 401 Unauthorized
```

#### Possíveis Causas e Soluções

**Causa 1: Token expirado**

✅ **Solução:**
1. Acesse o painel da Z-API
2. Vá em suas instâncias
3. Gere novo token
4. Atualize nos workflows:
   - `envia_video` → HTTP Request6 e HTTP Request7

**Causa 2: Client-Token incorreto**

✅ **Solução:**
1. Verifique o Client-Token no painel Z-API
2. Deve ser: `F6ebb1eaa3864462d97a9207743613045S`
3. Atualize no header dos requests

**Causa 3: Instância suspensa/inativa**

✅ **Solução:**
1. No painel Z-API, verifique status da instância
2. Se suspensa, reative
3. Aguarde sincronização (pode levar alguns minutos)

**Causa 4: IP bloqueado**

✅ **Solução:**
1. Verifique se seu servidor está na whitelist
2. No painel Z-API: Security → IP Whitelist
3. Adicione o IP do servidor n8n

#### Teste Manual

```bash
# Teste autenticação
curl -X GET "https://api.z-api.io/instances/SEU_INSTANCE/token/SEU_TOKEN/status" \
  -H "Client-Token: SEU_CLIENT_TOKEN"

# Resposta esperada:
{
  "connected": true,
  "session": "active"
}
```

---

### 5. Google Sheets - Erro ao atualizar trava

#### Sintoma
Erro ao tentar atualizar a planilha de trava de atendimento.

#### Possíveis Causas e Soluções

**Causa 1: Permissões da planilha**

✅ **Solução:**
1. Abra a planilha: [Link]
2. Compartilhar → Adicione o email do OAuth2
3. Permissão: "Editor"

**Causa 2: Nome da sheet incorreto**

✅ **Solução:**
- Nome deve ser exatamente: "PÃ¡gina1"
- Verifique se não há espaços extras

**Causa 3: Estrutura de colunas diferente**

✅ **Estrutura esperada:**
```
| telefone      | travar IA? | Ultimo envio |
|---------------|------------|--------------|
```

**Causa 4: Credenciais OAuth2 expiradas**

✅ **Solução:**
- Mesma solução do Google Calendar
- Reconecte as credenciais

**Causa 5: API do Google Sheets não ativada**

✅ **Solução:**
1. Google Cloud Console
2. Ative "Google Sheets API"

---

### 6. MCP Server não responde

#### Sintoma
O MCP Server Trigger não está ativo ou não responde.

#### Possíveis Causas e Soluções

**Causa 1: Workflow não ativado**

✅ **Solução:**
1. Abra o workflow "MCP DALVONETE"
2. Clique no toggle "Active"
3. Confirme que está verde

**Causa 2: Webhook ID mudou**

✅ **Solução:**
1. Anote o Webhook ID do nó MCP Server Trigger
2. Verifique se é o mesmo nas configurações do cliente MCP

**Causa 3: n8n não está acessível**

✅ **Solução:**
```bash
# Teste acesso ao n8n
curl -I https://n8n-n8n-start.degwfm.easypanel.host

# Deve retornar: HTTP/1.1 200 OK
```

**Causa 4: Falta de recursos no servidor**

✅ **Solução:**
1. No Easypanel, verifique uso de CPU/RAM
2. Se necessário, aumente recursos do container n8n
3. Reinicie o serviço n8n

---

## 📊 Logs e Monitoramento

### Acessar Logs do n8n

1. **Via interface:**
   - Vá em "Executions"
   - Filtre por status (Error, Success, Waiting)
   - Clique na execução para ver detalhes

2. **Via Easypanel:**
   - Abra o serviço n8n
   - Clique em "Logs"
   - Veja logs em tempo real

### Logs importantes a monitorar

```
✅ Sucesso: Workflow completed successfully
❌ Erro: Error in node 'nome_do_no'
⚠️  Aviso: Rate limit reached
```

### Configurar Alertas

1. **Email em caso de erro:**
   ```
   - Adicione nó "Email" após Error Trigger
   - Configure SMTP
   - Envie notificação para: arbnbdalvanete@gmail.com
   ```

2. **Webhook de monitoramento:**
   ```
   - Adicione nó "Webhook" após Error Trigger
   - Configure para enviar para serviço de monitoramento
   ```

---

## 🔍 Ferramentas de Debug

### 1. Postman/Insomnia
Use para testar APIs manualmente:
- Z-API endpoints
- Webhooks do n8n

### 2. curl
```bash
# Teste download de vídeo
curl -o test.mp4 https://SEUDOMINIO.com.br/dalvonete/video.mp4

# Teste API
curl -X POST URL_DO_WEBHOOK \
  -H "Content-Type: application/json" \
  -d '{"test": "data"}'
```

### 3. Browser DevTools
- Abra F12 no navegador
- Aba Network: Veja requests em tempo real
- Aba Console: Verifique erros JavaScript

### 4. n8n Debug Mode
```bash
# No Easypanel, adicione variável de ambiente:
N8N_LOG_LEVEL=debug

# Reinicie o container
```

---

## 📞 Quando Pedir Ajuda

Se após todas as tentativas o problema persistir:

1. **Reúna informações:**
   - Logs completos do erro
   - Passos exatos que causaram o problema
   - Screenshots do erro
   - Versão do n8n
   - Configurações relevantes

2. **Entre em contato:**
   - Email: arbnbdalvanete@gmail.com
   - Anexe as informações coletadas

3. **Comunidade n8n:**
   - Forum: https://community.n8n.io
   - Discord: https://discord.gg/n8n

---

## ✅ Checklist de Diagnóstico

Ao encontrar um problema, siga esta ordem:

- [ ] Verifique logs do n8n (Executions)
- [ ] Teste credenciais (Google, Z-API)
- [ ] Confirme URLs de arquivos estão acessíveis
- [ ] Valide formato dos dados (telefone, data, etc)
- [ ] Verifique se workflows estão ativos
- [ ] Teste cada nó isoladamente
- [ ] Confirme permissões (Sheets, Calendar)
- [ ] Verifique recursos do servidor (CPU, RAM)
- [ ] Teste manualmente via API/curl
- [ ] Consulte documentação oficial

---

**Última atualização:** Outubro 2025

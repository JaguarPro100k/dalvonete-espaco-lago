# 🚀 Guia de Instalação Completo

## Pré-requisitos

Antes de começar, certifique-se de ter:

- [ ] Acesso ao Easypanel
- [ ] Acesso ao n8n
- [ ] Conta Google (com Calendar e Sheets configurados)
- [ ] Conta Z-API ativa
- [ ] Domínio próprio (para hospedar arquivos de mídia)
- [ ] Cliente FTP/SFTP (FileZilla, WinSCP, etc)

## Passo 1: Configuração do Ambiente

### 1.1. Acesso ao Easypanel

1. Acesse: `hjmnzb.easypanel.host`
2. Faça login com as credenciais:
   - Email: `arbnbdalvanete@gmail.com`
   - Senha: [Sua senha]

3. **IMPORTANTE:** Altere a senha padrão:
   - Vá em Settings → Security
   - Clique em "Change Password"
   - Use uma senha forte (mínimo 12 caracteres, letras, números e símbolos)

### 1.2. Acesso ao n8n

1. Acesse: `https://n8n-n8n-start.degwfm.easypanel.host/signin`
2. Faça login com:
   - Email: `arbnbdalvanete@gmail.com`
   - Senha: [Sua senha]

3. **IMPORTANTE:** Altere a senha padrão

## Passo 2: Configuração do Google

### 2.1. Google Calendar

1. Acesse [Google Calendar](https://calendar.google.com)
2. Crie uma agenda específica para reservas (ou use existente):
   - Nome: "CHALÉ" ou "Reservas Dalvonete"
3. Obtenha o ID da agenda:
   - Clique nos 3 pontos da agenda → "Configurações e compartilhamento"
   - Role até "Integrar agenda"
   - Copie o "ID da agenda" (formato: xxxxx@group.calendar.google.com)

### 2.2. Google Sheets

1. Acesse o arquivo: [trava IA - Atendimento](https://docs.google.com/spreadsheets/d/1tWZa2xifXSs-0-gK_1Jv87E1NJE3-ZfNcVjDokfYCSk)
2. Verifique se tem acesso de edição
3. Estrutura necessária (PÃ¡gina1):
   ```
   | telefone      | travar IA? | Ultimo envio |
   |---------------|------------|--------------|
   | 5537999999999 | Sim        | 2025-10-29   |
   ```

### 2.3. Criar Credenciais OAuth2

#### Para Google Calendar:

1. Acesse [Google Cloud Console](https://console.cloud.google.com)
2. Crie um novo projeto ou use existente
3. Ative a API do Google Calendar:
   - Vá em "APIs & Services" → "Library"
   - Busque "Google Calendar API"
   - Clique em "Enable"

4. Crie credenciais OAuth2:
   - Vá em "APIs & Services" → "Credentials"
   - Clique em "Create Credentials" → "OAuth client ID"
   - Tipo: "Web application"
   - Nome: "n8n Calendar Integration"
   - Authorized redirect URIs: `https://n8n-n8n-start.degwfm.easypanel.host/rest/oauth2-credential/callback`

5. Copie Client ID e Client Secret

#### Para Google Sheets:

1. Repita o processo acima, mas ative "Google Sheets API"
2. Use as mesmas credenciais OAuth2 ou crie novas

## Passo 3: Configuração do n8n

### 3.1. Adicionar Credenciais do Google

1. No n8n, vá em: Settings (canto superior direito) → Credentials
2. Clique em "Add Credential"

**Google Calendar:**
- Tipo: "Google Calendar OAuth2 API"
- Nome: "Google Calendar dalvonete"
- Client ID: [Cole o Client ID]
- Client Secret: [Cole o Client Secret]
- Clique em "Connect my account"
- Autorize o acesso

**Google Sheets:**
- Tipo: "Google Sheets OAuth2 API"
- Nome: "Google Sheets dalvonete"
- Client ID: [Cole o Client ID]
- Client Secret: [Cole o Client Secret]
- Clique em "Connect my account"
- Autorize o acesso

### 3.2. Importar Workflows

1. Baixe os arquivos:
   - `MCP_DALVONETE.json`
   - `envia_video.json`

2. No n8n:
   - Vá em Workflows
   - Clique em "Add Workflow" → "Import from File"
   - Selecione `MCP_DALVONETE.json`
   - Repita para `envia_video.json`

### 3.3. Configurar Workflows

#### Workflow: MCP DALVONETE

1. Abra o workflow
2. Para cada nó, verifique e configure:

**Nó: busca_evento_calendario**
- Operation: Get All
- Calendar: Selecione "CHALÉ" (sua agenda)
- Return All: True
- Credential: "Google Calendar dalvonete"

**Nó: cadastra_lead_CRM**
- Workflow: Selecione o workflow de CRM (ID: 5d3jpJxl5JjFhGNg)
- Verifique os campos de entrada

**Nó: Trava_atendimento**
- Document: Selecione "trava IA - Atendimento"
- Sheet: "PÃ¡gina1"
- Credential: "Google Sheets dalvonete"

**Nó: envia_video**
- Workflow: Selecione "envia_video"

#### Workflow: envia_video

1. Abra o workflow
2. Configure os nós HTTP Request:

**HTTP Request (vídeo):**
```
URL: https://jaguarpro.com.br/dalvonete/video.mp4
⚠️ ATUALIZAR APÓS MIGRAÇÃO PARA:
https://SEUDOMINIO.com.br/dalvonete/video.mp4
```

**HTTP Request1 (imagem):**
```
URL: https://jaguarpro.com.br/dalvonete/imagem
⚠️ ATUALIZAR APÓS MIGRAÇÃO PARA:
https://SEUDOMINIO.com.br/dalvonete/imagem.jpg
```

**HTTP Request6 e HTTP Request7:**
- Mantenha as configurações da Z-API
- Verifique se as credenciais estão corretas

## Passo 4: Migração de Arquivos de Mídia

### 4.1. Preparar Arquivos

1. Os arquivos que você precisa hospedar:
   - `video.mp4` (vídeo promocional do chalé)
   - `imagem` (foto do chalé - renomeie para `imagem.jpg`)

2. Verifique o tamanho:
   - Vídeo: Máximo 16MB (limite Z-API)
   - Imagem: Máximo 5MB (recomendado)

3. Se necessário, comprima:
   - **Vídeo:** Use [HandBrake](https://handbrake.fr/) ou [FFmpeg]
   - **Imagem:** Use [TinyPNG](https://tinypng.com/) ou [JPEG Optimizer]

### 4.2. Opção A: Hostinger (Recomendado)

1. **Faça login na Hostinger:**
   - Acesse: https://www.hostinger.com.br
   - Entre com suas credenciais

2. **Acesse seu domínio:**
   - Vá em "Websites"
   - Clique no seu domínio

3. **Abra o Gerenciador de Arquivos:**
   - No painel, clique em "Gerenciador de Arquivos"
   - Navegue até `public_html`

4. **Crie a estrutura de pastas:**
   ```
   public_html/
   └── dalvonete/
       ├── video.mp4
       └── imagem.jpg
   ```

5. **Upload dos arquivos:**
   - Clique em "Upload" (canto superior direito)
   - Arraste os arquivos ou clique para selecionar
   - Aguarde 100% do upload

6. **Configure permissões:**
   - Clique com botão direito em cada arquivo
   - Selecione "Permissões" ou "Permissions"
   - Configure para: `644` (rw-r--r--)
   - Isso permite leitura pública

7. **Teste as URLs:**
   ```bash
   # Abra no navegador:
   https://SEUDOMINIO.com.br/dalvonete/video.mp4
   https://SEUDOMINIO.com.br/dalvonete/imagem.jpg
   
   # Ou teste via curl:
   curl -I https://SEUDOMINIO.com.br/dalvonete/video.mp4
   # Deve retornar: HTTP/1.1 200 OK
   ```

### 4.3. Opção B: Easypanel (Self-hosted)

1. **Criar serviço Nginx:**
   - No Easypanel, clique em "Create Service"
   - Escolha "From Template" → "Nginx"
   - Nome: "media-server-dalvonete"

2. **Configurar volumes:**
   - Adicione um volume: `/media` → `/usr/share/nginx/html`

3. **Configurar domínio:**
   - Em "Domains", adicione: `media.SEUDOMINIO.com.br`
   - Ative SSL (Let's Encrypt)

4. **Upload dos arquivos:**
   
   **Via Terminal do container:**
   ```bash
   # No Easypanel, abra o terminal do container
   cd /usr/share/nginx/html
   mkdir dalvonete
   cd dalvonete
   # Use wget ou copie os arquivos
   ```

   **Via SFTP:**
   - Use FileZilla ou WinSCP
   - Conecte ao servidor do Easypanel
   - Navegue até o volume
   - Copie os arquivos

5. **Teste:**
   ```
   https://media.SEUDOMINIO.com.br/dalvonete/video.mp4
   https://media.SEUDOMINIO.com.br/dalvonete/imagem.jpg
   ```

### 4.4. Atualizar URLs no n8n

1. **Abra o workflow `envia_video`**

2. **Nó HTTP Request:**
   ```
   ANTES: https://jaguarpro.com.br/dalvonete/video.mp4
   DEPOIS: https://SEUDOMINIO.com.br/dalvonete/video.mp4
   ```

3. **Nó HTTP Request1:**
   ```
   ANTES: https://jaguarpro.com.br/dalvonete/imagem
   DEPOIS: https://SEUDOMINIO.com.br/dalvonete/imagem.jpg
   ```

4. **Salve o workflow**

5. **Teste o envio:**
   - Execute manualmente o workflow
   - Verifique se o vídeo e imagem são enviados corretamente

## Passo 5: Teste Completo do Sistema

### 5.1. Teste Busca de Eventos

1. No workflow MCP, clique em "Execute Workflow"
2. Use a tool `busca_evento_calendario`:
   ```json
   {
     "timeMin": "2025-11-01T00:00:00Z",
     "timeMax": "2025-11-30T23:59:59Z"
   }
   ```
3. Verifique se retorna eventos do Calendar

### 5.2. Teste Cadastro de Lead

1. Execute a tool `cadastra_lead_CRM`:
   ```json
   {
     "nome": "Teste Sistema",
     "telefone": "5537999999999",
     "ocasiao": "chalé",
     "data_do_evento": "2025-12-15",
     "cidade": "Divinópolis"
   }
   ```
2. Verifique se o lead aparece no CRM

### 5.3. Teste Envio de Vídeo

1. Execute a tool `envia_video`:
   ```json
   {
     "telefone": "5537999999999"
   }
   ```
2. Verifique se recebe o vídeo e imagem no WhatsApp

### 5.4. Teste Trava de Atendimento

1. Execute a tool `Trava_atendimento`:
   ```json
   {
     "telefone": "5537999999999"
   }
   ```
2. Verifique se aparece na planilha do Google Sheets

## Passo 6: Ativar Produção

### 6.1. Ativar Workflows

1. Abra cada workflow
2. Clique no toggle "Active" (canto superior direito)
3. Verifique se aparece "Active" em verde

### 6.2. Configurar Monitoramento

1. Configure notificações de erro:
   - Em cada workflow, adicione nó "Error Trigger"
   - Configure para enviar email/notificação em caso de erro

### 6.3. Backup

1. Exporte os workflows:
   - Abra cada workflow
   - Clique nos 3 pontos → "Download"
   - Salve em local seguro

2. Backup do Google Sheets:
   - Arquivo → Fazer download → Excel (.xlsx)

## ✅ Checklist Final

Antes de considerar a instalação completa, verifique:

- [ ] n8n acessível e senha alterada
- [ ] Easypanel acessível e senha alterada
- [ ] Credenciais Google configuradas
- [ ] Workflows importados
- [ ] Arquivos de mídia migrados para domínio próprio
- [ ] URLs atualizadas no workflow envia_video
- [ ] Testes realizados com sucesso
- [ ] Workflows ativados
- [ ] Sistema de backup configurado
- [ ] Documentação revisada

## 🆘 Suporte

Se encontrar problemas:

1. Verifique os logs no n8n (Executions)
2. Consulte o arquivo `TROUBLESHOOTING.md`
3. Entre em contato: arbnbdalvanete@gmail.com

---

**Última atualização:** Outubro 2025

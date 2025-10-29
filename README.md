# 🏡 Agente IA Dalvonete - Sistema de Atendimento Automatizado

## 📋 Sobre o Projeto

Sistema de atendimento automatizado via WhatsApp para gestão de reservas e atendimento de chalé/eventos. O agente utiliza o protocolo MCP (Model Context Protocol) integrado ao n8n para automatizar processos de:

- 📅 Consulta de disponibilidade no Google Calendar
- 📝 Cadastro de leads no CRM
- 🎥 Envio de vídeos e imagens promocionais
- 🔒 Controle de atendimento via Google Sheets

## 🏗️ Arquitetura

```
┌─────────────────┐
│   WhatsApp      │
│   (Z-API)       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  MCP Server     │
│  (n8n)          │
├─────────────────┤
│ Tools:          │
│ • Calendario    │
│ • CRM           │
│ • Envio Video   │
│ • Trava IA      │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────┐
│   Integrações               │
├─────────────────────────────┤
│ • Google Calendar           │
│ • Google Sheets             │
│ • Workflow CRM              │
│ • Servidor de Mídia         │
└─────────────────────────────┘
```

## 🚀 Instalação e Configuração

### Pré-requisitos

- n8n instalado e configurado
- Conta Google (Calendar + Sheets)
- Conta Z-API para WhatsApp
- Servidor web para hospedar arquivos de mídia

### 1. Configuração do n8n

#### Acesso ao n8n
```
URL: https://n8n-n8n-start.degwfm.easypanel.host/signin
Email: arbnbdalvanete@gmail.com
Senha: [Configurar senha segura]
```

#### Importar Workflows

1. Acesse o n8n
2. Clique em "Import from File"
3. Importe os arquivos:
   - `MCP_DALVONETE.json` (Workflow principal)
   - `envia_video.json` (Workflow de envio de mídia)

### 2. Configuração das Credenciais

#### Google Calendar
- Nome: `Google Calendar dalvonete`
- ID da Agenda: `6c56deec10e7d9a65bb9c73a393a480934803a1f531035f438fdae05b0d7b68f@group.calendar.google.com`

#### Google Sheets
- Nome: `Google Sheets dalvonete`
- Planilha: `trava IA - Atendimento`
- ID: `1tWZa2xifXSs-0-gK_1Jv87E1NJE3-ZfNcVjDokfYCSk`

#### Z-API (WhatsApp)
```
Instance ID: 3E5F68ACE46F30605A0B2E6C7627E5AC
Token: 9F8363362C4A59C1CC7D5326
Client-Token: F6ebb1eaa3864462d97a9207743613045S
```

### 3. Ativar Workflows

1. Abra cada workflow importado
2. Verifique todas as credenciais
3. Clique em "Active" para ativar

## 📁 Hospedagem de Arquivos de Mídia

### ⚠️ Migração Importante

**Status Atual:** Os arquivos estão hospedados no domínio da Jaguar Pro:
- Vídeo: `https://jaguarpro.com.br/dalvonete/video.mp4`
- Imagem: `https://jaguarpro.com.br/dalvonete/imagem`

**Ação Necessária:** Migrar para domínio próprio da Dalvonete (projeto self-hosted)

### Tutorial: Como Hospedar Vídeo e Imagem

#### Opção 1: Usando Hostinger (Recomendado)

1. **Acesse o painel da Hostinger**
   - Faça login em https://www.hostinger.com.br
   - Vá para "Websites" → Selecione seu domínio

2. **Acesse o Gerenciador de Arquivos**
   - No painel do site, clique em "Gerenciador de Arquivos"
   - Navegue até `public_html`

3. **Crie uma pasta para os arquivos**
   ```
   public_html/
   └── dalvonete/
       ├── video.mp4
       └── imagem.jpg (ou .png)
   ```

4. **Faça upload dos arquivos**
   - Clique em "Upload" no gerenciador
   - Selecione os arquivos `video.mp4` e `imagem`
   - Aguarde o upload completar

5. **Teste o acesso**
   ```
   https://seudominio.com.br/dalvonete/video.mp4
   https://seudominio.com.br/dalvonete/imagem.jpg
   ```

6. **Configure permissões**
   - Clique com botão direito nos arquivos
   - Permissões → 644 (rw-r--r--)

#### Opção 2: Usando Easypanel (Self-hosted)

**Acesso ao Easypanel:**
```
URL: hjmnzb.easypanel.host
Email: arbnbdalvanete@gmail.com
Senha: [Configurar senha segura]
```

1. **Criar serviço de arquivos estáticos**
   - No Easypanel, crie um novo serviço
   - Use uma imagem nginx ou Apache
   - Configure o volume para `/usr/share/nginx/html`

2. **Upload dos arquivos**
   - Acesse o terminal do container
   - Faça upload via SCP/SFTP ou interface web
   - Organize na estrutura:
   ```
   /usr/share/nginx/html/
   └── dalvonete/
       ├── video.mp4
       └── imagem.jpg
   ```

3. **Configure o domínio**
   - Em "Domains", adicione seu domínio
   - Configure SSL/TLS (Let's Encrypt)

4. **Teste o acesso**
   ```
   https://seudominio.com.br/dalvonete/video.mp4
   https://seudominio.com.br/dalvonete/imagem.jpg
   ```

#### Opção 3: Usando CDN (Cloudflare R2, AWS S3, etc)

1. **Crie um bucket público**
2. **Configure CORS para permitir acesso do n8n**
3. **Upload dos arquivos**
4. **Obtenha as URLs públicas**

### 🔄 Atualizar URLs no n8n

Após hospedar os arquivos no novo domínio:

1. **Acesse o workflow `envia_video`**
2. **Localize os nós HTTP Request:**
   - **HTTP Request** (vídeo):
     ```
     URL atual: https://jaguarpro.com.br/dalvonete/video.mp4
     URL nova: https://SEUDOMINIO.com.br/dalvonete/video.mp4
     ```
   
   - **HTTP Request1** (imagem):
     ```
     URL atual: https://jaguarpro.com.br/dalvonete/imagem
     URL nova: https://SEUDOMINIO.com.br/dalvonete/imagem.jpg
     ```

3. **Salve e teste o workflow**

## 🛠️ Ferramentas do MCP

### 1. busca_evento_calendario
**Descrição:** Busca eventos disponíveis no Google Calendar

**Parâmetros:**
- `timeMin`: Data inicial (formato ISO)
- `timeMax`: Data final (formato ISO)

**Uso:** Verificar disponibilidade de datas para reservas

### 2. cadastra_lead_CRM
**Descrição:** Registra novos leads no sistema CRM

**Parâmetros:**
- `nome`: Nome do cliente
- `telefone`: Telefone (formato: 553799351255)
- `ocasiao`: Tipo do evento (chalé ou evento)
- `data_do_evento`: Data no formato YYYY-MM-DD
- `cidade`: Cidade do cliente

**Workflow:** ID `5d3jpJxl5JjFhGNg`

### 3. envia_video
**Descrição:** Envia vídeo e imagem promocional via WhatsApp

**Parâmetros:**
- `telefone_lead`: Telefone do destinatário

**Processo:**
1. Baixa vídeo do servidor
2. Converte para base64
3. Envia via Z-API
4. Baixa imagem do servidor
5. Converte para base64
6. Envia via Z-API

### 4. Trava_atendimento
**Descrição:** Registra bloqueio de atendimento automatizado

**Parâmetros:**
- `telefone`: Telefone do cliente

**Ação:** Adiciona/atualiza registro na planilha com status "Sim"

## 📊 Estrutura de Dados

### Google Sheets - Trava IA
```
┌──────────┬──────────────┬──────────────┐
│ telefone │ travar IA?   │ Ultimo envio │
├──────────┼──────────────┼──────────────┤
│ 5537...  │ Sim          │ 2025-10-29   │
└──────────┴──────────────┴──────────────┘
```

### CRM - Campos de Lead
```json
{
  "nome": "string",
  "telefone": "string (553799351255)",
  "ocasiao": "chalé|evento",
  "data_do_evento": "YYYY-MM-DD",
  "cidade": "string"
}
```

## 🔐 Segurança

### Credenciais Sensíveis

**⚠️ IMPORTANTE:** As credenciais fornecidas nesta documentação são de exemplo. 

**Antes de colocar em produção:**

1. ✅ Altere todas as senhas padrão
2. ✅ Use senhas fortes e únicas
3. ✅ Ative autenticação de dois fatores quando disponível
4. ✅ Revogue tokens antigos da Z-API
5. ✅ Configure IP whitelist no Easypanel
6. ✅ Use variáveis de ambiente para credenciais

### Recomendações

- Nunca commite arquivos com credenciais reais
- Use `.env` para variáveis sensíveis
- Rotate tokens periodicamente
- Mantenha logs de acesso
- Configure backup automático

## 🧪 Testes

### Testar Busca de Eventos
```json
{
  "timeMin": "2025-11-01T00:00:00Z",
  "timeMax": "2025-11-30T23:59:59Z"
}
```

### Testar Cadastro de Lead
```json
{
  "nome": "João Silva",
  "telefone": "5537999887766",
  "ocasiao": "chalé",
  "data_do_evento": "2025-12-15",
  "cidade": "Divinópolis"
}
```

### Testar Envio de Vídeo
```json
{
  "telefone": "5537999887766"
}
```

## 🐛 Troubleshooting

### Erro: Vídeo não é enviado
- ✅ Verifique se a URL do vídeo está acessível
- ✅ Confirme que o arquivo não excede 16MB (limite Z-API)
- ✅ Teste o download manual da URL
- ✅ Verifique logs do workflow no n8n

### Erro: Lead não cadastrado
- ✅ Verifique formato do telefone (DDI+DDD+Número)
- ✅ Confirme formato da data (YYYY-MM-DD)
- ✅ Teste workflow de CRM separadamente

### Erro: Calendar não retorna eventos
- ✅ Verifique credenciais do Google Calendar
- ✅ Confirme ID da agenda
- ✅ Verifique permissões de compartilhamento

### Erro: 401 Unauthorized na Z-API
- ✅ Verifique se o token não expirou
- ✅ Confirme Client-Token está correto
- ✅ Teste credenciais via Postman/Insomnia

## 📈 Melhorias Futuras

- [ ] Implementar cache de vídeos
- [ ] Adicionar analytics de conversão
- [ ] Criar dashboard de métricas
- [ ] Implementar notificações de erro
- [ ] Adicionar mais tipos de mídia
- [ ] Integrar com mais plataformas de CRM
- [ ] Criar interface web de gerenciamento

## 📝 Changelog

### v1.0.0 (Atual)
- ✅ Sistema de MCP implementado
- ✅ Integração com Google Calendar
- ✅ Cadastro de leads em CRM
- ✅ Envio de vídeo e imagem
- ✅ Sistema de trava de atendimento

### Próximas versões
- [ ] Migração para domínio próprio
- [ ] Implementação de CDN
- [ ] Sistema de monitoramento

## 🤝 Contribuindo

Para contribuir com este projeto:

1. Fork o repositório
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

## 📄 Licença

Este projeto é proprietário da Dalvonete.

## 📞 Suporte

Para suporte técnico:
- Email: arbnbdalvanete@gmail.com
- Documentação: [Link para wiki]

---

**Desenvolvido com ❤️ para Dalvonete**

# ⚡ Guia de Início Rápido

Quer começar rapidamente? Este guia te leva do zero ao funcionamento em 30 minutos!

## 🎯 Pré-requisitos (5 minutos)

Antes de começar, tenha em mãos:

- ✅ Credenciais de acesso ao Easypanel
- ✅ Credenciais de acesso ao n8n
- ✅ Conta Google (Gmail)
- ✅ Conta Z-API ativa
- ✅ Arquivos de mídia (video.mp4 e imagem.jpg)

## 🚀 Instalação Rápida (15 minutos)

### Passo 1: Configurar n8n (5 min)

1. **Acesse o n8n:**
   ```
   https://n8n-n8n-start.degwfm.easypanel.host/signin
   ```

2. **Faça login e MUDE A SENHA imediatamente**

3. **Configure credenciais Google:**
   - Settings → Credentials → Add Credential
   - Tipo: "Google Calendar OAuth2 API"
   - Nome: "Google Calendar dalvonete"
   - Siga o wizard de autorização
   - Repita para "Google Sheets OAuth2 API"

### Passo 2: Importar Workflows (5 min)

1. **Download dos workflows:**
   - [MCP_DALVONETE.json](link)
   - [envia_video.json](link)

2. **Importar no n8n:**
   - Workflows → "+" → Import from File
   - Selecione `MCP_DALVONETE.json`
   - Repita para `envia_video.json`

3. **Verificar credenciais:**
   - Abra cada workflow
   - Clique em cada nó com ⚠️ vermelho
   - Selecione a credencial correta

### Passo 3: Hospedar Mídia (5 min)

**Opção Rápida - Hostinger:**

1. Login na Hostinger
2. Gerenciador de Arquivos
3. Navegue até `public_html`
4. Crie pasta `dalvonete`
5. Upload de `video.mp4` e `imagem.jpg`
6. Teste: `https://seudominio.com.br/dalvonete/video.mp4`

**Atualize URLs no workflow `envia_video`:**
- Nó "HTTP Request": Cole sua URL do vídeo
- Nó "HTTP Request1": Cole sua URL da imagem

## ✅ Teste (5 minutos)

### Teste 1: Buscar Eventos

1. Abra workflow "MCP DALVONETE"
2. Execute manualmente
3. Tool: `busca_evento_calendario`
4. Parâmetros:
   ```json
   {
     "timeMin": "2025-11-01T00:00:00Z",
     "timeMax": "2025-11-30T23:59:59Z"
   }
   ```
5. ✅ Deve retornar eventos (ou array vazio)

### Teste 2: Enviar Vídeo

1. Abra workflow "envia_video"
2. Execute manualmente com:
   ```json
   {
     "telefone": "5537999999999"
   }
   ```
3. ✅ Deve receber vídeo e imagem no WhatsApp

### Teste 3: Cadastrar Lead

1. No workflow "MCP DALVONETE"
2. Tool: `cadastra_lead_CRM`
3. Parâmetros:
   ```json
   {
     "nome": "Teste Sistema",
     "telefone": "5537999999999",
     "ocasiao": "chalé",
     "data_do_evento": "2025-12-15",
     "cidade": "Divinópolis"
   }
   ```
4. ✅ Verificar se aparece no CRM

## 🎉 Ativar Sistema (2 minutos)

1. Em cada workflow, clique no toggle "Active"
2. Confirme que está verde
3. Sistema está pronto! 🚀

## 🆘 Problemas Comuns

### ❌ Erro: "Unauthorized" na Z-API

**Solução:**
```
1. Verifique token no painel Z-API
2. Atualize no workflow "envia_video"
3. Nodes: HTTP Request6 e HTTP Request7
4. Header: Client-Token
```

### ❌ Erro: Vídeo não baixa

**Solução:**
```
1. Teste URL no navegador
2. Deve baixar o arquivo
3. Se não, verifique:
   - Arquivo existe no servidor?
   - Permissões corretas (644)?
   - HTTPS habilitado?
```

### ❌ Erro: Calendar não retorna eventos

**Solução:**
```
1. Reconecte credencial Google Calendar
2. Settings → Credentials → Reconectar
3. Autorize novamente
4. Verifique ID da agenda está correto
```

## 📚 Próximos Passos

Agora que está funcionando:

1. **Leia a documentação completa:**
   - [README.md](README.md) - Visão geral
   - [INSTALLATION.md](INSTALLATION.md) - Instalação detalhada
   - [SECURITY.md](SECURITY.md) - Práticas de segurança

2. **Configure segurança:**
   - Altere TODAS as senhas
   - Configure backup
   - Leia práticas LGPD

3. **Personalize:**
   - Ajuste mensagens do bot
   - Configure alertas
   - Adicione mais tools

## 🔗 Links Úteis

- [Documentação n8n](https://docs.n8n.io)
- [Z-API Docs](https://developer.z-api.io)
- [Google Calendar API](https://developers.google.com/calendar)
- [Troubleshooting](TROUBLESHOOTING.md)

## 💡 Dicas

### Dica 1: Teste antes de ativar
Sempre teste workflows manualmente antes de ativá-los em produção.

### Dica 2: Monitore logs
Nos primeiros dias, monitore logs diariamente para identificar problemas.

### Dica 3: Backup
Faça backup dos workflows imediatamente após configuração.

### Dica 4: Documentação personalizada
Documente quaisquer customizações que você fizer.

## 📊 Checklist Final

Antes de considerar pronto:

- [ ] n8n acessível e senha alterada
- [ ] Workflows importados e ativos
- [ ] Credenciais Google configuradas
- [ ] Mídia hospedada e URLs atualizadas
- [ ] Todos os testes passando
- [ ] Backup realizado
- [ ] Documentação revisada
- [ ] Equipe treinada

## 🎓 Tutorial em Vídeo

[Link para vídeo tutorial](link) - 15 minutos

## 📞 Suporte

Precisa de ajuda?

- 📧 Email: arbnbdalvanete@gmail.com
- 📖 Docs: [Documentação completa](link)
- 🐛 Issues: [GitHub Issues](link)

---

**Tempo total estimado: 30 minutos** ⏱️

**Dificuldade: ⭐⭐☆☆☆ (Fácil)**

Bom trabalho! 🎉

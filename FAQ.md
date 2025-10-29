# ❓ Perguntas Frequentes (FAQ)

## 📋 Índice

- [Geral](#geral)
- [Instalação](#instalação)
- [Configuração](#configuração)
- [Uso](#uso)
- [Troubleshooting](#troubleshooting)
- [Segurança](#segurança)
- [Custos](#custos)
- [Integração](#integração)

---

## 🌐 Geral

### O que é o Agente IA Dalvonete?

É um sistema de atendimento automatizado via WhatsApp para gestão de reservas de chalé e eventos. Ele usa n8n com MCP (Model Context Protocol) para integrar múltiplos serviços.

### Quais são os principais recursos?

- 📅 Consulta de disponibilidade em tempo real
- 📝 Cadastro automático de leads
- 🎥 Envio de vídeos e fotos promocionais
- 🔒 Sistema de trava para atendimento humano
- 📊 Integração com Google Calendar e Sheets

### Preciso saber programar para usar?

Não! A instalação básica não requer programação. Porém, conhecimento em n8n ajuda em customizações avançadas.

### O sistema funciona 24/7?

Sim, uma vez ativado, funciona ininterruptamente. Recomendamos monitoramento regular.

### Quantas conversas simultâneas suporta?

Depende dos recursos do servidor e limites da Z-API. Configuração padrão suporta ~10 conversas simultâneas.

---

## 🔧 Instalação

### Quanto tempo leva a instalação?

Instalação básica: 30 minutos  
Instalação completa com testes: 2-3 horas

### Preciso contratar servidores?

Não necessariamente. O Easypanel já fornece infraestrutura. Você só precisa de:
- Hospedagem web para arquivos de mídia (pode usar Hostinger)
- Conta Z-API

### Posso usar em servidor compartilhado?

Sim, mas é recomendado servidor dedicado ou VPS para melhor performance e segurança.

### Funciona no Windows?

O n8n funciona em qualquer sistema. No caso, está sendo usado via Easypanel (Linux).

### Preciso instalar algo no meu computador?

Não. Tudo roda na nuvem. Você só precisa de um navegador web.

---

## ⚙️ Configuração

### Como obtenho credenciais do Google?

1. Acesse [Google Cloud Console](https://console.cloud.google.com)
2. Crie projeto
3. Ative APIs (Calendar e Sheets)
4. Crie credenciais OAuth2
5. Configure redirect URI do n8n

Veja tutorial completo em [INSTALLATION.md](INSTALLATION.md#32-criar-credenciais-oauth2)

### Preciso de uma conta Z-API específica?

Recomendamos conta Business ou Superior para estabilidade. Conta gratuita tem limitações.

### Posso usar múltiplas agendas do Google?

Sim, mas cada agenda precisa estar configurada no workflow. Configure uma de cada vez.

### Como altero as mensagens enviadas?

As mensagens são definidas nos workflows do n8n. Edite os nós de texto/resposta.

### Posso adicionar mais ferramentas (tools)?

Sim! n8n permite adicionar quantas tools quiser. Veja documentação do n8n sobre custom tools.

---

## 🎯 Uso

### Como o bot decide quando enviar vídeo?

Baseado nas instruções do MCP. O bot analisa a conversa e usa a tool `envia_video` quando apropriado.

### Posso personalizar quando o vídeo é enviado?

Sim. Edite a lógica no MCP Server ou adicione condições no workflow `envia_video`.

### O que é "trava de atendimento"?

É um sistema que marca números de telefone que preferem atendimento humano. Mensagens desses números não são processadas pela IA.

### Como destravar um número?

Na planilha Google Sheets "trava IA - Atendimento", mude "Sim" para "Não" ou delete a linha.

### Posso usar em múltiplos WhatsApp?

Sim, mas cada instância Z-API suporta um número. Para múltiplos números, precisa de múltiplas instâncias.

### Como adiciono mais datas no calendário?

Diretamente no Google Calendar. O sistema busca automaticamente os eventos.

### O bot responde em outros idiomas?

Depende da configuração do MCP e dos prompts. É possível configurar multi-idioma.

---

## 🐛 Troubleshooting

### O vídeo não está sendo enviado. O que fazer?

**Checklist:**
1. ✅ URL do vídeo acessível? Teste no navegador
2. ✅ Arquivo menor que 16MB?
3. ✅ Token Z-API válido?
4. ✅ Formato de telefone correto (553799999999)?
5. ✅ Workflow ativado?

Veja mais em [TROUBLESHOOTING.md](TROUBLESHOOTING.md#1-vídeo-não-é-enviado)

### Recebo erro "401 Unauthorized" da Z-API

Seu token expirou ou está incorreto. Solução:
1. Verifique token no painel Z-API
2. Atualize no workflow
3. Teste novamente

### Google Calendar não retorna eventos

**Possíveis causas:**
- Credenciais OAuth2 expiraram → Reconecte
- ID da agenda incorreto → Verifique no Google Calendar
- API não ativada → Ative no Google Cloud Console

### Como vejo os logs de erro?

No n8n:
1. Vá em "Executions"
2. Filtre por "Error"
3. Clique na execução para ver detalhes

### O workflow está lento

**Otimizações:**
1. Verifique recursos do servidor (CPU/RAM)
2. Reduza timeout dos nós
3. Implemente cache
4. Use CDN para mídia

---

## 🔐 Segurança

### As conversas são criptografadas?

Sim, toda comunicação usa HTTPS/TLS. Entre WhatsApp e Z-API, usa criptografia end-to-end.

### Onde ficam armazenadas as credenciais?

No n8n, de forma criptografada. NUNCA coloque credenciais em código ou commits.

### Como faço backup?

1. Export dos workflows (JSON)
2. Backup do Google Sheets
3. Backup dos arquivos de mídia

Frequência recomendada: Diário para workflows, semanal para mídia.

### O sistema é LGPD compliant?

Sim, se configurado corretamente:
- Obtenha consentimento dos clientes
- Tenha política de privacidade
- Permita exclusão de dados
- Documente tratamento de dados

Veja [SECURITY.md](SECURITY.md#81-lgpd)

### Como proteger contra ataques?

- Use senhas fortes e únicas
- Ative 2FA quando disponível
- Configure IP whitelist
- Monitore logs regularmente
- Mantenha sistema atualizado

### Devo rotacionar as senhas?

Sim. Recomendamos:
- Senhas administrativas: a cada 90 dias
- Tokens de API: a cada 180 dias
- Credenciais OAuth: verificar anualmente

---

## 💰 Custos

### Quanto custa para rodar o sistema?

**Custos típicos:**
- Easypanel: Gratuito ou conforme plano
- n8n: Gratuito (self-hosted)
- Google Calendar/Sheets: Gratuito
- Z-API: R$ 50-200/mês (depende do plano)
- Hospedagem de mídia: R$ 20-50/mês (Hostinger básico)

**Total estimado: R$ 70-250/mês**

### Posso usar tudo gratuito?

Parcialmente:
- ✅ n8n: Sim (self-hosted)
- ✅ Google Calendar: Sim
- ✅ Google Sheets: Sim
- ❌ Z-API: Trial limitado, depois pago
- ⚠️ Hospedagem mídia: Depende

### Z-API tem plano gratuito?

Trial limitado. Para uso profissional, recomenda-se plano pago.

### Quanto custa escalar?

Custos principais ao escalar:
- Servidor mais potente (Easypanel)
- Z-API com mais mensagens
- Possível CDN para mídia
- Monitoramento profissional

**Escala média: R$ 300-800/mês**

---

## 🔗 Integração

### Posso integrar com outros CRMs?

Sim! n8n tem integrações nativas com:
- HubSpot
- Salesforce
- Pipedrive
- RD Station
- E dezenas de outros

### Como integro com meu site?

Adicione um botão "Fale no WhatsApp" que redireciona para seu número Z-API.

### Posso integrar com Telegram/Facebook?

Sim, mas requer configuração adicional. n8n suporta múltiplas plataformas.

### Como conecto com meu sistema de pagamento?

Através do n8n, você pode integrar:
- Stripe
- PayPal
- Mercado Pago
- PagSeguro
- Outros

### Posso usar API própria?

Sim! Use o nó "HTTP Request" do n8n para chamar qualquer API REST.

### Como integro com planilhas Excel?

n8n tem nós nativos para:
- Excel Online (Microsoft 365)
- Google Sheets
- Upload/download de arquivos

---

## 📱 WhatsApp

### Funciona com WhatsApp Web?

Não. Usa Z-API que conecta diretamente à API oficial do WhatsApp Business.

### Preciso de WhatsApp Business?

Recomendado, mas não obrigatório. Z-API funciona com ambos.

### Quantas mensagens posso enviar por dia?

Depende do plano Z-API. Típico:
- Básico: ~1000/dia
- Profissional: ~5000/dia
- Enterprise: Ilimitado*

*Sujeito a fair use policy

### O bot responde a grupos?

Não por padrão. Mas pode ser configurado para monitorar grupos específicos.

### Como evito ser bloqueado pelo WhatsApp?

- Não envie spam
- Respeite rate limits
- Obtenha consentimento dos clientes
- Use templates aprovados para notificações

---

## 🚀 Performance

### Qual o tempo de resposta médio?

- Busca calendário: <2s
- Cadastro lead: <3s
- Envio vídeo: <10s
- Resposta texto: <1s

### Como otimizo o sistema?

1. Use CDN para mídia
2. Implemente cache
3. Otimize workflows (remova nós desnecessários)
4. Monitore e ajuste recursos do servidor

### Quantos leads posso processar por dia?

Limitações típicas:
- n8n: ~500-1000 leads/dia (com servidor básico)
- Z-API: Conforme plano
- Google APIs: ~10k requests/dia (gratuito)

### O que acontece se o servidor cair?

Mensagens são enfileiradas pela Z-API (por tempo limitado). Ao subir novamente, podem ser perdidas mensagens recentes.

**Recomendação:** Configure monitoramento com alertas.

---

## 🔄 Atualizações

### Como atualizo o n8n?

No Easypanel:
1. Vá no serviço n8n
2. Update image
3. Reinicie o container

**Importante:** Faça backup antes!

### Posso atualizar workflows em produção?

Sim, mas:
1. Teste em ambiente de staging
2. Faça backup antes
3. Evite horários de pico
4. Monitore após atualização

### Com que frequência devo atualizar?

- n8n: Mensalmente (ou quando há security fix)
- Workflows: Conforme necessidade
- Dependências: Trimestralmente

---

## 📚 Aprendizado

### Onde aprendo mais sobre n8n?

- [Documentação oficial](https://docs.n8n.io)
- [Curso n8n (YouTube)](https://youtube.com/n8n)
- [Comunidade n8n](https://community.n8n.io)
- [n8n Templates](https://n8n.io/workflows)

### Preciso entender de IA/ML?

Não para uso básico. O MCP já tem a lógica configurada. Para customizações avançadas, ajuda conhecer conceitos de IA.

### Há suporte técnico?

- Email: arbnbdalvanete@gmail.com
- Documentação: Este repositório
- Comunidade n8n: Para questões técnicas do n8n

---

## 💡 Dicas e Truques

### Dica 1: Use ambientes

Mantenha ambientes separados:
- Desenvolvimento: Para testes
- Staging: Para validação
- Produção: Para clientes reais

### Dica 2: Monitore métricas

Configure dashboard para acompanhar:
- Taxa de conversão
- Tempo de resposta
- Taxa de erro
- Satisfação do cliente

### Dica 3: Teste regularmente

Faça testes semanais:
- Envio de vídeo
- Busca de eventos
- Cadastro de lead

### Dica 4: Documente mudanças

Sempre documente customizações. Facilita manutenção futura.

### Dica 5: Comunidade

Participe da comunidade n8n. Muitas soluções para problemas comuns já existem.

---

## 🆘 Não Encontrou sua Pergunta?

- 📧 **Email:** arbnbdalvanete@gmail.com
- 📖 **Docs:** Leia [README.md](README.md) e [INSTALLATION.md](INSTALLATION.md)
- 🐛 **Bug:** Abra uma [issue no GitHub](link)
- 💬 **Chat:** Junte-se à [comunidade n8n](https://community.n8n.io)

---

**Última atualização:** Outubro 2025  
**Contribua:** Encontrou erro ou quer adicionar pergunta? Abra um PR!

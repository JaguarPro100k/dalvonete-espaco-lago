# Changelog

Todas as mudanças notáveis neste projeto serão documentadas neste arquivo.

O formato é baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.0.0/),
e este projeto adere ao [Versionamento Semântico](https://semver.org/lang/pt-BR/).

## [1.0.0] - 2025-10-29

### ✨ Adicionado
- Sistema MCP (Model Context Protocol) implementado no n8n
- Tool `busca_evento_calendario` para consultar disponibilidade no Google Calendar
- Tool `cadastra_lead_CRM` para registrar novos leads
- Tool `envia_video` para envio de vídeo e imagem promocional via WhatsApp
- Tool `Trava_atendimento` para marcar números que preferem atendimento humano
- Integração com Z-API para comunicação via WhatsApp
- Integração com Google Calendar para gestão de eventos
- Integração com Google Sheets para controle de atendimento
- Workflow automatizado de envio de mídia
- Sistema de validação de dados de entrada

### 📝 Documentação
- README.md completo com visão geral do projeto
- INSTALLATION.md com guia passo a passo de instalação
- TROUBLESHOOTING.md com soluções para problemas comuns
- ARCHITECTURE.md com diagramas e explicação da arquitetura
- SECURITY.md com práticas de segurança e LGPD
- config.example.env com template de configuração
- .gitignore para proteção de credenciais

### 🔐 Segurança
- Implementação de validação de telefone (formato: DDI+DDD+número)
- Validação de formato de data (YYYY-MM-DD)
- Sanitização de entrada de dados
- Uso de HTTPS em todas as comunicações
- Separação de credenciais em arquivo de ambiente

### 🐛 Conhecido
- Arquivos de mídia ainda hospedados no domínio Jaguar Pro (migração pendente)
- Necessário implementar rotação automática de tokens
- Alertas de segurança ainda não implementados

## [Futuro] - Próximas Versões

### 🚀 Planejado para v1.1.0
- [ ] Migração de arquivos de mídia para domínio próprio
- [ ] Implementação de CDN para otimização de entrega de mídia
- [ ] Sistema de cache para melhorar performance
- [ ] Dashboard de métricas e analytics
- [ ] Notificações automáticas de erro por email
- [ ] Backup automático de workflows

### 🚀 Planejado para v1.2.0
- [ ] Suporte a múltiplos idiomas
- [ ] Sistema de templates de mensagem personalizáveis
- [ ] Integração com mais plataformas de CRM
- [ ] API pública para integrações externas
- [ ] Modo de teste/sandbox
- [ ] Documentação interativa com exemplos

### 🚀 Planejado para v2.0.0
- [ ] Inteligência artificial aprimorada para respostas contextuais
- [ ] Sistema de agendamento automático
- [ ] Pagamento online integrado
- [ ] Portal do cliente
- [ ] App mobile
- [ ] Integração com calendários Apple/Outlook

---

## Tipos de Mudanças

- **✨ Adicionado** - para novas funcionalidades
- **🔄 Modificado** - para mudanças em funcionalidades existentes
- **❌ Descontinuado** - para funcionalidades que serão removidas
- **🗑️ Removido** - para funcionalidades removidas
- **🐛 Corrigido** - para correções de bugs
- **🔐 Segurança** - em caso de vulnerabilidades

---

**Data do documento:** 29 de Outubro de 2025

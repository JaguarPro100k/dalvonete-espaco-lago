# Guia de Instalação - AI SDR Dalvonete

## 🏗️ Instalação Completa Passo a Passo

### 1. Preparação do Ambiente

#### 1.1 Requisitos do Sistema
```bash
# Ubuntu 20.04+ ou CentOS 8+
# Mínimo: 4GB RAM, 2 vCPUs, 50GB storage
# Recomendado: 8GB RAM, 4 vCPUs, 100GB storage

# Atualizar sistema
sudo apt update && sudo apt upgrade -y

# Instalar dependências base
sudo apt install -y curl wget git unzip nginx certbot python3-certbot-nginx
```

#### 1.2 Instalação do Node.js
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
node --version  # Verificar versão 18+
```

#### 1.3 Instalação do Docker
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
sudo systemctl enable docker
sudo systemctl start docker
```

### 2. Configuração do Banco de Dados

#### 2.1 PostgreSQL
```bash
# Instalar PostgreSQL
sudo apt install -y postgresql postgresql-contrib

# Configurar usuário e banco
sudo -u postgres psql << EOF
CREATE USER n8n_user WITH PASSWORD 'senha_super_segura';
CREATE DATABASE dalvonete_ai OWNER n8n_user;
GRANT ALL PRIVILEGES ON DATABASE dalvonete_ai TO n8n_user;
\q
EOF

# Configurar acesso remoto (se necessário)
sudo nano /etc/postgresql/12/main/postgresql.conf
# Alterar: listen_addresses = '*'

sudo nano /etc/postgresql/12/main/pg_hba.conf
# Adicionar: host all all 0.0.0.0/0 md5

sudo systemctl restart postgresql
```

#### 2.2 Redis
```bash
# Instalar Redis
sudo apt install -y redis-server

# Configurar Redis
sudo nano /etc/redis/redis.conf
# Alterar: bind 127.0.0.1 ::1
# Alterar: requirepass sua_senha_redis

sudo systemctl restart redis-server
sudo systemctl enable redis-server
```

### 3. Instalação do n8n

#### 3.1 Via Docker (Recomendado)
```bash
# Criar diretório de trabalho
mkdir -p ~/ai-sdr-dalvonete
cd ~/ai-sdr-dalvonete

# Criar docker-compose.yml
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n_dalvonete
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=senha_admin_segura
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=host.docker.internal
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=dalvonete_ai
      - DB_POSTGRESDB_USER=n8n_user
      - DB_POSTGRESDB_PASSWORD=senha_super_segura
      - N8N_ENCRYPTION_KEY=sua_chave_de_criptografia_de_32_caracteres
      - WEBHOOK_URL=https://seu-dominio.com
      - GENERIC_TIMEZONE=America/Sao_Paulo
      - N8N_DEFAULT_LOCALE=pt-BR
    volumes:
      - n8n_data:/home/node/.n8n
    extra_hosts:
      - "host.docker.internal:host-gateway"

volumes:
  n8n_data:
EOF

# Iniciar n8n
docker-compose up -d
```

#### 3.2 Via NPM (Alternativo)
```bash
npm install n8n -g
export N8N_BASIC_AUTH_ACTIVE=true
export N8N_BASIC_AUTH_USER=admin
export N8N_BASIC_AUTH_PASSWORD=senha_admin_segura
n8n start
```

### 4. Configuração das APIs

#### 4.1 OpenAI API
1. Acesse [platform.openai.com](https://platform.openai.com/)
2. Crie uma conta e configure billing
3. Gere uma API key
4. Configure limite de uso (recomendado: $50/mês)

#### 4.2 Google Cloud APIs
```bash
# Instalar gcloud CLI
curl https://sdk.cloud.google.com | bash
source ~/.bashrc
gcloud init

# Criar projeto
gcloud projects create dalvonete-ai-$(date +%s)
gcloud config set project dalvonete-ai-PROJECT_ID

# Ativar APIs necessárias
gcloud services enable vision.googleapis.com
gcloud services enable calendar-json.googleapis.com
gcloud services enable sheets.googleapis.com

# Criar service account
gcloud iam service-accounts create dalvonete-ai \
    --display-name="Dalvonete AI Service Account"

# Gerar chave
gcloud iam service-accounts keys create ~/dalvonete-sa-key.json \
    --iam-account=dalvonete-ai@PROJECT_ID.iam.gserviceaccount.com
```

#### 4.3 Z-API (WhatsApp)
1. Acesse [z-api.io](https://z-api.io/)
2. Crie uma instância WhatsApp Business
3. Configure webhook: `https://seu-dominio.com/webhook/dalvonete`
4. Anote: Instance ID, Token, Client Token

### 5. Configuração do Nginx

```bash
# Criar configuração
sudo nano /etc/nginx/sites-available/dalvonete-ai

# Conteúdo do arquivo:
server {
    listen 80;
    server_name seu-dominio.com;

    location / {
        proxy_pass http://localhost:5678;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}

# Ativar site
sudo ln -s /etc/nginx/sites-available/dalvonete-ai /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

# Configurar SSL
sudo certbot --nginx -d seu-dominio.com
```

### 6. Importação dos Workflows

#### 6.1 Clone do Repositório
```bash
cd ~/
git clone https://github.com/seu-usuario/ai-sdr-dalvonete.git
cd ai-sdr-dalvonete
```

#### 6.2 Configuração de Credenciais
```bash
# Copiar arquivo de exemplo
cp config/environment.example.env .env

# Editar com suas credenciais
nano .env
```

#### 6.3 Importar Workflows
1. Acesse interface n8n: `https://seu-dominio.com`
2. Faça login (admin/senha_admin_segura)
3. Vá em Settings > Import workflow
4. Importe os arquivos na ordem:
   - `workflows/mcp-dalvonete.json`
   - `workflows/crm-registration.json`
   - `workflows/video-sender.json`
   - `workflows/main-sdr-workflow.json`

### 7. Configuração de Credenciais no n8n

#### 7.1 OpenAI API
- Name: `OpenAi account`
- API Key: `sua_openai_api_key`

#### 7.2 Google Calendar
- Name: `Google Calendar dalvonete`
- Type: OAuth2
- Client ID: `do_console_google_cloud`
- Client Secret: `do_console_google_cloud`

#### 7.3 Google Sheets
- Name: `Google Sheets dalvonete`
- Type: OAuth2
- Client ID: `do_console_google_cloud`
- Client Secret: `do_console_google_cloud`

#### 7.4 PostgreSQL
- Name: `Postgres account`
- Host: `localhost` (ou IP do servidor)
- Database: `dalvonete_ai`
- User: `n8n_user`
- Password: `senha_super_segura`
- Port: `5432`

#### 7.5 Redis
- Name: `Redis account`
- Host: `localhost`
- Port: `6379`
- Password: `sua_senha_redis`

### 8. Configuração do Google Calendar

```bash
# Criar calendário específico para o chalé
# No Google Calendar:
# 1. Criar novo calendário "CHALÉ"
# 2. Configurar compartilhamento com service account
# 3. Copiar Calendar ID (formato: xxxxxx@group.calendar.google.com)
```

### 9. Configuração do Google Sheets

```bash
# Criar planilha de controle
# 1. Criar "trava IA - Atendimento" no Google Sheets
# 2. Colunas: Nome, telefone, travar IA?, Ultimo envio
# 3. Compartilhar com service account (edit permissions)
# 4. Copiar Sheet ID da URL
```

### 10. Testes de Funcionamento

#### 10.1 Teste de Webhook
```bash
# Enviar teste para webhook
curl -X POST https://seu-dominio.com/webhook/dalvonete \
  -H "Content-Type: application/json" \
  -d '{
    "body": {
      "text": {"message": "Olá, teste"},
      "phone": "5537999999999",
      "senderName": "Teste",
      "isGroup": false,
      "fromMe": false,
      "fromApi": false,
      "messageId": "test123",
      "momment": "'$(date -Iseconds)'"
    }
  }'
```

#### 10.2 Verificar Logs
```bash
# Logs do n8n
docker logs n8n_dalvonete -f

# Logs do PostgreSQL
sudo tail -f /var/log/postgresql/postgresql-12-main.log

# Logs do Redis
sudo tail -f /var/log/redis/redis-server.log
```

### 11. Backup e Monitoramento

#### 11.1 Script de Backup
```bash
# Criar script de backup
cat > ~/backup-dalvonete.sh << 'EOF'
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backup/dalvonete"

mkdir -p $BACKUP_DIR

# Backup PostgreSQL
pg_dump -U n8n_user -h localhost dalvonete_ai > $BACKUP_DIR/db_$DATE.sql

# Backup workflows n8n
docker exec n8n_dalvonete n8n export:workflow --all --output=/tmp/workflows_$DATE.json
docker cp n8n_dalvonete:/tmp/workflows_$DATE.json $BACKUP_DIR/

# Cleanup (manter últimos 7 dias)
find $BACKUP_DIR -name "*.sql" -mtime +7 -delete
find $BACKUP_DIR -name "*.json" -mtime +7 -delete
EOF

chmod +x ~/backup-dalvonete.sh

# Agendar backup diário
echo "0 2 * * * /home/$USER/backup-dalvonete.sh" | crontab -
```

#### 11.2 Monitoramento
```bash
# Instalar htop para monitoramento
sudo apt install htop

# Configurar alertas simples
cat > ~/check-services.sh << 'EOF'
#!/bin/bash
if ! docker ps | grep -q n8n_dalvonete; then
    echo "N8N container is down!" | mail -s "Dalvonete AI Alert" admin@seu-dominio.com
fi

if ! systemctl is-active --quiet postgresql; then
    echo "PostgreSQL is down!" | mail -s "Dalvonete AI Alert" admin@seu-dominio.com
fi
EOF

chmod +x ~/check-services.sh
echo "*/5 * * * * /home/$USER/check-services.sh" | crontab -
```

### 12. Otimização de Performance

#### 12.1 Configuração PostgreSQL
```sql
-- Ajustar parâmetros no postgresql.conf
shared_buffers = 256MB
effective_cache_size = 1GB
maintenance_work_mem = 64MB
max_connections = 100
```

#### 12.2 Configuração Redis
```bash
# No redis.conf
maxmemory 512mb
maxmemory-policy allkeys-lru
```

### 13. Segurança

#### 13.1 Firewall
```bash
# Configurar UFW
sudo ufw allow ssh
sudo ufw allow 80
sudo ufw allow 443
sudo ufw --force enable
```

#### 13.2 Fail2ban
```bash
sudo apt install fail2ban

# Configurar para nginx
sudo nano /etc/fail2ban/jail.local
# [nginx-http-auth]
# enabled = true
```

### 🎉 Instalação Completa!

Após seguir todos os passos, seu AI SDR Dalvonete estará funcionando em:
- **Interface n8n**: `https://seu-dominio.com`
- **Webhook**: `https://seu-dominio.com/webhook/dalvonete`

### 🆘 Suporte

Se encontrar problemas durante a instalação:
1. Verifique os logs mencionados
2. Consulte a documentação oficial das ferramentas
3. Abra uma issue no repositório GitHub
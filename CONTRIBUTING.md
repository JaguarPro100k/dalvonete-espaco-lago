# 🤝 Guia de Contribuição

Obrigado por considerar contribuir com o projeto Dalvonete IA Agent! Este documento fornece diretrizes para contribuições.

## 📋 Índice

- [Código de Conduta](#código-de-conduta)
- [Como Posso Contribuir?](#como-posso-contribuir)
- [Processo de Desenvolvimento](#processo-de-desenvolvimento)
- [Padrões de Código](#padrões-de-código)
- [Mensagens de Commit](#mensagens-de-commit)
- [Pull Requests](#pull-requests)
- [Reportando Bugs](#reportando-bugs)
- [Sugerindo Melhorias](#sugerindo-melhorias)

## 📜 Código de Conduta

Este projeto segue um código de conduta para garantir um ambiente respeitoso e inclusivo:

- Seja respeitoso e profissional
- Aceite críticas construtivas
- Foque no que é melhor para o projeto
- Mostre empatia com outros membros da comunidade
- Use linguagem inclusiva e acolhedora

## 🛠️ Como Posso Contribuir?

### Reportar Bugs

Encontrou um bug? Ajude-nos a melhorar!

1. Verifique se o bug já não foi reportado nas [Issues](link)
2. Se não encontrar, abra uma nova issue
3. Use o template de bug report
4. Inclua o máximo de detalhes possível

### Sugerir Melhorias

Tem uma ideia para melhorar o projeto?

1. Verifique se já não existe uma sugestão similar
2. Abra uma issue com o template de feature request
3. Explique claramente o benefício da sua sugestão
4. Se possível, inclua exemplos de uso

### Contribuir com Código

1. Fork o repositório
2. Crie uma branch para sua feature (`git checkout -b feature/MinhaFeature`)
3. Faça suas alterações
4. Teste suas mudanças
5. Commit suas alterações
6. Push para sua branch
7. Abra um Pull Request

### Melhorar Documentação

- Correções de typos
- Clarificação de instruções
- Adição de exemplos
- Tradução para outros idiomas

## 🔄 Processo de Desenvolvimento

### 1. Configurar Ambiente

```bash
# Clone o repositório
git clone https://github.com/dalvonete/ia-agent.git
cd ia-agent

# Crie arquivo de configuração
cp config.example.env config.env

# Configure suas credenciais
nano config.env
```

### 2. Branches

Usamos o seguinte padrão de branches:

- `main` - Código em produção (protegida)
- `develop` - Desenvolvimento ativo
- `feature/*` - Novas funcionalidades
- `bugfix/*` - Correções de bugs
- `hotfix/*` - Correções urgentes em produção
- `docs/*` - Melhorias na documentação

### 3. Workflow

```bash
# Atualizar sua branch
git checkout develop
git pull origin develop

# Criar nova branch
git checkout -b feature/minha-feature

# Fazer alterações
# ... código ...

# Adicionar e commitar
git add .
git commit -m "feat: adiciona nova funcionalidade X"

# Push para seu fork
git push origin feature/minha-feature

# Abrir Pull Request no GitHub
```

## 📝 Padrões de Código

### JavaScript/n8n

```javascript
// ✅ Bom
function sanitizePhone(phone) {
  const cleaned = phone.replace(/\D/g, '');
  
  if (cleaned.length !== 13 || !cleaned.startsWith('55')) {
    throw new Error('Telefone inválido. Use formato: 553799999999');
  }
  
  return cleaned;
}

// ❌ Ruim
function sanitizePhone(phone){
const cleaned=phone.replace(/\D/g,'')
if(cleaned.length!==13||!cleaned.startsWith('55'))throw new Error('Telefone inválido. Use formato: 553799999999')
return cleaned
}
```

**Regras:**
- Use nomes descritivos para variáveis e funções
- Adicione comentários para lógica complexa
- Mantenha funções pequenas e focadas
- Use const/let ao invés de var
- Sempre trate erros

### JSON (Workflows n8n)

```json
{
  "name": "nome_descritivo",
  "nodes": [
    {
      "parameters": {
        "description": "Descrição clara do que o nó faz"
      }
    }
  ]
}
```

**Regras:**
- Use nomes descritivos em snake_case
- Sempre adicione descrição nos nós
- Organize nós logicamente
- Documente parâmetros importantes

### Documentação (Markdown)

```markdown
## Título Claro

Explicação concisa do conceito.

### Subtítulo

Detalhes específicos.

**Importante:** Use negrito para destacar pontos críticos.

```código
// Exemplos de código devem estar em blocos
```

- ✅ Use listas para pontos múltiplos
- ✅ Seja conciso e direto
```

## 💬 Mensagens de Commit

Usamos [Conventional Commits](https://www.conventionalcommits.org/pt-br/):

### Formato

```
<tipo>(<escopo>): <descrição>

[corpo opcional]

[rodapé opcional]
```

### Tipos

- `feat`: Nova funcionalidade
- `fix`: Correção de bug
- `docs`: Alteração em documentação
- `style`: Formatação, ponto e vírgula, etc (sem mudança de código)
- `refactor`: Refatoração de código
- `test`: Adição ou modificação de testes
- `chore`: Atualização de tarefas de build, configs, etc

### Exemplos

```bash
# ✅ Bom
feat(cadastro): adiciona validação de email
fix(envio-video): corrige timeout em vídeos grandes
docs(readme): atualiza instruções de instalação
refactor(calendar): simplifica lógica de busca de eventos

# ❌ Ruim
Update
Fixed bug
Changes
WIP
```

### Descrição

- Use imperativo: "adiciona" não "adicionado" ou "adicionando"
- Primeira letra minúscula
- Sem ponto final
- Máximo 72 caracteres
- Seja específico

### Corpo (opcional)

```
feat(cadastro): adiciona validação de email

- Valida formato usando regex
- Verifica se domínio existe
- Adiciona mensagem de erro clara
- Inclui testes unitários

Closes #123
```

## 🔍 Pull Requests

### Antes de Submeter

- [ ] Código segue os padrões do projeto
- [ ] Testes passam (se aplicável)
- [ ] Documentação atualizada
- [ ] Commits seguem padrão Conventional Commits
- [ ] Branch está atualizada com develop
- [ ] Não há conflitos

### Template de PR

```markdown
## Descrição

[Descrição clara do que este PR faz]

## Tipo de Mudança

- [ ] 🐛 Bug fix
- [ ] ✨ Nova funcionalidade
- [ ] 💥 Breaking change
- [ ] 📝 Documentação
- [ ] ♻️ Refatoração

## Testes

[Descreva os testes realizados]

## Checklist

- [ ] Código segue padrões do projeto
- [ ] Testes adicionados/atualizados
- [ ] Documentação atualizada
- [ ] Sem quebras de compatibilidade
```

### Processo de Revisão

1. Mantenedor revisa o código
2. Se aprovado: merge
3. Se mudanças necessárias: comentários e sugestões
4. Autor faz ajustes
5. Processo se repete até aprovação

## 🐛 Reportando Bugs

### Template de Bug Report

```markdown
**Descrição do Bug**
[Descrição clara e concisa do bug]

**Como Reproduzir**
1. Vá para '...'
2. Clique em '....'
3. Role até '....'
4. Veja erro

**Comportamento Esperado**
[O que você esperava que acontecesse]

**Comportamento Atual**
[O que realmente acontece]

**Screenshots**
[Se aplicável, adicione screenshots]

**Ambiente:**
- n8n Version: [ex: 1.0.0]
- Browser: [ex: Chrome 120]
- OS: [ex: Ubuntu 24]

**Logs**
```
[Cole logs relevantes aqui]
```

**Contexto Adicional**
[Qualquer outra informação relevante]
```

## 💡 Sugerindo Melhorias

### Template de Feature Request

```markdown
**Sua funcionalidade está relacionada a um problema?**
[Ex: Estou sempre frustrado quando...]

**Descreva a solução que você gostaria**
[Descrição clara e concisa do que você quer]

**Descreva alternativas que você considerou**
[Outras soluções ou funcionalidades que você considerou]

**Benefícios**
- [Benefício 1]
- [Benefício 2]

**Contexto Adicional**
[Screenshots, exemplos, links, etc]
```

## 📊 Prioridades

Contribuições são priorizadas assim:

1. 🔥 **Crítico**: Bugs de segurança, perda de dados
2. 🚨 **Alto**: Bugs que impedem uso, features bloqueantes
3. 📝 **Médio**: Melhorias de performance, UX
4. 💡 **Baixo**: Refatorações, documentação

## ❓ Dúvidas?

- 📧 Email: arbnbdalvanete@gmail.com
- 💬 Issues: [GitHub Issues](link)
- 📖 Docs: [Documentação completa](link)

## 🙏 Reconhecimentos

Agradecemos a todos os contribuidores!

- [@contributor1](link)
- [@contributor2](link)

---

**Obrigado por contribuir! 🎉**

# 🎫 Sistema de Classificação Automática de Tickets com IA

Automação inteligente que classifica tickets de suporte automaticamente 
usando n8n + Groq (LLaMA 3.3) + Airtable.

## 🎯 O que faz

- Recebe tickets via formulário web
- Classifica automaticamente por **categoria**, **prioridade** e **sentimento**
- Salva os resultados organizados no Airtable em tempo real
- Totalmente gratuito (Groq API + Airtable free tier)

## 🔄 Fluxo

Formulário → n8n → Groq API (LLaMA 3.3) → Airtable

## 🛠️ Stack

- **n8n** — orquestração do fluxo
- **Groq API** — classificação via LLM (gratuito)
- **Airtable** — armazenamento dos tickets
- **JavaScript** — integração via HTTP Request

## 📋 Categorias classificadas

| Campo | Opções |
|---|---|
| Categoria | Bug, Dúvida, Financeiro, Acesso, Outro |
| Prioridade | Baixa, Média, Alta, Crítica |
| Sentimento | Neutro, Frustrado, Satisfeito, Urgente |

## 🚀 Como rodar localmente

1. Clone o repositório
2. Instale o n8n: `npm install -g n8n`
3. Inicie com módulos habilitados:

$env:NODE_FUNCTION_ALLOW_BUILTIN="https,http"; npx n8n

4. Importe o `workflow.json` no n8n
5. Configure suas credenciais:
   - Groq API Key em `Credentials > OpenAI (Groq)`
   - Airtable Token em `Credentials > Airtable`
6. Publique o workflow e acesse a URL do formulário

## ⚙️ Variáveis necessárias

| Variável | Onde obter |
|---|---|
| Groq API Key | groq.com (gratuito) |
| Airtable Token | airtable.com/create/tokens |
| Airtable Base ID | URL da sua base |

## 🔮 Melhorias planejadas

- [ ] Notificação por email para tickets críticos
- [ ] Dashboard de analytics
- [ ] Integração com WhatsApp
- [ ] Escalação automática para humanos

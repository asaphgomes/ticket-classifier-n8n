# 🎫 Sistema de Classificação Automática de Tickets com IA

Automação inteligente de suporte ao cliente que classifica tickets, roteia para equipes e envia notificações multicanal em tempo real.

> Projeto de portfólio — construído com n8n + Groq (LLaMA 3.3) +
> Airtable + SendGrid + Telegram

![Workflow](workflow-screenshot.png)

---

## ✨ O que o sistema faz

- Recebe tickets via formulário web
- Classifica automaticamente por **categoria**, **prioridade** e **sentimento**
- Gera número de ticket único (TKT-XXXXXX)
- Define **SLA automático** por prioridade
- Separa tickets **críticos** dos normais com tratamento diferenciado
- Roteia para a equipe correta via Switch de 5 outputs
- Envia **email personalizado** por categoria (SendGrid)
- Envia **alerta em tempo real** no Telegram por canal dedicado
- Salva tudo no Airtable com dashboard de métricas

---

## 🗂️ Classificação automática

| Campo      | Opções                                    |
|------------|-------------------------------------------|
| Categoria  | Bug, Dúvida, Financeiro, Acesso, Outro    |
| Prioridade | Baixa, Média, Alta, Crítica               |
| Sentimento | Neutro, Frustrado, Satisfeito, Urgente    |
| SLA        | 1h (Crítica), 4h (Alta), 24h (Média), 72h (Baixa) |

---

## 🛠️ Stack

| Ferramenta     | Função                                 |
|----------------|----------------------------------------|
| n8n            | Orquestração do fluxo                  |
| Groq API       | Classificação por IA (LLaMA 3.3 — grátis) |
| Airtable       | Banco de dados + dashboard             |
| SendGrid       | Notificações por email                 |
| Telegram Bot   | Alertas em tempo real por canal        |
| JavaScript     | Integração HTTP com a Groq API         |

---

## 🚀 Versões

### v1.0 — Base
- Formulário de abertura de tickets
- Classificação automática por IA
- Armazenamento no Airtable

### v1.2 — Roteamento inteligente
- SLA automático por prioridade
- IF separando críticos dos normais
- Switch com 5 outputs por categoria
- Notificação por email via SendGrid
- Dashboard no Airtable

### v2.0 — Notificações em tempo real
- Integração com Telegram Bot
- Canal exclusivo para tickets críticos
- Canal por categoria para tickets normais
- Resolvido perda de dados entre nós com `$('Switch').item.json`

---

## ▶️ Como rodar localmente

1. Clone o repositório
2. Instale o n8n: `npm install -g n8n`
3. Inicie com módulos habilitados:

```powershell
$env:NODE_FUNCTION_ALLOW_BUILTIN="https,http"; npx n8n
```

4. Importe o `workflow.json` no n8n
5. Configure as credenciais:

| Credencial       | Onde obter                        |
|------------------|-----------------------------------|
| Groq API Key     | console.groq.com (gratuito)       |
| Airtable Token   | airtable.com/create/tokens        |
| SendGrid API Key | app.sendgrid.com                  |
| Telegram Token   | @BotFather no Telegram            |

6. Ative o workflow e acesse a URL do formulário

---

## 🧠 Engenharia de Prompt

A classificação é feita via LLaMA 3.3 (Groq) com um prompt estruturado
que instrui o modelo a retornar exclusivamente JSON, sem texto adicional.

**Campos classificados:**
- `categoria` — tipo do problema (Bug, Dúvida, Financeiro, Acesso, Outro)
- `prioridade` — urgência (Baixa, Média, Alta, Crítica)
- `sentimento` — estado emocional do cliente (Neutro, Frustrado, Satisfeito, Urgente)
- `resumo` — síntese do problema em uma frase
- `confianca` — score de 0 a 1 indicando certeza da classificação

**Decisão de fallback:**
Se `confianca < 0.7`, o ticket não segue o fluxo automático.
É marcado como `needs_review` e um alerta é enviado ao Telegram
para revisão humana — evitando roteamento incorreto com baixa certeza.

**Limitações conhecidas:**
- Tickets muito vagos ou ambíguos geram confiança baixa (comportamento esperado)
- O modelo pode classificar errado em edge cases fora do domínio de suporte
- Em caso de falha da API, o sistema entra em graceful degradation com `confianca: 0`

---

## 📥 Exemplo de Input / Output

**Input (formulário):**
```json
{
  "Nome": "Carlos Silva",
  "Email": "carlos@teste.com",
  "Descrição do Problema": "O sistema caiu completamente, nenhum usuário consegue acessar"
}
```

**Output (classificação da IA):**
```json
{
  "Ticket": "TKT-827341",
  "Categoria": "Bug",
  "Prioridade": "Critica",
  "Sentimento": "Urgente",
  "Resumo": "Sistema completamente indisponível para todos os usuários",
  "Confianca": 0.97,
  "SLA": "1 hora",
  "TempoResposta": "843ms"
}
```

**Ações disparadas automaticamente:**
- ✅ Registro salvo no Airtable
- ✅ Email urgente enviado via SendGrid
- ✅ Alerta no grupo "Tickets Críticos" do Telegram

---

## 💼 Business Impact

Este sistema substitui um processo manual de triagem de tickets que tipicamente envolve:

| Processo manual | Com automação |
|-----------------|---------------|
| Leitura e categorização por humano | Classificação por IA em < 1 segundo |
| Encaminhamento manual por email | Roteamento automático por categoria |
| SLA definido caso a caso | SLA calculado automaticamente por prioridade |
| Sem visibilidade de sentimento | Análise de sentimento em tempo real |
| Relatório manual semanal | Relatório gerado automaticamente todo domingo |
| Sem fallback para incerteza | Tickets ambíguos sinalizados para revisão humana |

**Estimativa de impacto:**
- Redução de ~80% no tempo de triagem manual
- Tempo médio de classificação: < 1 segundo (vs. minutos manualmente)
- Zero tickets perdidos — graceful degradation garante fallback sempre

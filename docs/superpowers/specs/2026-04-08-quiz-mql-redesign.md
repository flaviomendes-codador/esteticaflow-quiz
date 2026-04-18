# Quiz MQL Redesign — Spec

## Contexto

O quiz EsteticaFlow (index.html) passou por uma mudança estratégica: ao invés de segmentar por especialidade médica (nicho), qualifica por maturidade de marketing. O Step 2 já foi atualizado na sessão anterior para coletar `temAds` (sim/pausado/nao) no lugar de `nicho`.

**Problema atual:** O JavaScript ainda usa `A.nicho` e o objeto `nichoICP` para calcular os resultados — gerando bug silencioso (undefined) que quebra todos os cálculos. Steps 5 e 6 também coletam variáveis descontinuadas (`objetivos`, `controle`).

---

## Objetivo

Completar a reformulação do quiz para qualificar MQL por dois vetores adicionais:
1. **Maturidade operacional** — tamanho da equipe de atendimento
2. **Urgência de decisão** — quando quer resolver o problema

---

## ICP e MQL Alvo

- **ICP:** Dono de clínica com faturamento >R$60K/mês, ≥2 secretárias, investe em ads (R$3-4K/mês)
- **MQL perfeito:** Tem ads ativos + 2+ secretárias + urgência para resolver este mês
- **Desqualificadores:** Sem ads, só ele no atendimento, "não tenho pressa"

---

## Variáveis do Quiz (estado final)

| Variável | Step | Valores | Status |
|---|---|---|---|
| `name` | 1 | string | ✓ existente |
| `temAds` | 2 | sim / pausado / nao | ✓ já atualizado |
| `leads` | 3 | 20 / 55 / 120 / 200 | ✓ existente |
| `resposta` | 4 | 5min / 1h / 4h / depois | ✓ existente |
| `equipe` | 5 | eu / 1 / 2mais / team | ✗ substituir objetivos |
| `urgencia` | 6 | mes / trimestre / pesquisando / nao | ✗ substituir controle |
| `ticket` | 7 | 800 / 2500 / 7000 / 15000 | ✓ existente |

**Remover:** `nicho`, `objetivos`, `controle`

---

## Step 5 — Nova Pergunta: Equipe

**Pergunta:** "Quantas pessoas fazem o atendimento de leads na sua clínica?"
**Sub:** "Contato inicial, qualificação, resposta de WhatsApp, agendamento."

| Label | Sub | Valor |
|---|---|---|
| Só eu mesmo | Faço tudo: atendo, agenda e trato o paciente | `eu` |
| 1 secretária ou recepcionista | Uma pessoa cuida do atendimento | `1` |
| 2 ou mais secretárias | Tenho equipe de atendimento | `2mais` |
| Equipe + coordenador de atendimento | Operação estruturada com liderança | `team` |

Navegação: `selAuto('equipe', valor, this, 6)` — sem multiselect.

---

## Step 6 — Nova Pergunta: Urgência

**Pergunta:** "Com que urgência você quer resolver o problema de conversão de leads?"
**Sub:** "Sem julgamento — isso define o que priorizamos no seu resultado."

| Label | Sub | Valor |
|---|---|---|
| Preciso resolver ainda este mês | Cada semana que passa custa mais | `mes` |
| Nos próximos 2 a 3 meses | Tenho tempo para planejar bem | `trimestre` |
| Ainda estou pesquisando opções | Quero entender o mercado antes de decidir | `pesquisando` |
| Não tenho pressa pra isso | Situação está sob controle por enquanto | `nao` |

Navegação: `selAuto('urgencia', valor, this, 7)` — sem botão extra.

---

## JavaScript — Mudanças

### 1. Objeto A
```js
// ANTES
const A = { name:'', nicho:'', leads:0, resposta:'', objetivos:[], controle:'', ticket:0 };
// DEPOIS
const A = { name:'', temAds:'', leads:0, resposta:'', equipe:'', urgencia:'', ticket:0 };
```

### 2. Taxas de conversão por temAds (substitui nichoICP)
```js
const temAdsConv = {
  'sim':     { convBase: 0.09, convSist: 0.22 },
  'pausado': { convBase: 0.08, convSist: 0.20 },
  'nao':     { convBase: 0.07, convSist: 0.19 }
};
```

### 3. buildResult() — linhas críticas
```js
// ANTES
const icp    = nichoICP[A.nicho] || nichoICP['harmonizacao'];
const pResp  = Math.min(0.92, pBase + icp.perdaPlus);
// DEPOIS
const icp    = temAdsConv[A.temAds] || temAdsConv['nao'];
const pResp  = Math.min(0.92, pBase);
```

Textos que mudam:
- `resultTitle`: `<em>${A.name}</em>, aqui está o diagnóstico real da sua clínica`
- `produtoBadge`: `'EstéticaFlow IA — Diagnóstico Personalizado'`
- `resultSub`: manter texto genérico (remover referência a icp.label)
- `finDesc`: manter lógica mas sem `icp.label`
- `goalsList`: substituir por personalização baseada em `urgencia`
- `ctaSub`: usar `urgenciaTexto[A.urgencia]`

```js
const urgenciaTexto = {
  mes:         'implementar ainda este mês',
  trimestre:   'implementar nos próximos meses',
  pesquisando: 'entender suas opções com clareza',
  nao:         'transformar sua operação quando estiver pronto'
};
```

### 4. saveLeadToSupabase() — form_score e form_answers
```js
// Novo form_score (max 110)
const scoreMap = {
  temAds:   { sim: 40, pausado: 20, nao: 10 },
  equipe:   { team: 40, '2mais': 30, '1': 15, eu: 5 },
  urgencia: { mes: 30, trimestre: 15, pesquisando: 5, nao: 0 }
};
const form_score = (scoreMap.temAds[A.temAds]    || 10)
                 + (scoreMap.equipe[A.equipe]     ||  5)
                 + (scoreMap.urgencia[A.urgencia] ||  0);

// Novo form_answers
form_answers: {
  temAds:   A.temAds,
  leads:    A.leads,
  ticket:   A.ticket,
  resposta: A.resposta,
  equipe:   A.equipe,
  urgencia: A.urgencia,
}
```

### 5. Remover
- Objeto `nichoICP` (linhas ~1169-1190)
- Objeto `objLabel` (linhas ~1194-1201)
- Lógica de `multiSel` para objetivos (btnS5 disabled)
- Referência a `respScore` no topo do `saveLeadToSupabase`

---

## Lead Scoring

| Score | Classificação |
|---|---|
| ≥ 80 | MQL Perfeito |
| 60-79 | MQL Qualificado |
| 40-59 | Lead Morno |
| < 40 | Lead Frio / Desqualificado |

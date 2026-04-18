# Quiz MQL Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Completar a reformulação do quiz EsteticaFlow substituindo Steps 5 e 6 por perguntas de qualificação MQL, e corrigir todo o JavaScript que ainda usa variáveis descontinuadas (nicho, objetivos, controle).

**Architecture:** Todas as mudanças são em um único arquivo `index.html`. O HTML dos steps é substituído diretamente. O JavaScript tem três focos: (1) corrigir o objeto de dados `A`, (2) substituir `nichoICP` por `temAdsConv`, (3) corrigir `buildResult()` e `saveLeadToSupabase()`.

**Tech Stack:** HTML/CSS/JS vanilla, Supabase JS SDK v2

**Spec:** `docs/superpowers/specs/2026-04-08-quiz-mql-redesign.md`

---

## Arquivo único afetado

- **Modify:** `index.html` — todo o trabalho está aqui

---

## Task 1: Corrigir o objeto A e remover objetos obsoletos

**Files:**
- Modify: `index.html` — seção JavaScript (volta ~linha 1165)

- [ ] **Step 1: Atualizar o objeto `A` e a constante `TOTAL`**

Localizar (linha ~1165):
```js
const A = { name:'', nicho:'', leads:0, resposta:'', objetivos:[], controle:'', ticket:0 };
const TOTAL = 7;
```

Substituir por:
```js
const A = { name:'', temAds:'', leads:0, resposta:'', equipe:'', urgencia:'', ticket:0 };
const TOTAL = 7;
```

- [ ] **Step 2: Remover o objeto `nichoICP`**

Localizar e deletar o bloco inteiro (linhas ~1169-1190):
```js
const nichoICP = {
  harmonizacao: { ... },
  estetica_avancada: { ... },
  spa_medico: { ... },
  solo: { ... },
  pele: { ... }
};
```

No mesmo lugar, inserir:
```js
const temAdsConv = {
  'sim':     { convBase: 0.09, convSist: 0.22 },
  'pausado': { convBase: 0.08, convSist: 0.20 },
  'nao':     { convBase: 0.07, convSist: 0.19 }
};
```

- [ ] **Step 3: Remover o objeto `objLabel`**

Localizar e deletar o bloco inteiro (linhas ~1194-1201):
```js
const objLabel = {
  resposta_rapida: '...',
  parar_perder:    '...',
  followup:        '...',
  escalar:         '...',
  previsibilidade: '...',
  noshow:          '...'
};
```

- [ ] **Step 4: Verificar manualmente que nenhuma outra referência a `nichoICP`, `objLabel`, `A.nicho`, `A.objetivos`, `A.controle` permanece no arquivo**

```bash
grep -n "nichoICP\|objLabel\|A\.nicho\|A\.objetivos\|A\.controle\|respScore" index.html
```
Esperado: zero resultados.

- [ ] **Step 5: Commit**

```bash
cd "C:\Users\DELL\CLAUDE-CODE-PROJETOS\esteticaflow-quiz"
git add index.html
git commit -m "refactor: remover nichoICP/objLabel, atualizar objeto A para variáveis MQL"
```

---

## Task 2: Corrigir saveLeadToSupabase()

**Files:**
- Modify: `index.html` — função `saveLeadToSupabase` (linhas ~32-57)

- [ ] **Step 1: Substituir a função `saveLeadToSupabase` inteira**

Localizar o bloco atual:
```js
async function saveLeadToSupabase(phone) {
  const utms = getUTMs();
  const respScore = { '5min': 20, '1h': 50, '4h': 75, 'depois': 95 };
  const form_score = respScore[A.resposta] || 50;

  const { error } = await _supabase.from('leads').insert({
    tenant_id:    TENANT_ID,
    name:         A.name || 'Anônimo',
    whatsapp:     phone,
    form_score:   form_score,
    form_answers: {
      nicho:     A.nicho,
      leads:     A.leads,
      ticket:    A.ticket,
      resposta:  A.resposta,
      objetivos: A.objetivos,
      controle:  A.controle,
    },
    lgpd_consent: true,
    lgpd_consent_at: new Date().toISOString(),
    ...utms,
  });

  if (error) console.error('[FunnelMetrics] Erro ao salvar lead:', error.message);
  return !error;
}
```

Substituir por:
```js
async function saveLeadToSupabase(phone) {
  const utms = getUTMs();
  const scoreMap = {
    temAds:   { sim: 40, pausado: 20, nao: 10 },
    equipe:   { team: 40, '2mais': 30, '1': 15, eu: 5 },
    urgencia: { mes: 30, trimestre: 15, pesquisando: 5, nao: 0 }
  };
  const form_score = (scoreMap.temAds[A.temAds]    || 10)
                   + (scoreMap.equipe[A.equipe]     ||  5)
                   + (scoreMap.urgencia[A.urgencia] ||  0);

  const { error } = await _supabase.from('leads').insert({
    tenant_id:    TENANT_ID,
    name:         A.name || 'Anônimo',
    whatsapp:     phone,
    form_score:   form_score,
    form_answers: {
      temAds:   A.temAds,
      leads:    A.leads,
      ticket:   A.ticket,
      resposta: A.resposta,
      equipe:   A.equipe,
      urgencia: A.urgencia,
    },
    lgpd_consent: true,
    lgpd_consent_at: new Date().toISOString(),
    ...utms,
  });

  if (error) console.error('[FunnelMetrics] Erro ao salvar lead:', error.message);
  return !error;
}
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "fix: atualizar saveLeadToSupabase com novo form_score MQL e form_answers"
```

---

## Task 3: Substituir HTML do Step 5 (Equipe)

**Files:**
- Modify: `index.html` — bloco `<div class="step" id="step5">` (linhas ~851-889)

- [ ] **Step 1: Localizar o Step 5 atual**

O bloco começa em `<!-- ===== STEP 5 ===== -->` e termina antes de `<!-- ===== STEP 6 ===== -->`. Contém a pergunta de objetivos com multiselect e o botão `btnS5`.

- [ ] **Step 2: Substituir o HTML inteiro do Step 5**

Substituir todo o bloco (do `<!-- ===== STEP 5 ===== -->` até antes do `<!-- ===== STEP 6 ===== -->`) por:

```html
  <!-- ===== STEP 5 ===== -->
  <div class="step" id="step5">
    <div class="step-dots-wrap" id="dots5"></div>
    <h2>Quantas pessoas fazem o atendimento de leads na sua clínica?</h2>
    <p class="sub">Contato inicial, qualificação, resposta de WhatsApp, agendamento.</p>
    <div class="options">
      <div class="option" onclick="selAuto('equipe','eu',this,6)">
        <div class="option-icon"><svg width="19" height="19" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="M15.75 6a3.75 3.75 0 1 1-7.5 0 3.75 3.75 0 0 1 7.5 0ZM4.501 20.118a7.5 7.5 0 0 1 14.998 0A17.933 17.933 0 0 1 12 21.75c-2.676 0-5.216-.584-7.499-1.632Z"/></svg></div>
        <div class="option-text"><strong>Só eu mesmo</strong><span>Faço tudo: atendo, agenda e trato o paciente</span></div>
        <div class="option-check">✓</div>
      </div>
      <div class="option" onclick="selAuto('equipe','1',this,6)">
        <div class="option-icon"><svg width="19" height="19" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="M15.75 6a3.75 3.75 0 1 1-7.5 0 3.75 3.75 0 0 1 7.5 0ZM4.501 20.118a7.5 7.5 0 0 1 14.998 0A17.933 17.933 0 0 1 12 21.75c-2.676 0-5.216-.584-7.499-1.632Z"/></svg></div>
        <div class="option-text"><strong>1 secretária ou recepcionista</strong><span>Uma pessoa cuida de todo o atendimento</span></div>
        <div class="option-check">✓</div>
      </div>
      <div class="option" onclick="selAuto('equipe','2mais',this,6)">
        <div class="option-icon"><svg width="19" height="19" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="M18 18.72a9.094 9.094 0 0 0 3.741-.479 3 3 0 0 0-4.682-2.72m.94 3.198.001.031c0 .225-.012.447-.037.666A11.944 11.944 0 0 1 12 21c-2.17 0-4.207-.576-5.963-1.584A6.062 6.062 0 0 1 6 18.719m12 0a5.971 5.971 0 0 0-.941-3.197m0 0A5.995 5.995 0 0 0 12 12.75a5.995 5.995 0 0 0-5.058 2.772m0 0a3 3 0 0 0-4.681 2.72 8.986 8.986 0 0 0 3.74.477m.94-3.197a5.971 5.971 0 0 0-.94 3.197M15 6.75a3 3 0 1 1-6 0 3 3 0 0 1 6 0Zm6 3a2.25 2.25 0 1 1-4.5 0 2.25 2.25 0 0 1 4.5 0Zm-13.5 0a2.25 2.25 0 1 1-4.5 0 2.25 2.25 0 0 1 4.5 0Z"/></svg></div>
        <div class="option-text"><strong>2 ou mais secretárias</strong><span>Tenho equipe dedicada ao atendimento</span></div>
        <div class="option-check">✓</div>
      </div>
      <div class="option" onclick="selAuto('equipe','team',this,6)">
        <div class="option-icon"><svg width="19" height="19" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="M3.75 21h16.5M4.5 3h15M5.25 3v18m13.5-18v18M9 6.75h1.5m-1.5 3h1.5m-1.5 3h1.5m3-6H15m-1.5 3H15m-1.5 3H15M9 21v-3.375c0-.621.504-1.125 1.125-1.125h3.75c.621 0 1.125.504 1.125 1.125V21"/></svg></div>
        <div class="option-text"><strong>Equipe + coordenador de atendimento</strong><span>Operação estruturada com liderança dedicada</span></div>
        <div class="option-check">✓</div>
      </div>
    </div>
  </div>
```

- [ ] **Step 3: Verificar que não há mais referência a `btnS5` ou `multiSel` para objetivos**

```bash
grep -n "btnS5\|multiSel.*objetivos\|A\.objetivos" index.html
```
Esperado: zero resultados.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: substituir step 5 — pergunta de equipe de atendimento para qualificação MQL"
```

---

## Task 4: Substituir HTML do Step 6 (Urgência)

**Files:**
- Modify: `index.html` — bloco `<div class="step" id="step6">` (linhas ~892-918)

- [ ] **Step 1: Localizar o Step 6 atual**

O bloco começa em `<!-- ===== STEP 6 ===== -->` e termina antes de `<!-- ===== STEP 7 ===== -->`. Contém a pergunta de controle com 4 opções.

- [ ] **Step 2: Substituir o HTML inteiro do Step 6**

Substituir todo o bloco (do `<!-- ===== STEP 6 ===== -->` até antes do `<!-- ===== STEP 7 ===== -->`) por:

```html
  <!-- ===== STEP 6 ===== -->
  <div class="step" id="step6">
    <div class="step-dots-wrap" id="dots6"></div>
    <h2>Com que urgência você quer resolver o problema de conversão de leads?</h2>
    <p class="sub">Sem julgamento — isso define o que priorizamos no seu resultado.</p>
    <div class="options">
      <div class="option" onclick="selAuto('urgencia','mes',this,7)">
        <div class="option-icon"><svg width="19" height="19" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="m3.75 13.5 10.5-11.25L12 10.5h8.25L9.75 21.75 12 13.5H3.75Z"/></svg></div>
        <div class="option-text"><strong>Preciso resolver ainda este mês</strong><span>Cada semana que passa representa dinheiro saindo pela porta</span></div>
        <div class="option-check">✓</div>
      </div>
      <div class="option" onclick="selAuto('urgencia','trimestre',this,7)">
        <div class="option-icon"><svg width="19" height="19" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="M6.75 3v2.25M17.25 3v2.25M3 18.75V7.5a2.25 2.25 0 0 1 2.25-2.25h13.5A2.25 2.25 0 0 1 21 7.5v11.25m-18 0A2.25 2.25 0 0 0 5.25 21h13.5A2.25 2.25 0 0 0 21 18.75m-18 0v-7.5A2.25 2.25 0 0 1 5.25 9h13.5A2.25 2.25 0 0 1 21 11.25v7.5"/></svg></div>
        <div class="option-text"><strong>Nos próximos 2 a 3 meses</strong><span>Tenho tempo para planejar bem antes de implementar</span></div>
        <div class="option-check">✓</div>
      </div>
      <div class="option" onclick="selAuto('urgencia','pesquisando',this,7)">
        <div class="option-icon"><svg width="19" height="19" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="m21 21-5.197-5.197m0 0A7.5 7.5 0 1 0 5.196 5.196a7.5 7.5 0 0 0 10.607 10.607Z"/></svg></div>
        <div class="option-text"><strong>Ainda estou pesquisando opções</strong><span>Quero entender o mercado antes de tomar uma decisão</span></div>
        <div class="option-check">✓</div>
      </div>
      <div class="option" onclick="selAuto('urgencia','nao',this,7)">
        <div class="option-icon"><svg width="19" height="19" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round"><path d="M12 6v6h4.5m4.5 0a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z"/></svg></div>
        <div class="option-text"><strong>Não tenho pressa pra isso</strong><span>Situação está sob controle por enquanto</span></div>
        <div class="option-check">✓</div>
      </div>
    </div>
  </div>
```

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: substituir step 6 — pergunta de urgência para qualificação MQL"
```

---

## Task 5: Corrigir buildResult()

**Files:**
- Modify: `index.html` — função `buildResult()` (linhas ~1301-1345)

- [ ] **Step 1: Substituir a função `buildResult()` inteira**

Localizar o bloco inteiro da função e substituir por:

```js
function buildResult() {
  const icp    = temAdsConv[A.temAds] || temAdsConv['nao'];
  const leads  = parseInt(A.leads)    || 55;
  const ticket = parseInt(A.ticket)   || 2500;
  const pBase  = perdaResposta[A.resposta] || 0.55;
  const pResp  = Math.min(0.92, pBase);

  const leadsPerdidos    = Math.max(1, Math.round(leads * pResp));
  const clientesPerdidos = Math.max(1, Math.round(leadsPerdidos * icp.convBase));
  const receitaPerdida   = clientesPerdidos * ticket;
  const potencialMensal  = receitaPerdida;
  const potencial12m     = potencialMensal * 12;

  const leadsRecuperados = Math.max(1, Math.round(leadsPerdidos * 0.65));
  const clientesNovos    = Math.max(1, Math.round(leadsRecuperados * icp.convSist * 0.5));
  const funcs            = Math.max(1, Math.ceil(leadsRecuperados / 50));
  const custoEquipe      = funcs * 4500;

  const fmt = n => 'R$ ' + n.toLocaleString('pt-BR');

  const urgenciaTexto = {
    mes:         'implementar ainda este mês',
    trimestre:   'implementar nos próximos meses',
    pesquisando: 'entender suas opções com clareza',
    nao:         'transformar sua operação quando estiver pronto'
  };

  document.getElementById('resultTitle').innerHTML =
    `<em>${A.name}</em>, aqui está o diagnóstico real da sua clínica`;
  document.getElementById('resultSub').textContent =
    'Calculamos com base nos dados que você informou — volume de leads, ticket médio e tempo de resposta. O que você vai ver abaixo é o dinheiro real que está saindo pela porta da sua clínica todo mês.';
  document.getElementById('produtoBadge').textContent = 'EstéticaFlow IA — Diagnóstico Personalizado';

  document.getElementById('lossVal').textContent       = fmt(receitaPerdida) + '/mês';
  document.getElementById('gainVal').textContent       = fmt(potencialMensal) + '/mês';
  document.getElementById('recurrenceVal').textContent = fmt(potencial12m);
  document.getElementById('teamVal').textContent       = funcs + (funcs === 1 ? ' funcionário' : ' funcionários');
  document.getElementById('teamSub').textContent       =
    `só para tentar dar conta dos leads — ${fmt(custoEquipe)}/mês em salário + encargos, sem garantia de resultado`;

  document.getElementById('finDesc').innerHTML =
    `Dos <strong>${leads} leads que chegam por mês</strong> na sua clínica, <strong>${leadsPerdidos} somem sem virar paciente</strong> — é o que acontece quando o atendimento demora ou não existe fora do horário comercial. Com apenas ~${Math.round(icp.convBase*100)}% fechando hoje, isso representa <strong>${clientesPerdidos} pacientes perdidos × ${fmt(ticket)} de ticket = ${fmt(receitaPerdida)} que você deixa na mesa todo mês</strong>. Com o processo certo, essa taxa sobe para ~${Math.round(icp.convSist*100)}% — gerando <strong>+${clientesNovos} pacientes extras por mês</strong>. Em 12 meses, o impacto acumulado na sua clínica chega a <strong>${fmt(potencial12m)}</strong>.`;

  document.getElementById('goalsList').innerHTML =
    `<div class="goal-item"><span class="goal-check">—</span>Parar de perder leads fora do horário comercial</div>
     <div class="goal-item"><span class="goal-check">—</span>Qualificar e priorizar quem está pronto para agendar</div>
     <div class="goal-item"><span class="goal-check">—</span>Crescer o faturamento sem aumentar equipe</div>`;

  document.getElementById('ctaSub').textContent =
    `Em 20 minutos de call, você recebe um diagnóstico completo da sua operação e tem a oportunidade de conhecer o sistema que vai ${urgenciaTexto[A.urgencia] || 'implementar ainda este mês'} — com um plano claro para transformar seus leads em pacientes.`;
}
```

- [ ] **Step 2: Verificar que não há mais referências a variáveis obsoletas no arquivo inteiro**

```bash
grep -n "A\.nicho\|A\.objetivos\|A\.controle\|nichoICP\|objLabel\|icp\.label\|icp\.produto\|icp\.perdaPlus\|respScore" index.html
```
Esperado: zero resultados.

- [ ] **Step 3: Abrir o quiz no browser e testar o fluxo completo**

- Percorrer todos os 7 steps
- Verificar que o resultado aparece com valores calculados (não NaN/undefined)
- Abrir Console do browser e verificar que não há erros JS

- [ ] **Step 4: Commit final**

```bash
git add index.html
git commit -m "fix: corrigir buildResult() para usar temAdsConv e variáveis MQL — remove dependência de nicho"
```

---

## Verificação Final

Após completar todas as tasks:

- [ ] `grep -n "nicho\|objetivos\|controle\|nichoICP\|objLabel" index.html` → apenas comentários HTML se houver, zero referências em JS
- [ ] Abrir `index.html` localmente, completar o quiz de ponta a ponta
- [ ] Verificar no console do browser: sem erros, sem `undefined` nos valores calculados
- [ ] Verificar que o form_score máximo (sim + team + mes) = 110
- [ ] Push para GitHub Pages

```bash
git push origin master
```

---

## Resumo das mudanças por tipo

| Tipo | Qtd | Descrição |
|---|---|---|
| Bug fix crítico | 1 | `buildResult()` usava `A.nicho` → undefined |
| Bug fix | 1 | `saveLeadToSupabase()` enviava `nicho` removido |
| Feature | 2 | Steps 5 e 6 com perguntas MQL (equipe + urgência) |
| Refactor | 1 | Remove `nichoICP`, `objLabel`, adiciona `temAdsConv` |
| Data | 1 | Novo `form_score` com scoring multi-sinal (max 110) |

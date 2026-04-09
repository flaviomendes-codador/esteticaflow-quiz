# PRD — EstéticaFlow Quiz (Google Stitch)
**Produto:** Quiz de diagnóstico de leads para clínicas de estética  
**Objetivo:** Qualificar MQL (clínica com ads ativos, equipe, urgência) e mostrar perda de receita para justificar projeto de R$10K  
**Stack atual:** HTML/CSS/JS vanilla + Supabase  
**Target:** Rebuild no Google Stitch com máxima gamificação

---

## 1. Objetivo do Quiz

Filtrar o MQL perfeito sem perguntar diretamente sobre orçamento. O lead deve **sentir** que está perdendo dinheiro antes de ver o preço do projeto.

**Jornada emocional:**
1. "Interessante, vou ver meu diagnóstico" (curiosidade)
2. "Caramba, é a minha realidade mesmo" (reconhecimento)
3. "R$40K por mês? Não sabia que era tanto" (choque)
4. "Preciso resolver isso agora" (urgência)

---

## 2. Fluxo Completo (8 Steps)

| Step | Pergunta | Campo | Propósito |
|------|----------|-------|-----------|
| 1 | Nome | `name` | Personalização |
| 2 | Especialidade | `especialidade` | Gamificação + segmento |
| 3 | Tem anúncios? | `temAds` | Qualificação MQL crítica |
| 4 | Leads/mês | `leads` | Volume para cálculo |
| 5 | Tempo de resposta | `resposta` | Fator de perda |
| 6 | Equipe de atendimento | `equipe` | Qualificação MQL |
| 7 | Urgência | `urgencia` | Temperatura do lead |
| 8 | Ticket médio | `ticket` | Valor do cálculo |
| — | Loading + Resultado | — | Diagnóstico + CTA |

---

## 3. Especialidades (Step 2)

Após escolher, os **subtítulos** dos steps 3, 4, 5 e 8 mudam para referenciar a especialidade.

| Value | Label | convBase | convSist | Ticket ref | Termo |
|-------|-------|----------|----------|------------|-------|
| `harmonizacao` | Harmonização Facial | 5% | 21% | R$2.500 | paciente |
| `bucomaxilo` | Bucomaxilofacial | 7% | 25% | R$7.000 | paciente cirúrgico |
| `odontologia_estetica` | Odontologia Estética | 6% | 23% | R$4.500 | paciente estético |
| `estetica_avancada` | Estética Avançada/Laser | 5% | 22% | R$3.000 | paciente |
| `spa_medico` | Spa Médico / Clínica Completa | 5% | 20% | R$2.000 | cliente |
| `dermatologia` | Dermatologia Clínica | 6% | 22% | R$2.500 | paciente |

**Gamificação Stitch:** Mudar fundo/ilustração/animação por especialidade. Ex: harmonização = paleta rose gold, bucomaxilo = azul cirúrgico profissional.

---

## 4. Opções de Cada Pergunta

### Step 3 — Tem Anúncios?
| Value | Label | Subtexto |
|-------|-------|---------|
| `sim` | Sim, tenho campanhas ativas | Já invisto em tráfego pago e gero leads pelos anúncios |
| `pausado` | Já tive, mas pausei | Tive campanhas no passado mas hoje não estou rodando |
| `nao` | Não, meus leads vêm só do orgânico | Instagram, indicações ou busca sem investimento em ads |

**Nota:** `nao` = lead desqualificado. No Stitch, pode mostrar mensagem suave: "Seu diagnóstico vai ser diferente — seu potencial está no orgânico."

### Step 4 — Leads/mês
| Value (número) | Label | Subtexto |
|----------------|-------|---------|
| `20` | Menos de 30 leads/mês | Ainda construindo volume |
| `55` | 30 a 80 leads/mês | Volume crescendo, mas perda já visível |
| `120` | 80 a 150 leads/mês | Alto volume — difícil dar conta manualmente |
| `200` | Mais de 150 leads/mês | Potencial enorme sendo subutilizado |

### Step 5 — Tempo de Resposta
| Value | Label | Subtexto |
|-------|-------|---------|
| `5min` | Até 5 minutos | Resposta quase imediata — ótimo ponto de partida |
| `1h` | 30 minutos a 1 hora | Razoável, mas há espaço para crescer muito mais |
| `4h` | 1 a 4 horas | Média do Brasil — grande janela de oportunidade |
| `depois` | Mais de 4 horas / dia seguinte | Potencial gigante esperando para ser destravado |

**Pesos de perda:** 5min=10%, 1h=28%, 4h=55%, depois=75%

### Step 6 — Equipe de Atendimento
| Value | Label | Subtexto |
|-------|-------|---------|
| `eu` | Só eu mesmo | Faço tudo: atendo, agenda e trato o paciente |
| `1` | 1 secretária ou recepcionista | Uma pessoa cuida de todo o atendimento |
| `2mais` | 2 ou mais secretárias | Tenho equipe dedicada ao atendimento |
| `team` | Equipe + coordenador | Operação estruturada com liderança dedicada |

### Step 7 — Urgência
| Value | Label | Subtexto |
|-------|-------|---------|
| `mes` | Preciso resolver ainda este mês | Cada semana representa dinheiro saindo pela porta |
| `trimestre` | Nos próximos 2 a 3 meses | Tenho tempo para planejar antes de implementar |
| `pesquisando` | Ainda estou pesquisando opções | Quero entender o mercado antes de decidir |
| `nao` | Não tenho pressa pra isso | Situação está sob controle por enquanto |

**Nota:** `nao` = desqualificado. No Stitch: mostrar resultado mesmo assim, mas sem urgência no CTA.

### Step 8 — Ticket Médio
| Value (número) | Label | Subtexto |
|----------------|-------|---------|
| `800` | R$400 a R$1.200 | Procedimentos rápidos |
| `2500` | R$1.200 a R$3.500 | Procedimentos intermediários |
| `4000` | R$3.500 a R$6.000 | Procedimentos avançados |
| `6000` | Acima de R$6.000 | Procedimentos de alto valor |

---

## 5. Cálculo do Resultado (Modelo Gap)

```
esp = especialidadeData[especialidade]
convBase = esp.convBase + bonus_temAds  // ajuste por ter ads ou não
convSist = esp.convSist + bonus_temAds

pacientesHoje   = leads × convBase      // ex: 80 × 5% = 4
pacientesSist   = leads × convSist      // ex: 80 × 21% = 17
clientesNovos   = pacientesSist - pacientesHoje   // 13
perdaMensal     = clientesNovos × ticket           // 13 × 2500 = R$32.500
perda12m        = perdaMensal × 12                 // R$390.000
```

**Bonus por temAds:** sim=+1pp, pausado=0, nao=-1pp

**Exemplo MQL típico (harmonização, 120 leads, ticket R$2.500, ads=sim):**
- convBase = 6%, convSist = 22%
- clientesNovos = 120 × 0.16 = 19
- perdaMensal = 19 × 2.500 = **R$47.500/mês**
- perda12m = **R$570.000/ano** ✅

---

## 6. Resultado — Textos Personalizados por Especialidade

### Título
`"{name}, sua {clinica} está hemorragiando receita todo mês"`

### Subtítulo
`"Calculamos com base no volume de leads, ticket médio e tempo de resposta da sua {clinica}. O que você vai ver abaixo é o dinheiro real que sai pela porta enquanto seus concorrentes respondem mais rápido."`

### Número de Destaque (GRANDE)
- **Mensal:** `R$ XX.XXX/mês` — cor vermelha/alerta
- **Anual:** `R$ XXX.XXX nos próximos 12 meses se você não mudar nada` — cor gold

### Parágrafo Explicativo
`"Sua {clinica} recebe {leads} leads por mês. Sem um sistema, apenas {pacientesHoje} viram {termo}s (~{convBase}% de conversão). Com qualificação automatizada e resposta imediata, essa taxa sobe para ~{convSist}% — ou seja, {pacientesSist} {termo}s por mês. A diferença: {clientesNovos} {termo}s perdidos × {ticket} de ticket = {perdaMensal} que você deixa na mesa todo mês."`

### Goals / Objetivos (fixos, mas com {proc} e {clinica})
- "Responder todo lead de {proc} em minutos — 24h por dia"
- "Qualificar e priorizar quem está pronto para agendar {proc}"
- "Crescer o faturamento da {clinica} sem aumentar equipe"

### CTA Subtítulo
`"Em 20 minutos de call, você recebe um plano personalizado para sua {clinica} e tem a oportunidade de {urgenciaTexto} — com resultado em 3 semanas."`

**urgenciaTexto por valor:**
- `mes` → "implementar ainda este mês"
- `trimestre` → "implementar nos próximos meses"
- `pesquisando` → "entender suas opções com clareza"
- `nao` → "transformar sua operação quando estiver pronto"

---

## 7. Scoring MQL (Supabase form_score)

Score máximo: **140 pontos**

| Campo | Valor | Pontos |
|-------|-------|--------|
| especialidade | bucomaxilo | 30 |
| especialidade | odontologia_estetica | 25 |
| especialidade | estetica_avancada | 20 |
| especialidade | harmonizacao | 15 |
| especialidade | dermatologia | 15 |
| especialidade | spa_medico | 10 |
| temAds | sim | 40 |
| temAds | pausado | 20 |
| temAds | nao | 10 |
| equipe | team | 40 |
| equipe | 2mais | 30 |
| equipe | 1 | 15 |
| equipe | eu | 5 |
| urgencia | mes | 30 |
| urgencia | trimestre | 15 |
| urgencia | pesquisando | 5 |
| urgencia | nao | 0 |

**MQL perfeito:** bucomaxilo + sim + team + mes = **140 pts**  
**Threshold sugerido para SDR ligar:** ≥ 80 pts

---

## 8. Design System — Éclat Ivoire & Or v3.0

### Paleta
| Token | Hex | Uso |
|-------|-----|-----|
| `--bg` | `#faf7f2` | Fundo principal (ivoire quente) |
| `--bg-2` | `#f2ede4` | Superfície elevada (cards) |
| `--bg-3` | `#e8e2d6` | Card surface |
| `--gold` | `#a8882a` | Ouro 18k — cor principal |
| `--gold-lt` | `#c9a84c` | Ouro médio — hover/destaque |
| `--grad` | `135deg, #a8882a → #c9a84c` | Gradiente padrão |
| `--ink` | `#1a1410` | Texto principal |
| `--ink-2` | `#5a4e40` | Texto secundário |
| `--ink-3` | `#8a7e72` | Texto muted/captions |
| `--red` | `#d93050` | Alerta / perda |
| `--green` | `#0d8f64` | Sucesso |

### Tipografia
- **Display/Headlines:** Playfair Display 400/600 + italic
- **Interface:** Inter 300/400/500/600
- **Body:** 14-16px Inter 400
- **Captions/Labels:** 10-11px Inter 500, letter-spacing: 3px, uppercase

### Componentes Chave
- **Option card:** border 1px gold-20%, hover gold-52%, selected bg gold-6% + border gold
- **Button primário:** gradient gold, border-radius 3px
- **Badge:** border 1px gold-20%, color gold, uppercase, letter-spacing 3px
- **Progress bar:** 2px, gradient gold, top fixed
- **Step dots:** 6px círculos, done=gold-50%, active=gold+scale(1.3)

### Paleta por Especialidade (Gamificação Stitch)
| Especialidade | Cor secundária sugerida | Tom |
|---------------|------------------------|-----|
| Harmonização | Rose gold `#c4906e` | Feminino, sofisticado |
| Bucomaxilo | Azul profissional `#2d5986` | Clínico, confiança |
| Odontologia Estética | Branco pearl `#f8f5f0` | Limpeza, precisão |
| Estética Avançada | Violeta `#8b5cf6` | Tecnologia, inovação |
| Spa Médico | Verde sage `#6b8f71` | Bem-estar, tranquilidade |
| Dermatologia | Pêssego suave `#e8b4a0` | Pele, cuidado |

---

## 9. Arquitetura de Dados (Supabase)

### Tabela: `leads`
```sql
tenant_id       uuid
name            text
whatsapp        text
form_score      int        -- 0-140, threshold MQL = 80
form_answers    jsonb      -- {especialidade, temAds, leads, ticket, resposta, equipe, urgencia}
lgpd_consent    bool
lgpd_consent_at timestamptz
utm_source      text
utm_medium      text
utm_campaign    text
utm_content     text
utm_term        text
created_at      timestamptz
```

### form_answers exemplo (MQL perfeito)
```json
{
  "especialidade": "bucomaxilo",
  "temAds": "sim",
  "leads": 120,
  "ticket": 6000,
  "resposta": "4h",
  "equipe": "2mais",
  "urgencia": "mes"
}
```

---

## 10. Loading Screen (4 mensagens sequenciais)

1. "Analisando o volume de leads da sua clínica..."
2. "Calculando a taxa de conversão do seu segmento..."
3. "Mapeando o que custaria resolver isso com equipe humana..."
4. "Gerando seu diagnóstico personalizado..."

---

## 11. Sugestões para Gamificação no Stitch

1. **Transição por especialidade:** ao selecionar, animar a mudança de cor de fundo/tema
2. **Progress visual diferente por step:** barra + ícone temático da especialidade
3. **Micro-animações nas opções:** cards que "saltam" ao selecionar
4. **Contador regressivo no loading:** "Seu diagnóstico fica pronto em 4..." 
5. **Resultado dramático:** número de perda aparece contando de 0 até o valor final (counter animation)
6. **Comparação visual:** gráfico antes/depois da conversão (hoje X% vs com sistema Y%)
7. **Urgência no resultado:** "Você perdeu R$XX.XXX enquanto fazia este diagnóstico" (baseado no tempo gasto)
8. **Social proof contextual:** "47 clínicas de harmonização facial já usam o sistema"

---

## 12. Integração Pós-Resultado

- **CTA:** Agendar call via Calendly
- **Campo:** WhatsApp (obrigatório para salvar lead)
- **Redirect:** Abre Calendly em nova aba após salvar
- **LGPD:** Consentimento implícito ao submeter

---
*PRD gerado em 2026-04-09 · EstéticaFlow / escalacomia.com.br*

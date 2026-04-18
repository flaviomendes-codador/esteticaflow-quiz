# Spec — Apresentação de Vendas EstéticaFlow IA

**Data:** 2026-04-17  
**Status:** Aprovado para execução  
**Executor:** Codex  

---

## Objetivo

Criar um arquivo `apresentacao.html` na raiz do projeto com dois decks de slides embutidos:

1. **Deck Principal** — ancoragem de preço + oferta final
2. **Deck Downsell** — assistente de vendas personalizada

---

## Stack

- HTML puro — sem framework
- CSS inline / `<style>` tag — sem dependências externas
- JavaScript vanilla para navegação entre slides
- Design System ÉCLAT — IVOIRE & OR v3.0 (definido abaixo)
- Navegação: setas do teclado (← →) + botões na tela
- Fullscreen por padrão (viewport 100vw × 100vh por slide)

---

## Design System ÉCLAT — IVOIRE & OR v3.0

```
Fundo:           #faf7f2   (ivoire quente)
Fundo secundário:#f0ebe2
Card fundo:      #ffffff
Ouro principal:  #a8882a
Ouro médio:      #c9a84c
Gradiente ouro:  135deg, #a8882a → #c9a84c
Tinta principal: #1a1410
Tinta secundária:#5a4f42
Sucesso/verde:   #2d6a4f

Display: Playfair Display (Google Fonts — 400 / 600 / italic)
Interface: Inter (Google Fonts — 300 / 400 / 500 / 600 / 700)

Cards: border-radius 12px
Botões: border-radius 3px
Sombra card: 0 2px 16px rgba(168,136,42,0.10)
```

---

## Deck 1 — Principal (9 slides)

### Slide 1 — Capa
- Fundo gradiente ouro (135deg, #a8882a → #c9a84c)
- Logo texto: **EstéticaFlow IA**  (Playfair Display, branco, 48px)
- Subtítulo: "Sistema de Implementação — Proposta Personalizada" (Inter 300, branco, 20px)
- Rodapé: nome da clínica (deixar em branco — campo editável) ou só "escalacomia.com.br"

### Slide 2 — O que você recebe
- Título: "5 módulos. Uma operação completa." (Playfair Display, ouro, 36px)
- Lista dos 5 módulos com ícone emoji à esquerda:
  - 🤖 Atendente IA no WhatsApp — responde em 5 segundos, 24h/7d
  - 📊 Lead Scoring automático — classifica quem está pronto para comprar
  - 🔁 Follow-up automático — reativa leads frios sem esforço manual
  - 📈 Dashboard em tempo real — leads, conversão, receita por canal
  - 📅 Confirmações + anti-no-show — agenda cheia, sem remarcações manuais
- Fundo ivoire, texto tinta principal

### Slides 3 a 7 — Um módulo por slide (ancoragem individual)

Estrutura de cada slide:
- Fundo: branco com borda lateral esquerda 4px ouro
- Tag no topo: "MÓDULO 01 de 05" (pequeno, ouro, Inter 500)
- Nome do módulo (Playfair Display, 32px, tinta)
- Descrição de 2 linhas (Inter 400, 16px, tinta secundária)
- Box de preço individual no canto inferior direito:
  - Label: "Se contratado separado"
  - Valor em destaque (Inter 700, 28px, tinta)

**Módulo 01 — Atendente IA WhatsApp**
- Desc: "Responde leads automaticamente em segundos. Qualifica, agenda e nunca perde um contato — 24 horas por dia, 7 dias por semana."
- Preço: **R$ 2.997**

**Módulo 02 — Lead Scoring**
- Desc: "Classifica cada lead por probabilidade de fechamento antes do contato humano. Seu time foca só em quem já está pronto."
- Preço: **R$ 1.497**

**Módulo 03 — Follow-up Automático**
- Desc: "Sequência de reativação para leads frios. Recupera contatos que pararam de responder — sem esforço manual do atendimento."
- Preço: **R$ 1.997**

**Módulo 04 — Dashboard em Tempo Real**
- Desc: "Visão completa de leads por canal, taxa de conversão e receita estimada. Decisão de onde investir baseada em dados, não intuição."
- Preço: **R$ 1.497**

**Módulo 05 — Confirmações + Anti-No-Show**
- Desc: "Confirmações automáticas de consulta e reposição de cancelamentos. Redução de no-show de até 68% — agenda sempre cheia."
- Preço: **R$ 997**

### Slide 8 — Ancoragem Total

Layout: dois painéis lado a lado

**Painel esquerdo — Custo separado (riscado/cinza):**
- Título: "Se você contratasse separado"
- Lista:
  - Atendente IA WhatsApp ........... R$ 2.997
  - Lead Scoring ..................... R$ 1.497
  - Follow-up automático ............ R$ 1.997
  - Dashboard em tempo real ......... R$ 1.497
  - Confirmações + anti-no-show ..... R$   997
  - Setup e configuração personalizada R$ 2.500
  - **Total: R$ 11.485** (tachado, cinza)

**Painel direito — Oferta (dourado, destacado):**
- Badge: "PACOTE COMPLETO"
- Título: "EstéticaFlow IA"
- Valor: **R$ 10.000** (grande, ouro)
- Ou: "Fechando hoje na call:"
- Valor parcelado: **12x de R$ 583**

### Slide 9 — Próximo passo

- Fundo gradiente ouro
- Título: "Pronto para começar?" (Playfair Display, branco, 40px)
- Subtítulo: "Implementação completa em 10 a 15 dias. Você só aprova." (Inter 300, branco, 20px)
- Box branco centralizado com:
  - "Fechando hoje: 12x de R$ 583"
  - Ou "À vista: R$ 10.000"
- Rodapé: "escalacomia.com.br"

---

## Deck 2 — Downsell (3 slides)

Separado do deck 1. Um botão "Ver Downsell" no último slide do deck 1 ativa o deck 2.
Um botão "Voltar ao principal" no primeiro slide do deck 2.

### Slide D1 — Capa Downsell
- Fundo: ivoire quente
- Tag: "PARA QUEM QUER COMEÇAR COM O ESSENCIAL" (ouro, pequeno, maiúsculas)
- Título: "Assistente de Vendas Personalizada" (Playfair Display, 38px, tinta)
- Subtítulo: "O primeiro passo para uma operação que não perde leads." (Inter 300, 18px)

### Slide D2 — O que inclui
- Título: "O que está incluído"
- Lista com ícones:
  - 🤖 Atendente IA treinada com o perfil da sua clínica
  - ⚡ Resposta automática em segundos via WhatsApp
  - 📋 Qualificação e triagem do lead antes do atendimento humano
  - 🔧 Setup e configuração completa (você só aprova)
  - 📞 Suporte nos primeiros 30 dias de operação

### Slide D3 — Preço Downsell
- Fundo branco, borda lateral esquerda ouro
- Título: "Assistente de Vendas Personalizada"
- Valor grande: **12x de R$ 250** (Inter 700, 48px, ouro)
- Sub: "Total: R$ 3.000 — sem juros"
- Botão CTA: "Quero começar com a Assistente" (gradiente ouro, texto branco)

---

## Navegação

- Setas ← → do teclado navegam entre slides do deck ativo
- Indicador de progresso: "slide X de Y" no canto inferior direito
- Botão "Ver Downsell" visível apenas no slide 9 (deck 1)
- Botão "Voltar ao principal" visível apenas no slide D1 (deck 2)
- Transição: fade suave 300ms

---

## Output esperado

Arquivo único: `apresentacao.html` na raiz do projeto.
Deve funcionar offline (sem servidor), apenas abrindo o arquivo no browser.
Google Fonts carregadas via CDN (requer internet apenas para carregar fontes — fallback: system-ui).

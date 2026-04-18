# Spec — Proposta Interativa Elisa

**Data:** 2026-04-17  
**Status:** Aprovado para execução  
**Executor:** Codex  

---

## Objetivo

Criar `proposta-elisa.html` na raiz do projeto. Arquivo único, abre direto no browser, para ser enviado à lead Elisa via WhatsApp. Ela abre e vê a proposta completa sozinha — sem servidor, sem login.

---

## Stack

- HTML puro — sem framework
- CSS inline / `<style>` tag — sem dependências externas além de Google Fonts
- JavaScript vanilla — apenas para micro-interações
- Design System ÉCLAT — IVOIRE & OR v3.0 (definido abaixo)
- Funciona offline após o primeiro carregamento das fontes

---

## Design System ÉCLAT — IVOIRE & OR v3.0

```
Fundo página:    #faf7f2   (ivoire quente)
Fundo card:      #ffffff
Fundo destaque:  #f0ebe2
Ouro principal:  #a8882a
Ouro médio:      #c9a84c
Gradiente ouro:  135deg, #a8882a → #c9a84c
Tinta principal: #1a1410
Tinta secundária:#5a4f42
Tinta fraca:     #9a8f82
Verde suave:     #2d6a4f

Display: Playfair Display (Google Fonts — 400 / 600 / italic)
Interface: Inter (Google Fonts — 300 / 400 / 500 / 600 / 700)

Cards: border-radius 12px, box-shadow: 0 2px 16px rgba(168,136,42,0.08)
Botões: border-radius 3px
Separadores: 1px solid rgba(168,136,42,0.15)
```

---

## Layout — Uma única página com scroll

Largura máxima: 680px, centralizada. Padding lateral: 24px.

---

## Seção 1 — Hero (topo)

Fundo: gradiente ouro (135deg, #a8882a → #c9a84c).  
Padding: 56px 40px.  
Texto centralizado.

- Tag pequena acima: `PROPOSTA EXCLUSIVA` (Inter 500, 11px, branco 70%, letter-spacing 2px, maiúsculas)
- Título: `Elisa, aqui está` (Playfair Display italic, 18px, branco 80%)
- Título principal: `tudo que preparamos` (Playfair Display 600, 42px, branco)
- Subtítulo: `para transformar seu Instagram em uma máquina de agendamentos.` (Inter 300, 17px, branco 85%, max-width 480px)

---

## Seção 2 — A Oferta Principal

Fundo: branco. Card com borda lateral esquerda 3px gradiente ouro.  
Padding: 32px.

- Tag: `O QUE VOCÊ LEVA` (Inter 500, 11px, ouro, maiúsculas, letter-spacing 1.5px)
- Título: `Assistente de Vendas Personalizada` (Playfair Display 600, 28px, tinta)
- Descrição: `Uma IA treinada com o perfil da sua clínica. Responde leads no WhatsApp em segundos, qualifica, agenda — 24 horas por dia, sem você precisar estar online.` (Inter 400, 15px, tinta secundária, line-height 1.7)

---

## Seção 3 — Bônus (a parte interativa)

Título da seção: `+ Bônus exclusivos` (Playfair Display italic, 24px, tinta, centralizado)  
Subtítulo: `incluídos nesta proposta` (Inter 300, 14px, tinta fraca, centralizado)

**4 cards de bônus** em layout vertical (um por linha, largura total).

Cada card:
- Fundo: #ffffff
- Borda: 1px solid rgba(168,136,42,0.15)
- Border-radius: 12px
- Padding: 24px 28px
- Ao hover: box-shadow 0 4px 24px rgba(168,136,42,0.14), translateY(-2px), transição 200ms
- Ícone à esquerda (emoji grande, 28px)
- Título do bônus (Inter 600, 16px, tinta)
- Descrição (Inter 400, 14px, tinta secundária, line-height 1.6)
- Tag `BÔNUS` no canto superior direito (Inter 600, 10px, ouro, background ouro 10%, border-radius 4px, padding 2px 8px)

**Bônus 1**
- Ícone: 📈
- Título: `Assessoria de Marketing e Vendas`
- Desc: `Estratégia personalizada para seu negócio. Vamos juntos entender o que está funcionando e o que está travando seus resultados.`

**Bônus 2**
- Ícone: ⚡
- Título: `Como usar o botão Impulsionar do Instagram`
- Desc: `Você vai aprender a investir de forma inteligente no Instagram — sem desperdiçar dinheiro em post que não converte.`

**Bônus 3**
- Ícone: 🎯
- Título: `Como postar conteúdos que atraem pacientes`
- Desc: `Conteúdo que gera autoridade, atrai o perfil certo de cliente e deixa sua agenda cheia — sem precisar virar criadora de conteúdo.`

**Bônus 4**
- Ícone: 💛
- Título: `Como fidelizar e atender clientes do Instagram`
- Desc: `Do primeiro contato ao retorno. Você vai ter um processo claro para transformar curtidas em pacientes fiéis.`

---

## Seção 4 — Resumo do Investimento

Fundo: #f0ebe2 (ivoire médio). Border-radius 16px. Padding 40px.  
Centralizado.

- Tag: `SEU INVESTIMENTO` (Inter 500, 11px, ouro, maiúsculas, letter-spacing 1.5px)
- Valor principal: `12x de R$ 250` (Playfair Display 600, 52px, ouro principal)
- Subtexto: `sem juros · total R$ 3.000` (Inter 400, 14px, tinta fraca)
- Separador 1px
- Lista inline do que está incluído (texto pequeno, tinta secundária):
  `Assistente de Vendas IA  ·  Assessoria de Marketing  ·  Impulsionar  ·  Conteúdo  ·  Fidelização`

---

## Seção 5 — Mensagem final

Fundo: branco. Padding 40px. Texto centralizado.

- Citação em Playfair Display italic, 20px, tinta secundária, max-width 480px:
  `"Isso foi pensado especialmente para o momento em que você está — e para onde você quer chegar."`
- Assinatura abaixo: `EstéticaFlow IA` (Inter 600, 14px, ouro)

---

## Seção 6 — Rodapé

Fundo: #1a1410 (tinta).  
Centralizado. Padding 24px.

- Texto: `escalacomia.com.br` (Inter 400, 12px, branco 40%)

---

## Animação de entrada

Ao carregar a página, cada seção entra com fade-in + translateY(16px → 0) com delay escalonado:
- Hero: imediato
- Seção 2: delay 150ms
- Seção 3: delay 300ms
- Cada card de bônus: delay adicional de 80ms (0ms, 80ms, 160ms, 240ms) após a seção aparecer
- Seção 4: delay 500ms
- Seção 5: delay 600ms

Usar `IntersectionObserver` para disparar a animação quando o elemento entra na viewport (não tudo de uma vez no load).

---

## Output esperado

Arquivo único: `proposta-elisa.html` na raiz do projeto `esteticaflow-quiz`.  
Deve funcionar abrindo direto no browser (file://). Fontes do Google via CDN.

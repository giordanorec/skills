# Books & guides — biblioteca canônica de web design

> Quando o agente precisar fundamentar uma decisão, citar aqui. Cada item tem o conceito-chave.

## Tier S — leitura essencial

### Refactoring UI — Adam Wathan & Steve Schoger
- **Conceito-chave**: hierarquia visual via tipografia e cor, não decoração
- **Princípios trazidos**: contraste maior do que parece intuitivo, white space é design, cor em camadas (não uma única cor por papel)
- **Aplicar quando**: qualquer projeto de UI — é o fundamento
- **URL**: refactoringui.com

### Practical Typography — Matthew Butterick
- **Conceito-chave**: tipografia é 90% da legibilidade
- **Princípios**: 1 ponto a mais é o caminho mais rápido pra texto pior; line-height proporcional ao tamanho; line-length 45-90 caracteres
- **Aplicar quando**: definir tipografia em qualquer projeto
- **URL**: practicaltypography.com (free online)

### Atomic Design — Brad Frost
- **Conceito-chave**: atoms → molecules → organisms → templates → pages
- **Princípios**: reutilização via abstração ascendente, design system como código
- **Aplicar quando**: projetos com componentes reusáveis (componentes React, design tokens)
- **URL**: atomicdesign.bradfrost.com (free)

### Inclusive Design Patterns — Heydon Pickering (Smashing 2016)
- **Conceito-chave**: design pra todos, não só "normal users"
- **Princípios**: keyboard-first, screen-reader-tested, contrast actual, error states humanos
- **Aplicar quando**: qualquer projeto público
- **Refresh moderno**: "Inclusive Design for Accessibility" (Packt 2025)

## Tier A — Recommended

### Don't Make Me Think — Steve Krug
- **Conceito-chave**: UX é tornar o invisível invisível
- **Princípio**: usabilidade é uma decisão a menos pro usuário
- **Aplicar quando**: review de IA / navigation / form

### Design for Hackers — David Kadavy
- **Conceito-chave**: princípios de design pra quem vem de eng
- **Aplicar quando**: dev que precisa fazer design sozinho

### The Elements of Typographic Style — Robert Bringhurst
- **Conceito-chave**: bíblia da tipografia, web translation de Butterick
- **Aplicar quando**: tipografia editorial / longreads

### Designing for Emotion — Aarron Walter
- **Conceito-chave**: hierarquia de needs (Maslow) aplicada a produto
- **Princípio**: depois de funcional / confiável / utilizável vem **prazeroso**
- **Aplicar quando**: pensar copy, micro-interactions, easter eggs

### Smashing Book series — Smashing Magazine
- **Vol 6**: "Smashing Book 6 — New Frontiers in Web Design"
- **Aplicar quando**: deep-dive em técnica específica

### The Design of Everyday Things — Don Norman
- **Conceito-chave**: affordance, signifier, mapping, feedback
- **Aplicar quando**: review de affordance / signifiers em UI

## Tier B — Guias online (não livros)

### Vercel Engineering — "design.guide"
- vercel.com/design — princípios usados pelo Vercel
- Útil pra estética "developer tool"

### Apple Human Interface Guidelines (HIG)
- developer.apple.com/design/human-interface-guidelines
- Não é "livro" mas é referência canônica
- Útil pra Apple-style sites + apps

### Material Design 3
- m3.material.io
- Útil quando o cliente quer Android-feel

### Inclusive Components — Heydon Pickering
- inclusive-components.design
- Componentes web acessíveis com código

### web.dev (Google)
- Guides de Core Web Vitals, performance, acessibilidade
- Sempre atualizado

### MDN Web Docs
- developer.mozilla.org
- Reference de CSS / HTML / APIs novas (view-timeline, scroll-driven animations)

### A List Apart
- alistapart.com
- Artigos de profundidade sobre web design moderno

## Tier C — Newsletters e fontes vivas

- **CSS-Tricks** — guides CSS, ainda relevante
- **Smashing Magazine newsletter** — semanal, padrões UX/UI
- **Sidebar.io** — daily, links curados
- **Web Designer News** — diário, agregador
- **The Defensive CSS** — newsletter focada em robust CSS
- **Codrops** — tutoriais avançados de motion/WebGL
- **Tympanus** — Codrops parent, deep dives

## Princípios destilados que aparecem em TODOS

1. **Contraste maior que parece** — quando você acha que tá bom, aumente mais 20%
2. **Whitespace é o luxo** — adicionar espaço é quase sempre a melhor mudança
3. **Hierarquia via tamanho + peso + cor**, não via decoração
4. **Tipografia em 2 famílias max** — uma display, uma body
5. **Cor com função** — cada cor tem um papel (CTA, accent, semantic, neutral)
6. **Bordas suaves OU duras, escolhe um vibe** — 0px ou 8-12px ou 24px+, não 2-4px (parece bug)
7. **Sombras em camadas** — 2-3 sombras pequenas > 1 sombra grande
8. **Motion serve narrativa** — animação sem propósito é AI slop
9. **Acessibilidade no início** — refit depois é mais caro e pior
10. **Performance é UX** — site lento é site ruim independente de quão bonito

## Como o skill cita esses livros

Em `agents/redteam-craft.md` os critics ATACAM violações desses princípios usando a fonte:

> "Você violou a regra de Refactoring UI cap. 4 (contraste em camadas): seu texto secundário tá em #999 sobre #f5f5f5 — contraste 2.8:1, abaixo do mínimo 4.5:1 pra body text. Suba o foreground pra #5a5a5a (5.2:1) ou desça o background pra #ffffff."

Isso dá ao agente vocabulário fundamentado, não opinião.

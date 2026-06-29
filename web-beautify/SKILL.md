---
name: web-beautify
description: Embeleza websites a nível top mundial (Apple/Spotify/Nike-tier). Pipeline em 5 fases — Context → Plan (DESIGN.md âncora real) → Implement (recipes prontas: bento, kinetic, mesh, marquee, glass) → Red Team multi-perspectiva (craft, a11y, perf, originality, UX) → Iterate até aprovado. Estende a `anthropic/frontend-design` puxando DESIGN.md de 73+ brands reais e adicionando red team com 5 críticos. Use when invoked for landing pages, marketing sites, portfolios, SaaS dashboards, brand sites, ou refactor visual de qualquer site.
---

# web-beautify — pipeline pra site nivel top mundial

## Quando essa skill é chamada

Invoque quando o usuário pede:
- "deixa esse site mais bonito"
- "faz uma landing page nivel Apple/Stripe/Vercel"
- "esse site tá com cara de IA, melhora"
- "preciso de hero, sections, footer pra <projeto>"
- "embeleza"
- "design um site pra <X>"
- "faz um redesign"

Não invoque pra:
- bug fix de logic (não-visual)
- backend / database
- documentação técnica
- email templates (use email-design skill se existir)

## Por que essa skill existe

O default da IA é **AI slop**: hero centralizado + 2 botões pill + grid 3-col de cards + paleta indigo-roxa + Inter. Funcional, mas indistinguível de outros 1000 sites IA. Sites premium (Apple, Stripe, Linear) não saem assim.

A `anthropics/frontend-design` (277k installs) ataca isso mas:
- Não puxa de DESIGN.md REAIS — inventa rules internas
- Não tem red team multi-perspectiva
- Não tem recipes Awwwards-tier prontas
- Não recomenda stack baseado em contexto

A `web-beautify` preenche essas lacunas.

## O pipeline (5 fases)

### Fase 1 — Context (entender ANTES de mexer)

Invoque o subagente `context-analyzer` (em `agents/context-analyzer.md`).

Ele extrai em max 6 perguntas:
- Audiência primária (dev / consumer / b2b / creative)
- Brand identity (existe? tokens onde?)
- Stack constraint (Next? Astro? Vite? livre?)
- Wow target tier (1-4)
- Deadline
- Deploy target

Output: `state/context.json` com a recomendação completa (DESIGN.md âncora, recipes, stack, studio refs).

**Por que isso primeiro**: sem contexto, todo o resto é chute. AI slop nasce de "não perguntar".

### Fase 2 — Plan (ancorar em design system real)

Baseado no `context.json`:

1. **Puxe o DESIGN.md âncora** do catálogo (referenciar `references/02_design_md_catalog.md`):
   ```bash
   curl -sL https://raw.githubusercontent.com/VoltAgent/awesome-design-md/main/design-md/<slug>/DESIGN.md > .design/anchor.DESIGN.md
   ```

2. **Extraia os tokens** do YAML front-matter pro projeto (cores, typography, rounded, spacing).

3. **Liste as recipes** que vai aplicar (de `recipes/`):
   - Sempre considere: glassmorphism-nav, mesh-gradient (hero), kinetic-typography (statement)
   - Tier 2+: + bento-grid, marquee
   - Tier 3+: + scroll-snap-story, custom-cursor
   - Tier 4: + webgl-hero, page-transitions

4. **Selecione o stack** (referenciar `references/04_stacks.md`):
   - Vanilla CSS sempre que possível (zero deps)
   - Motion (ex-Framer Motion) pra React UI
   - GSAP só se precisa timeline complexa ou SplitText
   - Lenis pra smooth scroll global (Tier 2+)
   - Three.js + R3F só pra WebGL hero (Tier 4)

5. **Defina o "wow plan"** (princípio xiaopu/web-design):
   - Hero: kinetic typography OU mesh gradient animado
   - 1º scroll: scroll reveal forte OU pin moment
   - List/Bento: hover ativo com depth
   - 1 easter egg sutil (custom cursor opcional, micro-interaction inesperada)

Documente em `state/plan.md`. Mostre ao usuário pra confirm antes de implementar (rápido — não devolve menu, propõe).

### Fase 3 — Implement (aplicar recipes + escrever código)

1. **Load apenas as recipes selecionadas** (Read tool nos arquivos `recipes/<nome>.md`)
2. **Aplicar tokens da DESIGN.md** como CSS variables no `:root` (NUNCA hex hardcoded)
3. **Construir cada section** seguindo a recipe — copy-adapt, não copy-paste literal
4. **Conteúdo é content** — copy precisa ser específica, não AI marketing-speak
5. **Test em mobile-first** (tudo deve funcionar em 320px de largura)
6. **Verificar `prefers-reduced-motion`** em toda animation

### Fase 4 — Red Team (5 perspectivas atacam)

Em PARALELO, invoque os 5 críticos:

| Crítico | Foco | Arquivo |
|---|---|---|
| `redteam-craft` | tipografia, cor, espaçamento, hierarquia | `agents/redteam-craft.md` |
| `redteam-accessibility` | WCAG 2.2 AA, keyboard, SR, reduced motion | `agents/redteam-accessibility.md` |
| `redteam-performance` | LCP/CLS/INP, bundle, animation cost | `agents/redteam-performance.md` |
| `redteam-originality` | anti-AI-slop, distintividade | `agents/redteam-originality.md` |
| `redteam-ux` | affordance, error states, mobile, micro | `agents/redteam-ux.md` |

Cada um devolve JSON em `state/redteam/<critic>.json` com `verdict: approved | needs_revision | rejected` + lista de violations específicas.

**Política de severidade do orchestrator**:
- TODOS os 5 precisam dar `approved` pra ship.
- Qualquer `rejected` = bloqueia.
- 2+ `needs_revision` = iterate.

### Fase 5 — Iterate (loop até aprovação)

Se algum crítico não aprovou:

1. Compile a lista de violations de TODOS os 5
2. Priorize por severidade: critical → high → medium
3. Implemente os fixes (cite o crítico que pediu)
4. **Re-invoque APENAS os críticos que tinham violations** (não roda tudo de novo)
5. Loop até 5/5 aprovados

**Limite**: 3 iterações max. Se não converge, faça step back — volte pra Fase 2 e replan.

## Hard rules (não-negociáveis)

### Anti-AI-slop hardcoded

NUNCA:
- ❌ Use Inter, Roboto, Arial, Helvetica, Space Grotesk SEM justificativa explícita da DESIGN.md âncora
- ❌ Use `bg-gradient-to-r from-purple-500 to-pink-500` (a paleta default IA)
- ❌ Faça hero "H1 + p + 2 botões pill centralizado" sem variação
- ❌ Faça grid 3-col de feature cards uniformes
- ❌ Use cor que não vem de var/token (zero hex hardcoded em CSS de produção)
- ❌ Use emoji 🚀 ✨ 💡 🔥 decorativamente em headers
- ❌ Use copy genérica tipo "The future of X is here", "Empower your Y"

### Performance budgets (por tier)

| Tier | LCP mobile | JS gzip | CSS gzip |
|---|---|---|---|
| 1 simples | < 2.0s | < 100KB | < 50KB |
| 2 SaaS | < 2.5s | < 200KB | < 80KB |
| 3 campaign | < 3.0s | < 350KB | < 100KB |
| 4 awwwards | < 4.0s | < 500KB + WebGL | < 120KB |

### Acessibilidade

- WCAG 2.2 AA é o MÍNIMO sempre
- Contraste 4.5:1 texto normal, 3:1 large/UI
- Focus visible em TUDO
- `prefers-reduced-motion: reduce` desliga animations decorativas
- Keyboard navigation completa

## Onde olhar pra detalhes

Detalhes ficam em `references/` (carregar on-demand, não tudo de uma vez):

- `01_existing_skills.md` — landscape competitivo (skills similares)
- `02_design_md_catalog.md` — 73+ DESIGN.md catalogadas, heurística pra escolher âncora
- `03_differentiation_techniques.md` — patterns Awwwards-tier, anti-patterns AI slop
- `04_stacks.md` — GSAP / Motion / Lenis / Three / Rive — quando usar cada
- `05_studios_references.md` — studios pra benchmarkar (Locomotive, Active Theory, etc)
- `06_books_guides.md` — Refactoring UI, Practical Typography, etc — pra fundamentar decisões
- `07_meta_gaps.md` — o que essa v1 não cobre (color science, motion principles, etc — roadmap)

Recipes ficam em `recipes/`:

- `bento-grid.md`
- `kinetic-typography.md`
- `mesh-gradient.md`
- `marquee.md`
- `glassmorphism-nav.md`
- (futuras: scroll-snap-story, custom-cursor, webgl-hero, page-transitions)

Agentes em `agents/`:

- `context-analyzer.md` — Fase 1 (entender)
- `redteam-craft.md` — Fase 4 (crítico tipografia/cor/spacing/hierarquia)
- `redteam-accessibility.md` — Fase 4 (crítico WCAG/keyboard/SR)
- `redteam-performance.md` — Fase 4 (crítico LCP/CLS/INP/bundle)
- `redteam-originality.md` — Fase 4 (crítico anti-AI-slop)
- `redteam-ux.md` — Fase 4 (crítico affordance/feedback/mobile)

## Output esperado

Ao terminar, o usuário recebe:

1. **Site/landing rodando** (HTML + CSS + JS mínimo)
2. **`.design/anchor.DESIGN.md`** — o design system real ancorado
3. **`state/plan.md`** — decisões registradas (por que essa âncora, esses recipes, esse stack)
4. **`state/redteam/`** — verdicts dos 5 críticos (transparência total)
5. **Lista de TODOs futuros** — o que ficou pra v2 (ex: i18n, WebGL hero, etc)

## Anti-patterns no uso da skill

❌ Pular Fase 1 ("já sei o contexto") — assumption é mãe do AI slop
❌ Não puxar DESIGN.md real — inventar "estilo Apple" sem fonte
❌ Rodar críticos sequencial — eles são INDEPENDENTES, rode em paralelo
❌ Ignorar `needs_revision` — "tá ok assim" é como AI slop ganha
❌ Loop infinito sem replan — se 3 iter não convergem, volta pra Fase 2
❌ Aplicar recipes sem entender — recipes são templates, precisam adapt ao contexto

## Updates / changelog

- v0.1 (2026-06-28): initial — pipeline 5-phase, 6 references, 5 recipes, 6 agents

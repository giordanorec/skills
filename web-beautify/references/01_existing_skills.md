# Skills existentes que fazem sites bonitos (estado da arte, jun-2026)

> Mapa pra evitar reinventar e pra saber o que diferencia a `web-beautify` do resto.

## Tier 1 — As referências canônicas

### `anthropics/frontend-design` ⭐ THE benchmark
- **Stats**: 277k+ instalações, atualizada fev-2026, mantida por Vercel Engineering
- **Pitch oficial**: "frontend design and UI/UX development tools"
- **O que ela faz bem**:
  - 2-pass: plan compacto → critique → build
  - Lista negra explícita de fontes "overused by AI": **Inter, Roboto, Arial, Space Grotesk**
  - Ban a gradientes roxos genéricos
  - Lista 3 defaults a evitar:
    1. cream + serif + terracotta
    2. near-black + neon accent
    3. newspaper-style
  - Pega o conceito de "ground in subject matter" — design vem do domínio do produto
  - "Typography carries the personality"
  - Numbered markers (01/02/03) só se a sequência for real
- **O que ela NÃO tem (gap)**:
  - Não puxa de DESIGN.md reais (cataloga rules in-house)
  - Não tem red team multi-perspectiva
  - Não tem recipes prontas (kinetic, bento, marquee, etc)
  - Não recomenda stack (GSAP/Motion/Lenis/Three)
  - Não tem meta-agente "qual abordagem pra qual contexto"
- **URL**: `anthropics/claude-code/plugins/frontend-design/skills/frontend-design`

### `openai/frontend-skill`
- **Pitch**: "create visually strong landing pages, websites, and app UIs"
- **Estado**: oficial OpenAI, compatível com Codex
- **Gap**: opaca, sem ênfase em diferenciação anti-default

### `microsoft/frontend-design-review`
- **Pitch**: "review and create distinctive frontend interfaces"
- **Estado**: oficial Microsoft
- **Gap**: viés Fluent Design (Segoe, Acrylic), pouco fora desse mundo

### `KAOPU-XiaoPu/web-design`
- **Pitch**: "spec first, code second" — chinesa
- **Pontos fortes**:
  - 3 fases: collect → DESIGN.md → code
  - 9 seções obrigatórias no DESIGN.md (visual theme, color, typography, components com todos states, layout, depth, animation, guardrails, responsive)
  - 3 tiers de motion (L1/L2/L3)
  - Guardrails de performance: max 1 WebGL, `backdrop-filter` ≤14px, ≤2 pin-scrub
  - "Quality floor" como hard line (zero hex hardcoding, todos os states, etc)
  - "Wow moments" em 3 posições fixas (Hero, primeiro scroll, list view) + 1 detalhe sutil
- **Gap**: documentação só em chinês, sem red team

## Tier 2 — Skills adjacentes (não competem direto, complementam)

| Skill | O que faz |
|---|---|
| `anthropics/canvas-design` | PNG/PDF design — não-web |
| `anthropics/web-artifacts-builder` | claude.ai artifacts com React/Tailwind |
| `anthropics/theme-factory` | Aplicar/gerar temas em artifacts |
| `anthropics/brand-guidelines` | Aplicar identidade Anthropic |
| `figma/figma-generate-design` | Build screens via Figma DS |
| `figma/figma-implement-design` | Figma → código 1:1 |
| `google-labs-code/shadcn-ui` | shadcn/ui especificamente |
| `vercel-labs/next-best-practices` | Next.js patterns |
| `coreyhaines31/page-cro` | CRO em landing pages |
| `coreyhaines31/copywriting` | Copy persuasiva |

## Tier 3 — Landing page skills (small)

- `inferen-sh/landing-page-design` — SaaS conversion-focused
- `landing-page-designer` (marketplaces) — "Vibe Discovery" workflow
- `bear2u/my-skills/landing-page-guide-v2` — Next.js 14 + ShadCN + TS
- `rampstackco/claude-skills/landing-page-copy` — só copy

## Marketplaces / agregadores onde caçar mais

- **VoltAgent/awesome-agent-skills** — 1424+ skills (cross-tool: Claude, Codex, Gemini, Cursor)
- **anthropics/skills** — repo oficial
- **rohitg00/awesome-claude-code-toolkit** — 35 curated skills, 135 agents
- **travisvn/awesome-claude-skills**
- **BehiSecc/awesome-claude-skills**
- **ComposioHQ/awesome-claude-skills**
- **alirezarezvani/claude-skills** — 337 skills
- **awesomeskill.ai** — marketplace web
- **claudskills.com** — marketplace web
- **mcpmarket.com** — marketplace web
- **crossaitools.com** — agregador

## Diferenciação da `web-beautify`

Nenhum dos skills acima junta tudo isso:

1. **Cataloga 73+ DESIGN.md reais** de top brands (getdesign.md / VoltAgent/awesome-design-md) e puxa o relevante pra ancorar o projeto em vocabulário visual REAL — não "estilo Apple genérico", e sim **o DESIGN.md do apple.com puxado integralmente**.
2. **Red team multi-perspectiva** com 5 críticos (accessibility / performance / originality / craft / UX) e loop iterativo.
3. **Meta-agente context-aware** que pergunta as 4 coisas certas (audiência, brand, stack constraint, deadline) e recomenda direção.
4. **Recipes Awwwards-tier** prontas pra drop-in: bento ativo, kinetic typography, mesh gradient, marquee, custom cursor, scroll-snap chapters, WebGL hero (com performance budget).
5. **Stack picker** com regras de quando usar GSAP vs Motion vs Lenis vs Three vs Rive vs CSS view-timeline.
6. **Anti-AI-slop hardcoded** — extende a lista da Anthropic com mais defaults a banir (Inter quando aparece em tudo, "hero com gradiente + 3 cards + footer 3-col", etc).

# Meta-planning — gaps que os 6 passos anteriores deixaram

> O que mais precisa estar na skill pra ela ser completa, além de "skills + DESIGN.md + técnicas + stacks + studios + livros".

## Gaps identificados

### 1. Color science aplicada
Os livros falam de contraste mas não de:
- **OKLCH color space** (baseline 2024+) — mais perceptualmente uniforme que HSL
- **Color contrast APCA** (substituto moderno do WCAG 4.5:1) — mais preciso pra fonts variáveis
- **Color harmonies**: split-complementary, triadic, analogous — quando aplicar cada
- **Chromatic Vision Simulator** — testar pra deuteranopia/protanopia/tritanopia

→ Adicionar mini-ref em `references/08_color_science.md` (pendente, road-mappable)

### 2. Motion design principles
GSAP/Motion são stacks, mas os PRINCÍPIOS de motion não estão cobertos:
- **12 principles of animation** (Disney 1981) aplicados a UI: squash & stretch, anticipation, ease in/out, follow-through, secondary action
- **Easing curves específicas**:
  - `cubic-bezier(.6,.05,.3,1)` = "premium" feel (acelera lento, freia firme)
  - `cubic-bezier(.34,1.56,.64,1)` = "playful bounce"
  - `cubic-bezier(.2,.8,.2,1)` = "Material standard"
  - Linear = quase NUNCA usar pra movimento real (só pra loops infinitos)
- **Duration scale**: 100ms (instant feedback) / 200ms (hover) / 300ms (transition) / 600ms (page) / 1000ms+ (cinematic)
- **Frame budget**: 16.67ms por frame pra 60fps; 8.33ms pra 120fps

→ Adicionar `references/09_motion_principles.md`

### 3. Performance budgets concretos
Os princípios mencionam "LCP < 2.5s" mas falta:
- **Bundle budgets** por tipo de projeto: marketing site = 150KB JS gzip; SaaS = 300KB; experience = 500KB+
- **Image strategy**: AVIF > WebP > JPEG; responsive `<picture>`; lazy load tudo abaixo do fold
- **Font loading**: `font-display: swap` (FOUT) vs `optional` (skip se lento)
- **Web Vitals concretos**: como medir, como pin, como gradiente alertas

→ Adicionar `references/10_performance.md`

### 4. Acessibilidade prática (além de "respeite WCAG")
- **Keyboard navigation patterns**: focus traps, skip links, accessible menus
- **Screen reader testing**: VoiceOver iOS/macOS, NVDA Windows, TalkBack Android — minimum check
- **Live regions** (aria-live polite/assertive) — quando usar cada
- **Form errors** — não confiar em :invalid CSS, usar `aria-invalid` + `aria-describedby`
- **Animation toggle** — não basta `prefers-reduced-motion`, oferecer toggle no UI
- **High contrast mode** (Windows) — testar com `forced-colors: active`

→ Adicionar `references/11_a11y_practical.md`

### 5. Copywriting / voice (subestimado mas crítico)
Sites premium têm copy premium. Quase sempre falta isso na skill:
- **Voice & tone matrix** — voice constante por brand, tone varia por contexto (erro vs sucesso vs marketing)
- **Active voice** — sempre, exceto contexto formal
- **2nd person** (you/vocês) > 3rd person impessoal
- **Headlines premium** seguem patterns: contradição ("Less is more"), specific number ("3 things"), question ("What if..."), command ("Stop X. Start Y.")
- **Microcopy importa**: button labels, empty states, error messages, loading states

→ Adicionar `references/12_copywriting.md`

### 6. Mobile-first e responsive (mais que media queries)
- **Container queries** (baseline 2023+) — componentes responsivos ao container, não ao viewport
- **Touch target minimum 44x44px** (Apple) / 48dp (Material)
- **Safe areas iOS** — `env(safe-area-inset-*)` pra notch / home indicator
- **Hover states em touch** — :hover quebra UX em mobile, usar `@media (hover: hover)`
- **Mobile-first CSS**: começar pelo smaller, complicar pra up

→ Adicionar `references/13_responsive_modern.md`

### 7. SEO técnico (mesmo um site bonito precisa ser achado)
- **Semantic HTML** — `<main>`, `<article>`, `<section>`, `<nav>` real
- **Meta tags essenciais**: title, description, og:image, twitter:card
- **Structured data** (JSON-LD) — pra rich results
- **Sitemap + robots.txt** — incluir sempre
- **Performance afeta SEO** — Core Web Vitals são ranking factors

→ Adicionar `references/14_seo_technical.md`

### 8. Internationalização desde início (não retrofit)
- **`lang` attribute correto** no `<html>`
- **Texto fluido** — se ficar tight em pt-BR pode quebrar em alemão (palavras longas)
- **RTL support** se relevante (ar, he) — `dir="rtl"` + `:dir(rtl)`
- **Date/number formatting** via Intl API
- **Font fallback** com Unicode range correto

→ Quando pedido, mas não default

### 9. Print stylesheet (ainda existe)
- `@media print` pra hide nav/CTAs, expand content
- Page break controls

→ Nicho mas importante pra alguns produtos

### 10. Email-safe HTML pra newsletters
- Tabelas em vez de flexbox/grid (Outlook quebra)
- Inline CSS sempre
- Sem JS

→ Nicho, fora do escopo padrão

## Conclusão do meta-planning

A skill `web-beautify` na v1 cobre passos 1-6. Próximos passos (v1.1, v1.2, etc) adicionam:
- v1.1: color science + motion principles + performance budgets
- v1.2: a11y prático + copywriting
- v1.3: responsive moderno + SEO técnico
- v2: i18n + print + email (opcional, on-demand)

A v1 já é poderosa sem isso. Melhor ship cedo + iterar com feedback real.

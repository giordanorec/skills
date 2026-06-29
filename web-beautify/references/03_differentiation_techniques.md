# Técnicas que diferenciam — anti-AI-slop, nivel Awwwards

> 2026 toolkit: o que os sites premiados estão fazendo que os "AI-default" não fazem.
> Cada item aqui tem uma recipe correspondente em `recipes/` quando aplicável.

## Banidos por default (estende lista da Anthropic frontend-design)

A `anthropics/frontend-design` já bane:
- ❌ Fontes Inter / Roboto / Arial / Space Grotesk (overused by AI)
- ❌ Gradientes roxos genéricos
- ❌ Combos: cream+serif+terracotta / near-black+neon / newspaper

Extensões nossas:
- ❌ "Hero centralizado com 1 H1 + 1 p + 2 botões pill" — esse é o template AI slop nº1
- ❌ "Grid 3-col de cards iguais com ícone + título + descrição" — abolido
- ❌ Numbered sections (01/02/03) **quando a sequência é arbitrária** (use só quando ordem importa de verdade)
- ❌ Section "estatísticas com 4 números grandes" sem contexto
- ❌ "Cards com border-radius 12px + box-shadow leve" como única decoração
- ❌ Footer 3-col "Empresa / Produto / Recursos" sem personalidade
- ❌ Botão `bg-gradient-to-r from-purple-500 to-pink-500` (a assinatura visual do AI slop)
- ❌ Emoji decorativo em headers sem função (🚀 ✨ 💡 espalhados)

## Permitidos com cuidado (têm valor mas viraram default)

- ⚠️ Bento grid — bom, mas tem que ser **ativo** (hover expande/revela), não estático
- ⚠️ Gradient mesh — bom, mas precisa ser **animado** e ter relação com a brand
- ⚠️ Glassmorphism — bom no header, ruim em todo lugar
- ⚠️ Scroll reveals — bom em moderação, fadiga rápido se em todo elemento
- ⚠️ Custom cursor — só em sites de design/portfolio/tools, nunca em SaaS/commerce

## Diferenciadores reais — recipes (todas em `recipes/`)

### 1. Kinetic typography
- Palavras que rotacionam no hero (não placeholder cycling — palavras semanticamente conectadas)
- Text split por char/word/line com GSAP SplitText, anima individualmente
- Font-variation-settings animando peso ao hover
- Tracking negativo agressivo (-3% a -4%) em displays grandes
- Variable fonts com axis customizado animado
- **Stack**: CSS animations OU GSAP SplitText OU react-text-transition

### 2. Scroll-driven storytelling
- Lenis pra smooth scroll global
- GSAP ScrollTrigger pra pinning + scrubbing
- CSS view-timeline pra animações 100% nativas
- Scroll-snap chapters (cada section = stop point)
- Parallax multicamada (até 4 camadas, mais que isso é confusão)
- **Stack**: Lenis + GSAP ScrollTrigger OU CSS view-timeline+scroll-snap

### 3. WebGL hero (uso premium, 1 por página max)
- Three.js + React Three Fiber pra mundo 3D
- Shader noise (Perlin/Simplex) pra texturas vivas
- Mouse interaction (parallax 3D, distortion)
- **Performance budget**: < 8MB JS, < 60 draw calls, mobile fallback obrigatório
- **Stack**: Three.js + R3F + drei

### 4. Bento grid ativo (Bento 2.0)
- Tiles de tamanhos diferentes (não grid uniforme — quebra ritmo)
- Hover expande, revela vídeo/animação, mostra dados extras
- Radius generoso (16-28px) mas consistente
- Mix de tile types: image, code, animation, stat, quote
- **Stack**: CSS Grid + IntersectionObserver

### 5. Marquee infinito (ticker)
- Pure CSS `animation: scroll Xs linear infinite`
- Conteúdo duplicado pra loop seamless
- Velocidade varia por tipo: logos = 30s, frases = 38s, fotos = 45s
- Pode ter dois marquees opostos em camadas diferentes
- **Stack**: CSS only

### 6. Custom cursor
- Substituir cursor por elemento DOM ou Canvas
- Estados: default / hover-link / hover-button / drag
- Smooth follow com `gsap.quickTo` ou `lerp`
- Magnetic effect em CTAs (cursor é puxado pro botão a 60px)
- **Use ONLY em**: sites de design, tools, portfolios. NUNCA em SaaS/commerce.
- **Stack**: GSAP quickTo OU vanilla JS lerp

### 7. Mesh gradient animado
- 3-5 blobs com `border-radius: 50%` + `filter: blur(80-120px)`
- `mix-blend-mode: multiply` ou `screen` pra interagir entre si
- Animação 12-20s com `cubic-bezier(.6,.05,.3,1)`
- Cores devem vir da DESIGN.md âncora, não inventadas
- **Stack**: CSS keyframes (ou WebGL pra mesh real)

### 8. Glassmorphism nav (sticky)
- `backdrop-filter: saturate(180%) blur(20px)` (não passar de 14px-20px por performance)
- `background: rgba(<bg-color>, .72)` (transparência calibrada — não 100% transparente)
- Border-bottom hairline `1px solid var(--border)`
- Sombra muito sutil (não passa de `0 1px 4px rgba(0,0,0,.04)`)
- **Stack**: CSS only

### 9. Page transitions (SPA-feeling em MPA)
- Barba.js pra page transitions clássicas
- View Transitions API (nativa, baseline Chrome+Safari+Firefox 2025+)
- Astro view transitions
- Layout-shared elements entre páginas
- **Stack**: Barba.js OU View Transitions API

### 10. Loading states com personalidade
- Skeleton screens com `linear-gradient` shimmer (não spinners genéricos)
- Loading sequences contadas (1/10, 2/10) que viram experiência
- "Loader" que reflete brand (ex: progress bar com a logo da marca)
- **Anti-pattern**: spinner genérico do CSS

### 11. Scroll-snap chapters
- `scroll-snap-type: y mandatory` no container
- Cada section = snapt point com altura `100svh`
- Indicador lateral (dots) clicável
- **Use quando**: narrativa linear, max ~7 chapters

### 12. Real product mockup in hero (não screenshot)
- Mini UI fake do produto, embedded no DOM
- Tilted 1-2deg (não 8deg que vira "perspectiva fake")
- Hover endireita
- Pode ter animações dentro (typing, button press, data flow)
- **Stack**: HTML/CSS only, ou React mini component

### 13. Variable font axis animado
- `font-variation-settings: 'wght' var(--w)` com hover transition
- Axis customizado de algumas fontes (ex: Recursive tem MONO/CASL/CRSV/slnt)
- Pode ser scroll-driven com Houdini ou CSS-only com :hover

### 14. Cursor-reactive elements
- Elementos que respondem à proximidade do mouse (não só hover)
- Magnetic links/buttons
- Cards que tiltam baseado em mouse position (subtle, max 4deg)
- **Stack**: vanilla JS com `mousemove` + `requestAnimationFrame`

### 15. Generative art como hero
- Canvas/SVG/WebGL gerando arte única no load (seed-based)
- Renaissance Edition (Awwwards SOTM Feb 2026) usou isso
- **Stack**: p5.js OU canvas vanilla OU SVG.js

## "Wow moments" obrigatórios (princípio xiaopu/web-design)

Toda landing premium tem 3 momentos "wow" + 1 detalhe sutil:
1. **Hero**: kinetic text OU WebGL OU mesh gradient animado
2. **Primeiro scroll** (rodar viewport pela 1a vez): scroll-triggered reveal forte OU pin moment
3. **List/Bento view**: hover ativo que paga o investimento
4. **Detalhe sutil**: custom cursor, easter egg, micro-interaction inesperada

Sem isso, o site é "competente" mas não "memorável".

## Performance guardrails (não comprometer pra ganhar wow)

- LCP < 2.5s (mobile)
- CLS < 0.1
- INP < 200ms
- Bundle JS < 200KB gzip (sem incluir WebGL)
- WebGL: max 1 cena por página
- `backdrop-filter`: max 20px blur
- Pin-scrub: max 2 instâncias
- Animations: respeitar `prefers-reduced-motion: reduce` SEMPRE

## Acessibilidade (não-negociável)

- Contraste WCAG AA mínimo: 4.5:1 texto normal, 3:1 texto grande/UI
- Focus visible em TUDO (não `outline: none` sem substituto)
- Keyboard navigation completa
- ARIA quando padrão HTML não cobre
- Reduced motion: animations decorativas DESLIGAM
- Screen readers: textos de imagens, ordem lógica de DOM
- Color: nunca SÓ cor pra transmitir significado (símbolo + cor)

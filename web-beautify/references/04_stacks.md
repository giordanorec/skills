# Stacks — qual ferramenta puxar pra qual efeito

> Regra de ouro 2026: cada library faz UMA coisa muito bem. Combiná-las certo é o craft.

## A árvore de decisão rápida

```
animar elementos de UI (hover, fade-in, page transition leve)
  → Motion (Framer Motion v12+) se React, OU CSS animations puras

animação complexa com timeline (sequência de N eventos, SVG morphing, text effects)
  → GSAP (+ plugins: ScrollTrigger, SplitText, MotionPath, MorphSVG)

scroll suave global + cinematic feel
  → Lenis (sucessor de Locomotive Scroll)

scroll-triggered animations sincronizadas
  → GSAP ScrollTrigger (combina com Lenis perfeitamente)
  OU CSS view-timeline (baseline 2025, sem JS)

3D / WebGL / shaders
  → Three.js (vanilla) OU React Three Fiber + drei (React)
  + Shader complexo → glsl + glslify

animação 2D vetorial com interatividade
  → Rive (substituiu Lottie na maioria dos cenários — state machines built-in)
  Lottie só pra animations exportadas direto do After Effects

animação editorial em código (estilo timeline visual)
  → Theatre.js

3D scenes editáveis sem código
  → Spline (export → Three.js OU iframe)

page transitions tipo SPA em MPA
  → Barba.js (clássico, multi-framework)
  OU View Transitions API (nativa baseline 2025)
  OU Astro view transitions

state management de animação cross-component
  → Motion (motion values são reativas)

texto split em char/word/line
  → GSAP SplitText (paid) OU split-type (free)

physics-based motion (spring, drag, throw)
  → Motion (boa API spring)
  OU react-spring (nicho React)

forms / inputs com animação fluida
  → Framer Motion + Radix UI
```

## Comparação dos pesos pesados

### GSAP (GreenSock)
- **Forte**: timelines complexas, ScrollTrigger, SplitText, plugins maduros
- **Fraco**: bundle (~50KB), curva, sintaxe imperativa
- **Use quando**: precisa de controle fino de sequência, scroll scrubbing, text effects
- **Don't use**: pra fade-in trivial em React

### Motion (ex-Framer Motion, v12+)
- **Forte**: API declarativa React-first, motion values reativas, spring physics
- **Fraco**: dependente de React, menos poderoso em scroll-scrubbing
- **Use quando**: app React precisa de animation engine
- **Don't use**: animations vanilla JS (overkill)

### Lenis
- **Forte**: smooth scroll que sente cinematic, integra com GSAP perfeitamente
- **Fraco**: muda comportamento de scroll padrão (cuidado com acessibilidade)
- **Use quando**: o site quer "feel premium", scroll lerp visível
- **Don't use**: sites onde scroll precisa ser instantâneo (docs, search results)

### Three.js / React Three Fiber
- **Forte**: o canon de WebGL na web
- **Fraco**: cost de aprendizado, performance é com você
- **Use quando**: cena 3D é central à experiência (não decorativa)
- **Don't use**: pra background sutil — CSS gradient resolve

### Rive
- **Forte**: state machines no editor, file size menor que Lottie, interativo
- **Fraco**: editor próprio (curva nova), não-React precisa de runtime
- **Use quando**: precisa de animation 2D que reage ao usuário/estado
- **Don't use**: animation pura exportada de AE (Lottie ainda funciona)

### CSS view-timeline + scroll-snap (nativo)
- **Forte**: zero JS, performance perfeita, baseline 2025
- **Fraco**: ainda limitado em complexidade
- **Use quando**: scroll reveals simples, parallax, snap chapters
- **Don't use**: scroll scrubbing complexo (vá GSAP ScrollTrigger)

## Stacks combinadas (receitas testadas)

### "Premium scroll story" (style: Active Theory, Locomotive)
```
Lenis + GSAP ScrollTrigger + React Three Fiber + drei
```
Lenis pro smooth scroll, ScrollTrigger pinning/scrubbing, R3F pro hero WebGL.

### "Editorial app" (style: Linear, Vercel)
```
Next.js + Motion + Tailwind CSS + Radix Primitives
```
Page transitions com Motion, scroll reveals com IntersectionObserver, UI com Radix.

### "Marketing site rápido" (style: Stripe, Notion)
```
Astro + view transitions API + GSAP (só onde precisar)
```
Astro pra perf insano + zero-JS por default, GSAP só nas seções animadas.

### "Awwwards-grade portfolio"
```
Vite + Three.js + GSAP + Lenis + Barba.js + glsl
```
Stack clássica de studio: Vite build, custom WebGL hero, page transitions Barba, shaders glsl.

### "SaaS dashboard polido"
```
Next.js App Router + Motion + Radix + Tremor + Lucide
```
Sem WebGL, foco em UI craft, animations em hover/transitions.

## Pegadinhas

- **Tailwind + Motion**: o Motion v12 tem `motion/react` (não `framer-motion`). Atualize imports.
- **GSAP em React**: use `useGSAP` do `@gsap/react`, não `useEffect` cru.
- **Lenis + ScrollTrigger**: precisa de `Lenis.on('scroll', ScrollTrigger.update)` + `gsap.ticker.add()` integration.
- **Three.js + Next.js**: o canvas precisa de `'use client'` no App Router.
- **Rive + Next.js**: SSR não renderiza canvas, use dynamic import com `ssr: false`.
- **View Transitions + React**: precisa de `unstable_ViewTransition` (React 19+) ou polyfill.

## Quando NÃO usar nada disso

Sites onde:
- Conteúdo > forma (artigos, docs, search)
- Performance é o feature principal (real-time, low-latency apps)
- Audiência idosa ou com low-end devices (animations cansam)
- A4 / printable layouts (animations não imprimem)

Nesses casos, CSS puro + transitions clássicas é a resposta certa.

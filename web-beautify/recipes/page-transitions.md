# Recipe — Page Transitions (SPA-feeling em MPA)

> Transições entre páginas que dão "feel" SPA mesmo em sites multi-página.
> Funciona em SSR / static / SPA. Trademark de Apple, Linear, Stripe.

## Quando usar

- ✅ Sites multi-página (MPA) que querem fluidez SPA
- ✅ Marketing sites com hierarquia de páginas (home / produto / about)
- ✅ Portfolio com case studies
- ❌ NÃO use em SPA já (você já tem transitions nativas)
- ❌ NÃO use em docs / longreads (transição atrapalha contexto)
- ❌ NÃO use sem `prefers-reduced-motion` fallback

## Opção 1 — View Transitions API (nativa, baseline 2025+)

Mais nova, zero deps, suporte Chrome+Edge+Safari+Firefox stable.

### Habilitar

```html
<!DOCTYPE html>
<html>
<head>
  <meta name="view-transition" content="same-origin">
</head>
```

⚠️ Só funciona com **same-origin** navigation (não cross-domain).

### CSS — controle das animações

```css
/* Old page = current state; new page = destination */
@view-transition {
  navigation: auto; /* habilita pra todas as navegações */
}

/* Default: fade cross-fade */
::view-transition-old(root),
::view-transition-new(root) {
  animation-duration: .4s;
  animation-timing-function: cubic-bezier(.6,.05,.3,1);
}

/* Custom: slide */
::view-transition-old(root) {
  animation: slideOutLeft .4s cubic-bezier(.6,.05,.3,1);
}
::view-transition-new(root) {
  animation: slideInRight .4s cubic-bezier(.6,.05,.3,1);
}
@keyframes slideOutLeft {
  to { transform: translateX(-10%); opacity: 0; }
}
@keyframes slideInRight {
  from { transform: translateX(10%); opacity: 0; }
}

@media (prefers-reduced-motion: reduce) {
  ::view-transition-old(root),
  ::view-transition-new(root) {
    animation: none;
  }
}
```

### Variant — Shared element transition

Elemento que persiste entre páginas (ex: hero image expand into product detail):

```html
<!-- página A (lista) -->
<img src="/p/abc.jpg" style="view-transition-name: card-abc" />

<!-- página B (detalhe) -->
<img src="/p/abc.jpg" style="view-transition-name: card-abc" />
```

O browser anima a transição entre os dois pontos automaticamente.

### Trigger manual (JS)

```js
// Em SPA frameworks
function navigate(url) {
  if (!document.startViewTransition) {
    location.href = url;
    return;
  }
  document.startViewTransition(() => {
    fetchPageAndReplaceDOM(url);
  });
}
```

## Opção 2 — Astro view transitions

Astro 3+ tem View Transitions API integrada. Habilitar:

```astro
---
// src/layouts/Layout.astro
import { ViewTransitions } from 'astro:transitions';
---
<html>
<head>
  <ViewTransitions />
</head>
<body><slot /></body>
</html>
```

E aplicar `transition:animate` em elementos:

```astro
<img src="..." transition:name="hero-img" transition:animate="slide" />
```

## Opção 3 — Barba.js (clássico, funciona em qualquer stack)

Pra projetos que não dão pra usar View Transitions API.

```bash
npm install @barba/core
```

```js
import barba from '@barba/core';

barba.init({
  transitions: [{
    name: 'fade',
    leave(data) {
      return new Promise((resolve) => {
        gsap.to(data.current.container, {
          opacity: 0,
          duration: .3,
          onComplete: resolve,
        });
      });
    },
    enter(data) {
      return new Promise((resolve) => {
        gsap.fromTo(data.next.container,
          { opacity: 0 },
          { opacity: 1, duration: .4, onComplete: resolve }
        );
      });
    },
  }],
});
```

HTML wrapper:

```html
<main data-barba="wrapper">
  <div data-barba="container" data-barba-namespace="home">
    <!-- conteúdo da página -->
  </div>
</main>
```

## Opção 4 — Framer Motion / Motion (React)

Pra apps React. AnimatePresence + layout animations.

```jsx
import { AnimatePresence, motion } from 'motion/react';

function App() {
  const location = useLocation(); // do router
  return (
    <AnimatePresence mode="wait">
      <motion.div
        key={location.pathname}
        initial={{ opacity: 0, y: 20 }}
        animate={{ opacity: 1, y: 0 }}
        exit={{ opacity: 0, y: -20 }}
        transition={{ duration: .35, ease: [.6, .05, .3, 1] }}
      >
        <Routes>{/* ... */}</Routes>
      </motion.div>
    </AnimatePresence>
  );
}
```

## Princípios

1. **Curta**: 200-400ms total. Mais cansa, menos parece bug.
2. **Easing premium**: `cubic-bezier(.6,.05,.3,1)` ou `expo.out` (Motion/GSAP)
3. **Movimento sutil**: 10-20px de translate, não 100px
4. **Direção semântica**: voltar = slide-right; avançar = slide-left
5. **Reduced motion**: SEMPRE desligar movement, manter só opacity fade

## Tipos comuns

| Tipo | Quando usar |
|---|---|
| Cross-fade | Default, funciona em qualquer transição |
| Slide horizontal | Navegação sequencial (next/prev) |
| Slide vertical | Mobile-feel, parent → child |
| Shared element | Hero image expand into detail |
| Wipe / mask | Brand sites, dramatic transitions |
| Morph / shape | Studios, awwwards (custom, caro) |

## Performance

- View Transitions API: nativa, GPU-accelerated, custo zero
- Barba.js: ~5KB gzip + GSAP/Motion (se usar)
- Motion AnimatePresence: parte do bundle Motion (~30KB)
- View transitions atrasam navigation por ~300ms (psicologicamente OK; perceptualmente "premium")

## Acessibilidade

- ⚠️ `prefers-reduced-motion: reduce` → desligar transition motion
- ⚠️ Focus management: foco deve ir pro conteúdo da nova página (não ficar no link clicado)
- ⚠️ Screen readers anunciam mudança de página automaticamente — não interferir
- ⚠️ Loading state visível se transição > 500ms (algum erro)

### Focus management — código canônico

```js
// Em qualquer framework SPA, após navegação:
function onRouteChange() {
  // Move focus pro main content (não pro <a> clicado)
  const main = document.querySelector('main') || document.querySelector('h1');
  if (main) {
    main.setAttribute('tabindex', '-1');
    main.focus({ preventScroll: false });
  }

  // Announce route change pro screen reader
  const ann = document.getElementById('route-announcer');
  if (ann) {
    ann.textContent = ''; // reset
    setTimeout(() => {
      ann.textContent = `Navegou para ${document.title}`;
    }, 50);
  }
}
```

```html
<!-- Live region pra anunciar mudança de página -->
<div id="route-announcer" aria-live="polite" aria-atomic="true" class="sr-only"></div>
```

## Anti-patterns

❌ Transition > 600ms (parece bug)
❌ Transition em TODA navegação inclusive same-page anchors (excessive)
❌ Movimento exagerado (slide de 100% da viewport — desorientador)
❌ Cross-origin navigations (View Transitions API não funciona, só same-origin)
❌ Sem `prefers-reduced-motion` fallback
❌ Esquecer focus management após navegação
❌ Layout shift durante transição (CLS hit)

## Detectar suporte

```js
if (!document.startViewTransition) {
  // Browser velho — fallback: navegação normal
} else {
  // Browser moderno — usar API
}
```

## Inspirações reais

- apple.com (cross-fade entre product pages)
- linear.app/now (slide transitions)
- stripe.com (subtle fade + scale)
- vercel.com (View Transitions API em uso)
- humaan.com (shared element transitions)
- offbrand.studio (custom morph transitions)

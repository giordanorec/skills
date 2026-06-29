# Recipe — Scroll-Snap Story (narrative chapters)

> Página dividida em chapters, cada um ocupando 100vh, com snap automático.
> Funciona como slide-deck mas rolável. Usado por Apple em product launches,
> Stripe em onboarding, Linear em "now" page.

## Quando usar

- ✅ Narrativa linear forte (manifesto, launch story, onboarding)
- ✅ Cada chapter é uma unidade visual completa
- ✅ Max ~7 chapters (mais = fadiga)
- ❌ NÃO use em conteúdo escaneável (artigos, docs, lista de features mistas)
- ❌ NÃO use sem fallback no mobile (snap em mobile é ruim com toolbars dinâmicos)
- ❌ NÃO use se há > 1 fold de texto por chapter (snap atrapalha leitura longa)

## HTML

```html
<main class="story">
  <section class="chapter chapter-1" data-chapter="01">
    <div class="chapter-inner">
      <p class="eyebrow">capítulo 01</p>
      <h2>O ano em que tudo mudou</h2>
      <p>Texto curto, focado. 2-3 parágrafos max.</p>
    </div>
  </section>

  <section class="chapter chapter-2" data-chapter="02">
    <div class="chapter-inner">
      <p class="eyebrow">capítulo 02</p>
      <h2>O que aprendemos</h2>
      <p>...</p>
    </div>
  </section>

  <!-- mais chapters -->
</main>

<aside class="story-nav" aria-label="Navegação por capítulos">
  <a href="#ch1" class="story-dot" data-target="1"><span class="sr-only">Capítulo 1</span></a>
  <a href="#ch2" class="story-dot" data-target="2"><span class="sr-only">Capítulo 2</span></a>
  <a href="#ch3" class="story-dot" data-target="3"><span class="sr-only">Capítulo 3</span></a>
</aside>
```

## CSS

```css
html { scroll-behavior: smooth; }

.story {
  /* `proximity` em vez de `mandatory` — keyboard PgDn/PgUp continuam funcionando.
     `mandatory` quebra Page Up/Down: usuário não consegue parar entre chapters. */
  scroll-snap-type: y proximity;
  overflow-y: scroll;
  height: 100vh;
  scroll-padding-top: 0;
}

.chapter {
  scroll-snap-align: start;
  scroll-snap-stop: normal; /* `always` força stop e quebra keyboard skip */
  min-height: 100svh;        /* svh > vh em mobile (lida com toolbars) */
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 80px 24px;
  position: relative;
}

/* Cada chapter com sua identidade visual */
.chapter-1 { background: var(--bg);     color: var(--ink); }
.chapter-2 { background: var(--accent); color: var(--accent-fg); }
.chapter-3 { background: var(--bg-deep); color: #fff; }
.chapter-4 { background: var(--bg-alt); color: var(--ink); }

.chapter-inner {
  max-width: 720px;
  text-align: center;
}

.chapter h2 {
  font-family: var(--font-display);
  font-size: clamp(40px, 7vw, 88px);
  font-weight: 800;
  letter-spacing: -.035em;
  line-height: 1.02;
  margin: 0 0 24px;
}

/* Story nav lateral (dots) */
.story-nav {
  position: fixed;
  right: 24px;
  top: 50%;
  transform: translateY(-50%);
  display: flex;
  flex-direction: column;
  gap: 12px;
  z-index: 50;
}
.story-dot {
  /* WCAG 2.5.8 NEW 2.2: touch target ≥ 24px. Visualmente 10px, mas área clicável 28px. */
  position: relative;
  display: block;
  width: 28px; height: 28px;
  background: transparent;
}
.story-dot::after {
  content: '';
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
  width: 10px; height: 10px;
  border-radius: 50%;
  background: var(--ink-2);
  opacity: .3;
  transition: opacity .2s ease, transform .2s ease;
}
.story-dot[aria-current="true"]::after {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1.4);
  background: var(--accent);
}
.story-dot:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 2px;
  border-radius: 50%;
}
```

## JS — sincronizar dots com chapter visível (com aria-current)

```js
const chapters = document.querySelectorAll('.chapter');
const dots = document.querySelectorAll('.story-dot');

const io = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.intersectionRatio < 0.5) return;
    const idx = Array.from(chapters).indexOf(e.target);
    // aria-current="true" no dot ativo (WCAG 2.4.8 location)
    dots.forEach(d => d.removeAttribute('aria-current'));
    if (dots[idx]) dots[idx].setAttribute('aria-current', 'true');
  });
}, { threshold: 0.5, root: document.querySelector('.story') });

chapters.forEach(c => io.observe(c));

// Click dot → scroll to chapter
dots.forEach((dot, i) => {
  dot.addEventListener('click', (e) => {
    e.preventDefault();
    chapters[i].scrollIntoView({ behavior: 'smooth' });
    // Move focus pro chapter destino (WCAG 2.4.3 Focus Order)
    chapters[i].setAttribute('tabindex', '-1');
    chapters[i].focus({ preventScroll: true });
  });
});
```

## HTML — chapters precisam ser focusable + identificáveis

```html
<section class="chapter chapter-1" id="ch-1" tabindex="-1" aria-labelledby="ch-1-h">
  <div class="chapter-inner">
    <p class="eyebrow">capítulo 01</p>
    <h2 id="ch-1-h">Título do chapter</h2>
    <p>...</p>
  </div>
</section>
```

## Variant — Snap horizontal (chapters lado-a-lado)

```css
.story-horizontal {
  scroll-snap-type: x mandatory;
  overflow-x: scroll;
  display: flex;
  width: 100vw;
  height: 100vh;
}
.chapter-h {
  scroll-snap-align: start;
  flex: 0 0 100vw;
  height: 100vh;
}
```

⚠️ Horizontal snap quebra UX padrão (scroll vertical é esperado em web). Use só pra galleries ou step-flows curtos.

## Variant — Chapter com pin scrub (parallax dentro do snap)

Combinação Lenis + GSAP ScrollTrigger pra pinning dentro do chapter:

```js
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
gsap.registerPlugin(ScrollTrigger);

gsap.to('.chapter-2 .reveal-image', {
  scale: 1.3,
  scrollTrigger: {
    trigger: '.chapter-2',
    start: 'top top',
    end: 'bottom top',
    scrub: 1,
    pin: false,
  }
});
```

## Variant — Background color transition entre chapters

```js
const chapterColors = {
  'chapter-1': '#ffffff',
  'chapter-2': '#ff385c',
  'chapter-3': '#0a0a0a',
};

const io2 = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.intersectionRatio < 0.5) return;
    const cls = [...e.target.classList].find(c => chapterColors[c]);
    if (cls) document.documentElement.style.background = chapterColors[cls];
  });
}, { threshold: 0.5 });

chapters.forEach(c => io2.observe(c));
```

Pair com CSS:
```css
html { transition: background .6s cubic-bezier(.6,.05,.3,1); }
```

## Performance

- Scroll-snap é nativo, custo zero
- IntersectionObserver pra dots = barato
- Pin-scrub (variant) = + GSAP/Lenis, ~30KB
- `100svh` mobile-safe (evita jump quando toolbar aparece)

## Acessibilidade

- ⚠️ Snap "captura" usuário keyboard — verificar que Page Down/Up ainda funcionam
- ⚠️ Screen readers: chapters precisam ter `<section>` + `aria-labelledby="ch-title"`
- ⚠️ `scroll-snap-stop: always` evita "pular" chapter (importante pra SR)
- ⚠️ Skip link no topo permitindo pular pro footer
- ⚠️ `prefers-reduced-motion: reduce` → desligar `scroll-behavior: smooth`:
  ```css
  @media (prefers-reduced-motion: reduce) {
    html { scroll-behavior: auto; }
    .story { scroll-snap-type: none; }
  }
  ```

## Anti-patterns

❌ Snap em página com > 7 chapters (cansa muito)
❌ Chapter com texto longo que precisa rolar (snap vs scroll conflito)
❌ Snap horizontal sem indicação visual (quebra expectativa)
❌ Esquecer `100svh` (mobile fica quebrado com toolbars)
❌ Sem dots/indicator (usuário não sabe onde está nem quantos faltam)
❌ Snap em conteúdo escaneável (não-narrativo)
❌ Sem reduced-motion fallback

## Inspirações reais

- apple.com/iphone-16/ (chapters product story)
- stripe.com/atlas (onboarding chapters)
- linear.app/now (release story)
- the-feed.in (manifesto sites)
- humaan.com (case studies por snap)

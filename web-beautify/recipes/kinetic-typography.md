# Recipe — Kinetic Typography

> Texto em movimento como statement central do hero. Diferencia drasticamente
> de "H1 estático + parágrafo + 2 botões". Usado por Stripe, Vercel, Linear.

## Quando usar

- ✅ Hero principal de landing premium
- ✅ Section dramática (transição entre chapters)
- ✅ Mostrar "uma série de X" (rotação semântica)
- ❌ NÃO use em body copy (cansa, ilegível)
- ❌ NÃO use sem `prefers-reduced-motion` desligar
- ❌ NÃO use se a audiência tem reading disabilities (overload visual)

## Variant A — Rotating word (palavra que cicla)

A mais usada. Word swap dentro de um H1 estático.

### HTML

⚠️ **A11y crítico**: o rotator é visual-only. Sem `aria-hidden`, leitor de tela
recebe 5 palavras seguidas como frase incoerente ("uma palavra um cheiro uma sensação
domingo de manhã uma palavra"). Solução: `aria-hidden` no rotator + texto canônico em `.sr-only`.

```html
<h1 class="hero-title">
  Você pensa em
  <!-- Versão pra leitor de tela: texto estático canônico -->
  <span class="sr-only">uma palavra, um cheiro, uma sensação.</span>
  <!-- Versão visual: rotator escondido pro SR -->
  <span class="kinetic-rotator" aria-hidden="true">
    <span class="kinetic-track">
      <span class="kinetic-word">uma palavra.</span>
      <span class="kinetic-word">um cheiro.</span>
      <span class="kinetic-word">uma sensação.</span>
      <span class="kinetic-word">domingo de manhã.</span>
      <span class="kinetic-word">uma palavra.</span> <!-- repetir 1º pra loop seamless -->
    </span>
  </span>
  Eles capturam o sabor.
</h1>
```

### CSS

```css
/* Padrão universal pra esconder visualmente mas manter pro SR */
.sr-only {
  position: absolute;
  width: 1px; height: 1px;
  padding: 0; margin: -1px;
  overflow: hidden; clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

/* Tracking display agressivo (-2.5% a -3.5%) — diferencia kinetic Stripe-tier de AI slop */
.hero-title {
  font-size: clamp(48px, 7vw, 92px);
  font-weight: 800;
  line-height: 1.02;
  letter-spacing: -0.03em;
}

.kinetic-rotator {
  display: inline-block;
  height: 1.05em;
  overflow: hidden;
  vertical-align: bottom;
  position: relative;
}
.kinetic-track {
  display: block;
  animation: kineticScroll 12s cubic-bezier(.6,.05,.3,1) infinite;
}
.kinetic-word {
  display: block;
  color: var(--accent);
  font-style: italic;
  line-height: 1.05;
  letter-spacing: -0.035em; /* italic aparenta mais espaçado, tightening compensa */
  font-weight: 700; /* 700 italic > 800 italic (que geralmente fica feio) */
}
@keyframes kineticScroll {
  0%, 18% { transform: translateY(0%); }
  22%, 40% { transform: translateY(-100%); }
  44%, 62% { transform: translateY(-200%); }
  66%, 84% { transform: translateY(-300%); }
  88%, 100% { transform: translateY(-400%); }
}
@media (prefers-reduced-motion: reduce) {
  .kinetic-track { animation: none; transform: translateY(0); }
  .kinetic-word:not(:first-child) { display: none; }
}
```

**Princípios chave**:
- 4-5 palavras max (mais cansa)
- Cada palavra fica ~2.5s visível
- `overflow: hidden` no rotator
- Repetir 1ª palavra no final pra loop SEM jank
- Use `cubic-bezier(.6,.05,.3,1)` (não `ease`) — feel premium

## Variant B — Text split + reveal (GSAP SplitText)

Cada char/word aparece individualmente em sequência. Usado em Stripe, Linear.

### HTML

```html
<h1 class="hero-title split-text">A vida toda em uma única ideia.</h1>
```

### JS com GSAP SplitText

```js
import { gsap } from 'gsap';
import { SplitText } from 'gsap/SplitText';
gsap.registerPlugin(SplitText);

const split = new SplitText('.split-text', { type: 'chars,words' });

gsap.from(split.chars, {
  y: 80,
  opacity: 0,
  rotateX: -90,
  stagger: 0.02,
  duration: 1,
  ease: 'expo.out',
});
```

### Alternativa free — `split-type`

```js
import SplitType from 'split-type';
const split = new SplitType('.split-text', { types: 'chars,words' });

split.chars.forEach((char, i) => {
  char.style.opacity = '0';
  char.style.transform = 'translateY(40px)';
  char.style.transition = `opacity .6s ease ${i * 20}ms, transform .6s cubic-bezier(.6,.05,.3,1) ${i * 20}ms`;
});

setTimeout(() => {
  split.chars.forEach(char => {
    char.style.opacity = '1';
    char.style.transform = 'translateY(0)';
  });
}, 100);
```

## Variant C — Gradient text animado

```html
<h1 class="hero-title">
  <span class="gradient-text">Mais que um jogo.</span>
</h1>
```

```css
.gradient-text {
  background: linear-gradient(
    90deg,
    var(--accent) 0%,
    var(--accent-2) 35%,
    var(--accent-3) 70%,
    var(--accent) 100%
  );
  background-size: 300% auto;
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: gradientShift 6s linear infinite;
}
@keyframes gradientShift {
  to { background-position: 300% center; }
}
```

## Variant D — Variable font weight animado

Pra fontes variáveis (Recursive, Inter Variable, etc):

```css
@import url('https://fonts.googleapis.com/css2?family=Recursive:wght@300..1000&display=swap');

.kinetic-weight {
  font-family: 'Recursive', sans-serif;
  font-variation-settings: 'wght' var(--w, 400);
  transition: --w .4s ease;
}
.kinetic-weight:hover { --w: 900; }
```

(Precisa `@property` registrado pra `--w` animar suavemente)

```css
@property --w {
  syntax: '<number>';
  initial-value: 400;
  inherits: false;
}
```

## Variant E — Typewriter / caret blink

Útil em terminals, code blocks, demo mockups.

```html
<div class="fake-input">tem cheiro de chuva quente<span class="caret"></span></div>
```

```css
.caret {
  display: inline-block;
  width: 2px; height: 1em;
  background: var(--accent);
  margin-left: 2px;
  vertical-align: text-bottom;
  animation: caretBlink 1.1s steps(2) infinite;
}
@keyframes caretBlink { 50% { opacity: 0; } }
```

## Performance

- Variants A, C, D: 100% CSS, custo zero
- Variants B (GSAP SplitText): + ~30KB gzip, mas vale em Tier 3-4
- Variant B (split-type free): + ~5KB gzip
- Sempre: `will-change: transform, opacity` nos elementos animados

## Acessibilidade

- **Sempre** ter `prefers-reduced-motion: reduce` desligando animation
- Variant A: leitor de tela vai ler TODAS as palavras seguidas — considere `aria-hidden="true"` no rotator + `<span class="sr-only">` com texto estático
- Variant B: leitor lê texto inteiro normal (SplitText preserva)

## Anti-patterns

❌ Texto rotacionando em body copy (faz dor de cabeça)
❌ Velocidade muito rápida (palavras 1-2s) — ninguém lê
❌ Velocidade muito lenta (palavras > 5s) — fica parado
❌ Mais de 6 palavras na rotação (overload)
❌ Cor da palavra rotacionando = mesma do background (ilegível)
❌ Sem fallback reduced-motion (acessibilidade quebrada)

## Inspirações reais

- stripe.com homepage (gradient text + word rotation)
- vercel.com (split text reveal)
- linear.app (subtle text reveals)
- typewolf.com (catálogo de typography web)

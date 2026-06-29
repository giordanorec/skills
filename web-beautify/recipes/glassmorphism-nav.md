# Recipe — Glassmorphism Nav (sticky header com blur)

> Header translúcido com `backdrop-filter: blur` que deixa conteúdo aparecer
> por trás de forma sutil. Trademark de Apple, Linear, Vercel.
> Custo: zero JS, performance OK se blur ≤ 20px.

## Quando usar

- ✅ Sticky nav em qualquer site moderno (premium feel)
- ✅ Floating action bars
- ✅ Modal overlays
- ❌ NÃO use em todos os cards (overwhelming, perf hit)
- ❌ NÃO use sem fallback pra browsers velhos
- ❌ NÃO use com blur > 20px (perf cai em scroll)

## HTML

```html
<header class="site-header">
  <div class="header-inner">
    <a href="/" class="brand">
      <span class="brand-mark">🌶️</span>
      <span class="brand-title">Sabor Jogo</span>
    </a>
    <nav class="header-nav">
      <a href="/historia">história</a>
      <a href="/jogar">jogar</a>
    </nav>
    <div class="header-actions">
      <a href="/login" class="btn btn-secondary">entrar</a>
      <a href="/signup" class="btn btn-primary">criar conta</a>
    </div>
  </div>
</header>
```

## CSS

```css
.site-header {
  position: sticky;
  top: 0;
  z-index: 50;
  /* 0.92 garante 4.5:1 contraste mesmo sobre mesh gradient laranja vibrante.
     0.72 era o ideal estético mas falha WCAG quando o background atrás é colorido.
     Se sua brand permite glass mais translúcido, mede contraste em TODOS os
     possíveis fundos (hero gradient, dark mode, mesh, etc) antes de baixar. */
  background: rgba(255, 255, 255, .92);
  backdrop-filter: saturate(180%) blur(20px);
  -webkit-backdrop-filter: saturate(180%) blur(20px);
  border-bottom: 1px solid var(--border, rgba(0,0,0,.08));
}

/* Fallback solid pra browsers sem backdrop-filter */
@supports not (backdrop-filter: blur(20px)) {
  .site-header { background: rgba(255, 255, 255, .98); }
}

/* Forced colors mode (Windows High Contrast) — preservar contorno */
@media (forced-colors: active) {
  .site-header {
    background: Canvas;
    border-bottom: 1px solid CanvasText;
  }
}

.header-inner {
  max-width: 1280px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 14px 24px;
  gap: 16px;
}

.brand {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  text-decoration: none;
  color: var(--ink, #1a1a1a);
}
.brand-mark { font-size: 22px; line-height: 1; transition: transform .3s ease; }
.brand:hover .brand-mark { transform: rotate(-12deg); }
.brand-title {
  /* Fonte vem da DESIGN.md âncora. Fallback SÓ system stack (Inter é AI default banido). */
  font-family: var(--font-display, system-ui, -apple-system, BlinkMacSystemFont, sans-serif);
  font-size: 17px;
  font-weight: 800;
  letter-spacing: -.025em;
}

.header-nav {
  display: flex;
  gap: 24px;
  margin-left: auto;
  margin-right: 16px;
}
.header-nav a {
  font-size: 14px;
  font-weight: 500;
  color: var(--ink, #1a1a1a);
  opacity: .7;
  text-decoration: none;
  transition: opacity .15s ease;
}
.header-nav a:hover { opacity: 1; }
.header-nav a[aria-current="page"] { opacity: 1; font-weight: 600; }

/* Focus visible — WCAG 2.4.7 (não-negociável) */
.header-nav a:focus-visible,
.brand:focus-visible,
.header-actions .btn:focus-visible {
  outline: 2px solid var(--accent, #0066cc);
  outline-offset: 4px;
  border-radius: 4px;
  opacity: 1;
}

.header-actions {
  display: flex;
  gap: 8px;
}
```

## Princípios pra glassmorphism ficar bom

1. **Transparência calibrada**: 0.72-0.85 (não 0.5 ou 1.0)
   - 100% transparente = você não vê o blur trabalhando
   - < 60% = pode ficar ilegível em backgrounds variados
2. **Blur 14-20px**: mais que isso = perf hit; menos = não dá "feel"
3. **`saturate(180%)`**: aumenta saturação pra compensar branco do blur
4. **Border-bottom hairline**: define sutilmente onde nav acaba (não shadow pesada)
5. **Z-index alto** (>50): nav sempre por cima
6. **`-webkit-backdrop-filter`**: ainda necessário em Safari

## Variant — Dark theme glass

```css
[data-theme="dark"] .site-header {
  background: rgba(18, 18, 18, .72);
  border-bottom-color: rgba(255, 255, 255, .08);
}
```

## Variant — Glass com hide-on-scroll-down

Header que some ao scrollar pra baixo, volta ao scrollar pra cima.

```js
let lastScroll = 0;
const header = document.querySelector('.site-header');

window.addEventListener('scroll', () => {
  const current = window.scrollY;
  if (current > lastScroll && current > 80) {
    header.classList.add('header--hidden');
  } else {
    header.classList.remove('header--hidden');
  }
  lastScroll = current;
}, { passive: true });
```

```css
.site-header {
  transition: transform .3s cubic-bezier(.6,.05,.3,1);
}
.site-header.header--hidden {
  transform: translateY(-100%);
}
```

## Variant — Glass com solid background ao scrollar

Header começa transparente, fica sólido ao scrollar:

```js
const header = document.querySelector('.site-header');
const updateHeader = () => {
  header.classList.toggle('header--scrolled', window.scrollY > 50);
};
window.addEventListener('scroll', updateHeader, { passive: true });
updateHeader();
```

```css
.site-header {
  background: rgba(255, 255, 255, 0);
  border-bottom: 1px solid transparent;
  transition: background .25s ease, border-color .25s ease, backdrop-filter .25s ease;
}
.site-header.header--scrolled {
  background: rgba(255, 255, 255, .85);
  backdrop-filter: saturate(180%) blur(20px);
  border-bottom-color: var(--border);
}
```

## Performance

- `backdrop-filter` é GPU-accelerated mas pesado em mobile low-end
- Blur > 20px causa **repaint em cada scroll** (visible jank)
- Solução pra mobile: reduzir blur ou desligar:

```css
@media (max-width: 600px) {
  .site-header { backdrop-filter: blur(10px); }
}
```

- Use `will-change: transform` se header tem `hide-on-scroll`
- Test `Performance Monitor` no DevTools: rasterize time < 8ms é OK

## Acessibilidade

- Contraste do texto da nav sobre o blur deve ser AA mesmo quando background atrás é variado
  - Solução: garantir transparência ≤ .85 + saturate
- `aria-current="page"` no link ativo (pra leitor de tela)
- Tab navigation funciona normal (não precisa nada extra)
- Skip link antes do header pra usuários keyboard:
  ```html
  <a href="#main" class="skip-link">pular pra conteúdo</a>
  ```

## Anti-patterns

❌ Blur > 30px (perf morre em scroll)
❌ Transparência 100% sem fallback solid (texto ilegível em backgrounds variados)
❌ Glass em TODO card do site (cansa, perf hit cumulativo)
❌ Esquecer `-webkit-backdrop-filter` (Safari mostra sólido)
❌ Esquecer `@supports not (backdrop-filter)` fallback (browsers velhos)
❌ Header sem `position: sticky` (ponto da glass é parecer "flutuante")
❌ Header sem border-bottom hairline (parece flutuar feio)

## Inspirações reais

- apple.com (header é THE reference)
- linear.app (sutil mas perfeito)
- vercel.com (high contrast version)
- arc.net (extreme glass everywhere — referência mas overkill)

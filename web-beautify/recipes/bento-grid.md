# Recipe — Bento Grid (Bento 2.0, active tiles)

> Grid modular inspirado em bento lunchbox japonês. Tiles de tamanhos diferentes,
> hover ativo (expande, revela vídeo, mostra dados). Popularizado por Apple,
> adotado por 67% dos top SaaS landings em 2026.

## Quando usar

- ✅ Showcase de features (5-7 features, cada uma é uma "história")
- ✅ Portfolio com peças de tamanhos diferentes
- ✅ Dashboard com cards de tipos distintos
- ❌ NÃO use pra grid uniforme de cards iguais (vira tabela)
- ❌ NÃO use com < 4 tiles (não tem variação)
- ❌ NÃO use com > 9 tiles (vira caos)

## Layout — princípios

1. **Tiles de tamanhos diferentes** (não uniforme — quebra ritmo)
2. **Radius consistente** (16-24px, NUNCA misturar 8 com 24)
3. **Gap uniforme** (16-24px entre todos)
4. **Cada tile tem propósito visual diferente** (não 4 tiles de "ícone + título + texto")

## HTML

```html
<section class="bento" id="features">
  <header class="bento-header">
    <p class="eyebrow">recursos</p>
    <h2>Quatro coisas que mudam como você joga.</h2>
  </header>
  <div class="bento-grid">
    <article class="bento-tile bento-large">
      <span class="bento-num">01</span>
      <h3>Headline curta e específica</h3>
      <p>Descrição em 2 linhas max. Foca em valor, não em feature.</p>
      <div class="bento-visual">
        <!-- ilustração / animação / mockup -->
      </div>
    </article>

    <article class="bento-tile bento-medium">
      <h3>Tile médio</h3>
      <p>Texto + visual mais comedido.</p>
    </article>

    <article class="bento-tile bento-small">
      <h3>Tile pequeno</h3>
      <p>Stat ou quote breve.</p>
    </article>

    <article class="bento-tile bento-wide">
      <h3>Tile horizontal largo</h3>
      <p>Pra conteúdo que precisa de largura.</p>
    </article>
  </div>
</section>
```

## CSS — base

```css
.bento {
  padding: 96px 24px;
  background: var(--bg-alt);
}
.bento-header { max-width: 720px; margin: 0 auto 48px; text-align: center; }
.bento-header h2 {
  font-size: clamp(32px, 4.5vw, 56px);
  font-weight: 800;
  letter-spacing: -.025em;
  line-height: 1.05;
}

.bento-grid {
  max-width: 1200px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  grid-auto-rows: 280px;
  gap: 20px;
}

/* Tiles — definir spans explícitos */
.bento-large  { grid-column: span 4; grid-row: span 2; }
.bento-medium { grid-column: span 2; }
.bento-small  { grid-column: span 2; }
.bento-wide   { grid-column: span 6; }

@media (max-width: 900px) {
  .bento-grid { grid-template-columns: 1fr; grid-auto-rows: auto; }
  .bento-large, .bento-medium, .bento-small, .bento-wide {
    grid-column: 1; grid-row: auto; min-height: 280px;
  }
}

/* Tile base */
.bento-tile {
  position: relative;
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: 24px;
  padding: 32px;
  overflow: hidden;
  display: flex; flex-direction: column;
  box-shadow:
    0 0 0 1px rgba(0,0,0,.02),
    0 2px 6px rgba(0,0,0,.04);
  transition:
    transform .35s cubic-bezier(.2,.8,.2,1),
    box-shadow .35s ease,
    border-color .25s ease;
}
.bento-tile:hover {
  transform: translateY(-6px);
  box-shadow:
    0 0 0 1px rgba(0,0,0,.04),
    0 12px 32px rgba(0,0,0,.10),
    0 4px 12px rgba(0,0,0,.06);
  border-color: color-mix(in srgb, var(--accent) 20%, var(--border));
}

.bento-num {
  font-family: var(--font-mono);
  font-size: 12px; font-weight: 600;
  color: var(--accent);
  letter-spacing: .15em;
  margin-bottom: 16px;
}
.bento-tile h3 {
  font-size: clamp(20px, 2vw, 26px);
  font-weight: 700;
  letter-spacing: -.015em;
  line-height: 1.2;
  margin: 0 0 12px;
}
.bento-tile p {
  font-size: 15px;
  color: var(--muted);
  margin: 0 0 auto;
}
```

## Bento 2.0 — active tiles (hover expande/revela)

Pra ir além do "card com hover lift":

### Variant A — Tile que mostra preview no hover

```css
.bento-tile .preview {
  position: absolute;
  inset: auto 0 -100% 0;
  height: 50%;
  background: var(--card-2);
  border-top: 1px solid var(--border);
  padding: 16px;
  transition: bottom .35s cubic-bezier(.2,.8,.2,1);
}
.bento-tile:hover .preview { bottom: 0; }
```

### Variant B — Tile com video silencioso no hover

```html
<article class="bento-tile bento-large has-video">
  <video class="bento-video" muted loop playsinline preload="metadata">
    <source src="/preview.mp4" type="video/mp4">
  </video>
  <div class="bento-content">
    <h3>Veja em movimento</h3>
  </div>
</article>
```

```css
.bento-tile.has-video { position: relative; }
.bento-video {
  position: absolute; inset: 0;
  width: 100%; height: 100%;
  object-fit: cover;
  opacity: 0;
  transition: opacity .35s ease;
}
.bento-tile.has-video:hover .bento-video { opacity: .8; }
.bento-tile.has-video .bento-content {
  position: relative;
  z-index: 1;
  color: white;
  mix-blend-mode: difference;
}
```

Trigger video play no hover via JS:
```js
document.querySelectorAll('.has-video').forEach(tile => {
  const video = tile.querySelector('video');
  tile.addEventListener('mouseenter', () => video.play());
  tile.addEventListener('mouseleave', () => video.pause());
});
```

### Variant C — Tile com stat animado (count up)

```html
<article class="bento-tile bento-small bento-stat">
  <div class="stat-num" data-target="42000">0</div>
  <p>usuários ativos</p>
</article>
```

```js
// IntersectionObserver + requestAnimationFrame count up
const io = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (!e.isIntersecting) return;
    const el = e.target.querySelector('.stat-num');
    const target = parseInt(el.dataset.target);
    const dur = 1500;
    const start = performance.now();
    const tick = (t) => {
      const p = Math.min((t - start) / dur, 1);
      el.textContent = Math.floor(p * target).toLocaleString('pt-BR');
      if (p < 1) requestAnimationFrame(tick);
    };
    requestAnimationFrame(tick);
    io.unobserve(e.target);
  });
}, { threshold: 0.4 });
document.querySelectorAll('.bento-stat').forEach(el => io.observe(el));
```

## Validations (rodar antes de ship)

- [ ] No mobile, todos os tiles viram coluna única
- [ ] Sem horizontal scroll em viewport 320px
- [ ] Hover state funciona em touch (use `:active` como fallback)
- [ ] Performance: animations em `transform` + `opacity` (composited)
- [ ] Acessibilidade: `:focus-visible` igual ao `:hover` em tiles clicáveis

## Anti-patterns

❌ 4 tiles do mesmo tamanho — vira grid genérico
❌ Tiles com radius diferente (24, 12, 16 misturados)
❌ Hover effect só "scale 1.05" sem mais nada
❌ Texto pequeno (< 14px) dentro de tile pequeno (ilegível)
❌ Padding < 24px (cards apertados)

## Inspirações reais (validar visual antes de prod)

- apple.com (qualquer página de produto, sempre tem bento)
- bentogrids.com (galeria)
- vercel.com homepage
- linear.app (mostra bento em "features")

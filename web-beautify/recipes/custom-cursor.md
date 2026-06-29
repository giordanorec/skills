# Recipe — Custom Cursor

> Cursor customizado substituindo o nativo. Pode ter estados (default,
> hover-link, hover-button, drag), magnetic effect, e seguir mouse com easing.
> **USO RESTRITO**: só em sites de design/portfolio/tools criativos.
> NUNCA em SaaS, e-commerce, ou qualquer site funcional pra audiência ampla.

## Quando usar

- ✅ Portfolio criativo (designer, dev, studio)
- ✅ Site de tool criativa (Figma-like, Spline, etc)
- ✅ Brand site experimental / campaign
- ✅ Award submission (Awwwards / FWA)
- ❌ NÃO use em SaaS B2B, fintech, commerce
- ❌ NÃO use em site informativo
- ❌ NÃO use em mobile (não existe cursor — só ative em `pointer: fine`)
- ❌ NÃO use sem fallback no cursor nativo (acessibilidade)

## HTML

```html
<div class="cursor" aria-hidden="true">
  <div class="cursor-dot"></div>
  <div class="cursor-ring"></div>
</div>
```

## CSS

```css
/* Esconder cursor nativo SÓ em pointer: fine (desktop/laptop) */
@media (pointer: fine) {
  html, body { cursor: none; }
  a, button, [role="button"], input, select, textarea { cursor: none; }
}

.cursor {
  position: fixed;
  top: 0; left: 0;
  pointer-events: none;
  z-index: 9999;
  mix-blend-mode: difference; /* inverte cor sobre qualquer fundo */
}

.cursor-dot {
  position: absolute;
  top: 0; left: 0;
  width: 8px; height: 8px;
  border-radius: 50%;
  background: #fff;
  transform: translate(-50%, -50%);
  will-change: transform;
}

.cursor-ring {
  position: absolute;
  top: 0; left: 0;
  width: 36px; height: 36px;
  border-radius: 50%;
  border: 1.5px solid #fff;
  transform: translate(-50%, -50%);
  will-change: transform;
  transition: width .2s ease, height .2s ease, opacity .2s ease;
}

/* Estados ao hover de elementos interativos */
.cursor.is-hovering-link .cursor-ring {
  width: 56px; height: 56px;
  border-width: 2px;
}
.cursor.is-hovering-button .cursor-ring {
  width: 72px; height: 72px;
  background: rgba(255,255,255,.1);
  border-color: transparent;
}
.cursor.is-clicking .cursor-ring {
  width: 24px; height: 24px;
}

/* Mobile/touch — esconder por completo */
@media (pointer: coarse) {
  .cursor { display: none; }
  html, body { cursor: auto; }
}
```

## JS — Smooth follow + state machine

```js
class CustomCursor {
  constructor() {
    this.cursor = document.querySelector('.cursor');
    this.dot = this.cursor.querySelector('.cursor-dot');
    this.ring = this.cursor.querySelector('.cursor-ring');

    this.mouse = { x: 0, y: 0 };
    this.dotPos = { x: 0, y: 0 };
    this.ringPos = { x: 0, y: 0 };

    this.bindEvents();
    this.tick();
  }

  bindEvents() {
    window.addEventListener('mousemove', (e) => {
      this.mouse.x = e.clientX;
      this.mouse.y = e.clientY;
    });

    // Hover states
    document.querySelectorAll('a, [role="link"]').forEach(el => {
      el.addEventListener('mouseenter', () => this.cursor.classList.add('is-hovering-link'));
      el.addEventListener('mouseleave', () => this.cursor.classList.remove('is-hovering-link'));
    });

    document.querySelectorAll('button, [role="button"]').forEach(el => {
      el.addEventListener('mouseenter', () => this.cursor.classList.add('is-hovering-button'));
      el.addEventListener('mouseleave', () => this.cursor.classList.remove('is-hovering-button'));
    });

    // Click state
    window.addEventListener('mousedown', () => this.cursor.classList.add('is-clicking'));
    window.addEventListener('mouseup',   () => this.cursor.classList.remove('is-clicking'));

    // Sair da janela
    document.addEventListener('mouseleave', () => this.cursor.style.opacity = '0');
    document.addEventListener('mouseenter', () => this.cursor.style.opacity = '1');
  }

  tick() {
    // Dot: instant (segue mouse exato)
    this.dotPos.x = this.mouse.x;
    this.dotPos.y = this.mouse.y;
    this.dot.style.transform = `translate(${this.dotPos.x}px, ${this.dotPos.y}px) translate(-50%, -50%)`;

    // Ring: lerp suave (segue com delay agradável)
    this.ringPos.x += (this.mouse.x - this.ringPos.x) * 0.18;
    this.ringPos.y += (this.mouse.y - this.ringPos.y) * 0.18;
    this.ring.style.transform = `translate(${this.ringPos.x}px, ${this.ringPos.y}px) translate(-50%, -50%)`;

    requestAnimationFrame(this.tick.bind(this));
  }
}

// Só ativa em devices com pointer fine
if (matchMedia('(pointer: fine)').matches) {
  new CustomCursor();
}
```

## Variant — Magnetic CTA

CTAs "puxam" o cursor pra dentro quando ele chega perto:

```js
document.querySelectorAll('.magnetic').forEach(el => {
  el.addEventListener('mousemove', (e) => {
    const rect = el.getBoundingClientRect();
    const cx = rect.left + rect.width / 2;
    const cy = rect.top + rect.height / 2;
    const dx = (e.clientX - cx) * 0.3;
    const dy = (e.clientY - cy) * 0.3;
    el.style.transform = `translate(${dx}px, ${dy}px)`;
  });
  el.addEventListener('mouseleave', () => {
    el.style.transform = 'translate(0, 0)';
  });
});
```

```css
.magnetic { transition: transform .25s cubic-bezier(.2,.8,.2,1); }
```

## Variant — Cursor com label contextual

Cursor mostra texto quando hover em elemento específico ("clique pra ver", "drag", etc):

```html
<div class="cursor">
  <div class="cursor-dot"></div>
  <div class="cursor-ring"></div>
  <span class="cursor-label"></span>
</div>

<a href="/video" data-cursor-label="play">Vídeo</a>
```

```js
document.querySelectorAll('[data-cursor-label]').forEach(el => {
  el.addEventListener('mouseenter', () => {
    this.cursor.querySelector('.cursor-label').textContent = el.dataset.cursorLabel;
    this.cursor.classList.add('has-label');
  });
  el.addEventListener('mouseleave', () => {
    this.cursor.classList.remove('has-label');
  });
});
```

```css
.cursor-label {
  position: absolute;
  top: 0; left: 0;
  transform: translate(20px, -50%);
  font-size: 13px;
  font-weight: 600;
  color: #fff;
  white-space: nowrap;
  opacity: 0;
  transition: opacity .2s ease;
}
.cursor.has-label .cursor-label { opacity: 1; }
```

## Performance

- `requestAnimationFrame` loop = 1 frame budget (~0.5ms)
- `transform` only (composited) — não causa layout
- `pointer-events: none` no cursor evita interferir com clicks
- Lerp factor 0.15-0.2 = sweet spot (mais alto = rigid, menos = laggy)

## Acessibilidade

- ⚠️ **Crítico**: ALWAYS provide cursor nativo como fallback. Hide nativo SÓ em `pointer: fine`.
- ⚠️ `aria-hidden="true"` no cursor element (não é conteúdo)
- ⚠️ Keyboard navigation segue funcionando — focus visible inalterado
- ⚠️ `prefers-reduced-motion: reduce` deve desligar:
  ```css
  @media (prefers-reduced-motion: reduce) {
    html, body, a, button { cursor: auto; }
    .cursor { display: none; }
  }
  ```
- ⚠️ Permitir toggle UI pra desligar custom cursor (em sites longos)

## Anti-patterns

❌ Custom cursor em site funcional sério (SaaS, fintech, commerce) — usuário se irrita
❌ Cursor sem fallback nativo em mobile (toque vê área vazia)
❌ Lerp muito lento (cursor "atrás" do mouse causa fricção)
❌ Estados que não correspondem a affordance real (label "click" em coisa que não clica)
❌ Cursor que esconde info importante (cobre texto)
❌ Cursor sem `mix-blend-mode` em backgrounds variados (some)
❌ Esquecer `pointer: coarse` media query (mobile pega cursor invisível)
❌ Sem `prefers-reduced-motion` desligando

## Inspirações reais

- bruno-simon.com (cursor + 3D world)
- humaan.com (magnetic CTAs)
- locomotive.ca (label contextual)
- studio-rauda.com (cursor inverso)
- madebyhusky.com (custom cursor sutil)
- darius.studio (state machine completo)

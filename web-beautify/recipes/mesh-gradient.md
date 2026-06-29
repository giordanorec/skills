# Recipe — Mesh Gradient (animado)

> Background com blobs flutuantes que dão "depth" sem image. Trademark
> de Stripe, adotado por Vercel, Linear, Notion. Custo: zero JS.

## Quando usar

- ✅ Hero section (background atmosférico)
- ✅ CTA band (chama atenção sem CTA gritante)
- ✅ Section transition (separa contextualmente)
- ❌ NÃO use em todo o body (overwhelming, perf hit)
- ❌ NÃO use com 5+ blobs (ruído)
- ❌ NÃO use sem `prefers-reduced-motion: reduce` desligar animação

## HTML

```html
<section class="hero">
  <div class="mesh-blob mesh-blob-1" aria-hidden="true"></div>
  <div class="mesh-blob mesh-blob-2" aria-hidden="true"></div>
  <div class="mesh-blob mesh-blob-3" aria-hidden="true"></div>
  <div class="hero-inner">
    <!-- conteúdo do hero -->
  </div>
</section>
```

## CSS — versão clássica (3 blobs)

```css
.hero {
  position: relative;
  overflow: hidden;
  isolation: isolate; /* mantém blend-modes scoped */
  background: var(--bg);
  padding: 96px 24px;
}

.mesh-blob {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  opacity: .55;
  z-index: 0;
  pointer-events: none;
  animation: meshFloat 18s ease-in-out infinite;
}

/* Cores DEVEM vir da DESIGN.md âncora — NUNCA hex fallback (forçar erro visual se var
   não foi definida, pra agente lembrar de ancorar). Se quiser sandbox sem âncora,
   defina `--mesh-1/2/3` no `:root` explicitamente do projeto. */
.mesh-blob-1 {
  width: 520px; height: 520px;
  background: var(--mesh-1);
  top: -120px; left: -100px;
}
.mesh-blob-2 {
  width: 460px; height: 460px;
  background: var(--mesh-2);
  bottom: -180px; right: -60px;
  animation-delay: -6s;
}
.mesh-blob-3 {
  width: 380px; height: 380px;
  background: var(--mesh-3);
  top: 30%; left: 35%;
  opacity: .35;
  animation-delay: -12s;
}

.hero-inner {
  position: relative;
  z-index: 1; /* conteúdo acima dos blobs */
}

@keyframes meshFloat {
  0%, 100% { transform: translate(0, 0) scale(1); }
  33%      { transform: translate(40px, -30px) scale(1.05); }
  66%      { transform: translate(-30px, 20px) scale(.95); }
}

@media (prefers-reduced-motion: reduce) {
  .mesh-blob { animation: none; }
}

/* MOBILE — reduzir blur (80-120px em mobile low-end causa 15-25ms rasterize) */
@media (max-width: 720px) {
  .mesh-blob { filter: blur(40px); opacity: .4; }
}

/* Forced colors — desligar */
@media (forced-colors: active) {
  .mesh-blob { display: none; }
}
```

## Princípios pra mesh ficar bom

1. **Cores da brand**: NUNCA cores random — sempre vem do `--mesh-*` definido pela DESIGN.md âncora
2. **Blur generoso**: 60-120px (menos = parece círculo, mais = parece washed out)
3. **Opacity 0.3-0.7**: 100% domina o conteúdo, < 20% some
4. **3-5 blobs max**: mais = caos
5. **Posicionamento estratégico**: blobs nas BORDAS, não no centro (deixa centro pra conteúdo)
6. **`pointer-events: none`**: nunca interceptar clicks
7. **`aria-hidden="true"`**: leitor de tela não precisa "ver"

## Variant — Mesh com blend-mode (Stripe-style)

Pra mesh mais sofisticada onde blobs se misturam visualmente:

```css
.mesh-blob { mix-blend-mode: multiply; }

/* Em dark theme: */
[data-theme="dark"] .mesh-blob { mix-blend-mode: screen; }
```

`multiply` em light bg = blobs ficam mais escuros onde se sobrepõem
`screen` em dark bg = blobs ficam mais claros onde se sobrepõem

## Variant — Mesh via conic-gradient (sem blobs HTML)

Pra economizar elementos:

```css
.hero {
  background:
    conic-gradient(
      from 0deg at 30% 30%,
      var(--mesh-1), var(--mesh-2), var(--mesh-3), var(--mesh-1)
    );
  filter: blur(60px) saturate(1.2);
  /* problema: filter no parent afeta filhos. Use ::before */
}

.hero::before {
  content: '';
  position: absolute; inset: -10%;
  background: conic-gradient(/* ... */);
  filter: blur(60px);
  z-index: -1;
}
```

## Variant — Mesh WebGL (Tier 4 only)

Pra mesh REAL (não fake CSS), use shader Three.js:

```js
import { ShaderMaterial, Mesh, PlaneGeometry } from 'three';

const material = new ShaderMaterial({
  uniforms: {
    uTime: { value: 0 },
    uColor1: { value: new Color('#ff7a52') },
    uColor2: { value: new Color('#ffb59a') },
    uColor3: { value: new Color('#ffd39a') },
  },
  fragmentShader: /* glsl */ `
    uniform float uTime;
    uniform vec3 uColor1, uColor2, uColor3;
    varying vec2 vUv;
    void main() {
      float t = uTime * 0.1;
      vec3 c = mix(uColor1, uColor2, sin(vUv.x * 3.0 + t) * 0.5 + 0.5);
      c = mix(c, uColor3, sin(vUv.y * 4.0 + t * 1.3) * 0.5 + 0.5);
      gl_FragColor = vec4(c, 1.0);
    }
  `,
  vertexShader: /* glsl */ `
    varying vec2 vUv;
    void main() {
      vUv = uv;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
    }
  `,
});
```

**Use only se Tier 4** — adiciona ~50KB Three.js gzip + complexidade.

## Performance

- CSS version: ~zero perf hit (GPU compositing)
- Mas `blur()` pesado em mobile low-end → considerar `transform: translateZ(0)` pra forçar GPU
- Sempre teste: instale Chrome DevTools "Performance Monitor", veja se rasterize time > 10ms

## Acessibilidade

- `aria-hidden="true"` em blobs (decorativos)
- `pointer-events: none` (não bloqueiam click)
- `prefers-reduced-motion: reduce` desliga animação
- Contraste de conteúdo: garantir que texto sobre o mesh continua AA (4.5:1)

## Anti-patterns

❌ Blobs com cores random sem relação com brand
❌ Mais de 5 blobs (caos)
❌ Blobs no centro do hero (compete com conteúdo)
❌ Blur < 40px (parece círculo, não blob)
❌ Opacity 100% (domina, esconde conteúdo)
❌ Animação > 30s (lenta demais, parece travado)
❌ Animação < 8s (rápido demais, distrai)
❌ Sem reduced-motion fallback
❌ Cores neutras + blur = parece manchada/poluída

## Inspirações reais

- stripe.com homepage (hero gradient mesh é trademark)
- vercel.com (mesh nos heros de produto)
- linear.app (mesh sutil em CTA)
- notion.so/ai (mesh dramática)

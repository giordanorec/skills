# Recipe — Marquee Infinito (Ticker)

> Linha de conteúdo rolando infinitamente. Trademark de sites premium pra
> mostrar "social proof" (logos de clientes), tagline repetida em fonte grande,
> ou conjunto de meme phrases.

## Quando usar

- ✅ Logo bar de clientes (Stripe, Notion, etc)
- ✅ Tagline manifesto em fonte grande (statement)
- ✅ Lista de features rápida sem precisar grid
- ✅ Decoração tipográfica (texto repetido em fonte massiva)
- ❌ NÃO use pra conteúdo que precisa ser lido (não há tempo)
- ❌ NÃO use sem pausa no hover (usuário não consegue interagir)
- ❌ NÃO use mais que 1 marquee por viewport (cansa)

## HTML

```html
<section class="marquee" aria-hidden="true">
  <div class="marquee-track">
    <span class="marquee-item">sabor amizade</span>
    <span class="marquee-sep">·</span>
    <span class="marquee-item">sabor energético</span>
    <span class="marquee-sep">·</span>
    <span class="marquee-item">sabor namorado</span>
    <span class="marquee-sep">·</span>
    <span class="marquee-item">sabor rolê</span>
    <span class="marquee-sep">·</span>
    <!-- DUPLICAR o conteúdo pra loop seamless -->
    <span class="marquee-item">sabor amizade</span>
    <span class="marquee-sep">·</span>
    <span class="marquee-item">sabor energético</span>
    <span class="marquee-sep">·</span>
    <span class="marquee-item">sabor namorado</span>
    <span class="marquee-sep">·</span>
    <span class="marquee-item">sabor rolê</span>
    <span class="marquee-sep">·</span>
  </div>
</section>
```

⚠️ Conteúdo DUPLICADO é crítico — sem isso o loop "salta" no final.

## CSS

```css
.marquee {
  background: var(--bg-deep, #1a1a1a);
  color: var(--text-on-deep, #fff);
  padding: 24px 0;
  overflow: hidden;
  position: relative;
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
}

.marquee-track {
  display: inline-flex;
  gap: 32px;
  white-space: nowrap;
  font-family: var(--font-display);
  font-weight: 800;
  font-size: clamp(28px, 4vw, 48px);
  letter-spacing: -.025em;
  animation: marqueeScroll 38s linear infinite;
  will-change: transform;
}

.marquee-item { padding: 0 4px; }
.marquee-item:nth-child(4n+1) { color: var(--accent); }
.marquee-sep { opacity: .5; }

@keyframes marqueeScroll {
  from { transform: translateX(0); }
  to   { transform: translateX(-50%); } /* -50% pq duplicamos */
}

/* Pausa no hover (UX, permite ler) */
.marquee:hover .marquee-track {
  animation-play-state: paused;
}

@media (prefers-reduced-motion: reduce) {
  .marquee-track { animation: none; }
}
```

## Velocidades calibradas

| Conteúdo | Duration ideal |
|---|---|
| Logos pequenos (40px alt) | 25-30s |
| Logos grandes | 40-50s |
| Frases curtas (fonte 18px) | 22-28s |
| Texto grande (fonte 48px) | 35-45s |
| Texto massivo (fonte 96px+) | 50-60s |

Regra: 100-150px de conteúdo por segundo. Mais rápido distrai, mais lento parece travado.

## Variant — Marquee duplo (camadas opostas)

```html
<div class="marquee-double">
  <div class="marquee-row marquee-row-1">
    <!-- conteúdo 1 -->
  </div>
  <div class="marquee-row marquee-row-2">
    <!-- conteúdo 2 -->
  </div>
</div>
```

```css
.marquee-row-1 { animation: marqueeScroll 38s linear infinite; }
.marquee-row-2 {
  animation: marqueeScroll 42s linear infinite reverse;
  opacity: .6;
  margin-top: -8px;
}
```

Camada 2 mais lenta + opacity reduzida + sentido contrário = depth visual.

## Variant — Marquee com fade nas bordas

Pra suavizar a entrada/saída de conteúdo na borda do viewport:

```css
.marquee {
  mask-image: linear-gradient(
    90deg,
    transparent 0%,
    black 10%,
    black 90%,
    transparent 100%
  );
  -webkit-mask-image: linear-gradient(
    90deg,
    transparent 0%,
    black 10%,
    black 90%,
    transparent 100%
  );
}
```

## Variant — Marquee com logos de clientes

```html
<section class="marquee-logos" aria-label="Clientes">
  <h2 class="sr-only">Clientes</h2>
  <div class="marquee-track">
    <img src="/logos/stripe.svg" alt="Stripe" class="client-logo">
    <img src="/logos/vercel.svg" alt="Vercel" class="client-logo">
    <img src="/logos/linear.svg" alt="Linear" class="client-logo">
    <!-- duplicar -->
    <img src="/logos/stripe.svg" alt="" class="client-logo" aria-hidden="true">
    <img src="/logos/vercel.svg" alt="" class="client-logo" aria-hidden="true">
    <img src="/logos/linear.svg" alt="" class="client-logo" aria-hidden="true">
  </div>
</section>
```

```css
.client-logo {
  height: 32px;
  width: auto;
  filter: grayscale(1);
  opacity: .6;
  transition: filter .2s ease, opacity .2s ease;
}
.client-logo:hover {
  filter: grayscale(0);
  opacity: 1;
}
```

## Variant — Marquee CSS-only sem JS (mais simples)

Versão minimalista (sem hover-pause):

```html
<div class="marquee-simple">
  <div class="marquee-simple-content">Texto repetido · Texto repetido · Texto repetido ·</div>
</div>
```

```css
.marquee-simple { overflow: hidden; width: 100%; }
.marquee-simple-content {
  display: inline-block;
  white-space: nowrap;
  padding-left: 100%; /* começa fora da tela à direita */
  animation: marqueeSimple 20s linear infinite;
}
@keyframes marqueeSimple {
  to { transform: translateX(-100%); }
}
```

Funciona mas tem "buraco" entre repetições — ok pra cases simples.

## Performance

- Use `will-change: transform` no `.marquee-track`
- Use `transform` (composited), nunca `left`/`margin-left` (layout thrash)
- Conteúdo deve caber em RAM facilmente — não use 1000 items
- Se usar imagens, lazy-load não funciona (todas precisam estar carregadas)
- Em mobile, considere DESLIGAR marquee (pause no `@media (max-width: 600px)`)

## Acessibilidade

- `aria-hidden="true"` no marquee se for puramente decorativo
- Pausar no hover (já incluído)
- `prefers-reduced-motion: reduce` desliga animation
- Se conteúdo é importante (logos clientes), adicionar `<h2 class="sr-only">` e descrição estática alternativa

## Anti-patterns

❌ Velocidade muito rápida (não dá tempo de ler)
❌ Sem duplicação do conteúdo (loop tem jump visível)
❌ Marquee em conteúdo que precisa ser lido (CTA, instructions)
❌ Múltiplos marquees no mesmo viewport (caos)
❌ Marquee sem hover-pause E sem reduced-motion (acessibility fail)
❌ Cores chamativas em fonte massiva (overwhelming)

## Inspirações reais

- stripe.com (logo marquee no homepage)
- attentive.com (frases manifesto em marquee)
- ramp.com (logos no homepage)
- numerous portfolios (marquee tipográfica)

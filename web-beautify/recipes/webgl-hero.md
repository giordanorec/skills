# Recipe — WebGL Hero (Three.js / R3F)

> Hero com cena 3D / shader interativo. Trademark de top studios (Active
> Theory, Immersive Garden, Lusion). **USO PREMIUM**: Tier 4 only, e sempre
> com performance budget rigoroso + fallback mobile.

## Quando usar

- ✅ Brand campaign / launch experience
- ✅ Portfolio criativo (designer, dev, studio)
- ✅ Site experimental / award submission
- ✅ Cliente explicitamente quer "experience", não "site"
- ❌ NÃO use em SaaS / commerce / informational
- ❌ NÃO use sem performance budget verificado em mobile low-end
- ❌ NÃO use sem fallback estático (alguns devices não rodam WebGL)
- ❌ NÃO use mais de 1 cena WebGL por página

## Stack

- **Three.js** (vanilla) — controle máximo
- **React Three Fiber (R3F) + drei** — se projeto React
- **GLSL shaders** — pra noise, distortion, atmospheric effects

## Variant A — R3F minimal (recomendado pra Tier 4)

```bash
npm install three @react-three/fiber @react-three/drei
```

```jsx
'use client';
import { Canvas, useFrame } from '@react-three/fiber';
import { Environment, Float } from '@react-three/drei';
import { useRef } from 'react';
import * as THREE from 'three';

function FloatingOrb() {
  const ref = useRef();
  useFrame((state, delta) => {
    ref.current.rotation.y += delta * 0.2;
    ref.current.position.y = Math.sin(state.clock.elapsedTime) * 0.2;
  });
  return (
    <Float speed={1.5} rotationIntensity={1} floatIntensity={1.2}>
      <mesh ref={ref}>
        <icosahedronGeometry args={[1.5, 4]} />
        <meshPhysicalMaterial
          color="#5e6ad2"
          roughness={0.1}
          metalness={0.4}
          clearcoat={1}
          clearcoatRoughness={0}
          transmission={0.7}
          thickness={1.2}
          ior={1.5}
        />
      </mesh>
    </Float>
  );
}

export function HeroWebGL() {
  return (
    <div className="webgl-hero">
      <Canvas
        camera={{ position: [0, 0, 5], fov: 35 }}
        gl={{ antialias: true, alpha: true }}
        dpr={[1, 2]}
      >
        <ambientLight intensity={0.4} />
        <pointLight position={[5, 5, 5]} intensity={1.2} />
        <FloatingOrb />
        <Environment preset="studio" />
      </Canvas>
      <div className="webgl-hero-content">
        <h1>Não é só design.<br/>É o sabor.</h1>
        <button>começar →</button>
      </div>
    </div>
  );
}
```

```css
.webgl-hero {
  position: relative;
  height: 100svh;
  background: #0a0a0a;
}
.webgl-hero canvas { position: absolute; inset: 0; }
.webgl-hero-content {
  position: relative; z-index: 1;
  display: flex; flex-direction: column;
  justify-content: center; align-items: center;
  height: 100%; color: #fff; text-align: center;
}
```

## Variant B — Shader gradient mesh REAL (não fake CSS)

Pra mesh genuinamente animado com noise simplex:

```jsx
const frag = /* glsl */ `
  uniform float uTime;
  uniform vec3 uColor1, uColor2, uColor3;
  varying vec2 vUv;
  // Simplex noise omitted for brevity — use lygia/generative ou paste snippet
  void main() {
    float t = uTime * 0.15;
    float n1 = snoise(vUv * 2.0 + vec2(t, t));
    float n2 = snoise(vUv * 3.0 - vec2(t * 0.7, t * 1.3));
    vec3 c = mix(uColor1, uColor2, n1 * 0.5 + 0.5);
    c = mix(c, uColor3, n2 * 0.5 + 0.5);
    gl_FragColor = vec4(c, 1.0);
  }
`;
```

Cores devem vir da DESIGN.md âncora.

## Variant C — Distortion no scroll

Imagem que distorce conforme scroll:

```jsx
import { useScroll } from '@react-three/drei';
// shader com displacement baseado em scroll progress
```

Stack: R3F + drei `useScroll` + custom shader.

## Performance budgets (não-negociável)

| Métrica | Budget |
|---|---|
| Three.js bundle gzip | < 100KB |
| FPS desktop | ≥ 60 |
| FPS mobile médio | ≥ 30 |
| Draw calls | < 60 |
| Polygons | < 100k |
| Texture memory | < 50MB |
| LCP (hero visible) | < 4s |

Se algum estoura → simplificar OU fallback estático.

## Fallback obrigatório

```jsx
import { useEffect, useState } from 'react';

function HeroWithFallback() {
  const [ok, setOk] = useState(true);

  useEffect(() => {
    // WebGL check
    try {
      const c = document.createElement('canvas');
      const gl = c.getContext('webgl2') || c.getContext('webgl');
      if (!gl) setOk(false);
    } catch {
      setOk(false);
    }
    // Reduced motion check
    if (matchMedia('(prefers-reduced-motion: reduce)').matches) setOk(false);
    // Low-end device check
    if (navigator.hardwareConcurrency < 4) setOk(false);
  }, []);

  return ok ? <HeroWebGL /> : <StaticHero />;
}
```

`StaticHero` deve ser CSS-only — mesh gradient da recipe `mesh-gradient.md` é boa alternativa.

## Otimizações chave

1. **DPR cap**: `dpr={[1, 2]}` (retinas 3x destroem mobile)
2. **Texture compression**: KTX2 ou Basis Universal (reduz 70%)
3. **Geometry merging**: combine static meshes
4. **Suspense + lazy import**: load só quando visible
5. **Pause when offscreen**: `useFrame` só roda se hero no viewport
6. **Tone mapping**: ACESFilmic é o default OK

```jsx
import { Suspense, lazy } from 'react';
const HeroWebGL = lazy(() => import('./HeroWebGL'));

<Suspense fallback={<StaticHero />}>
  <HeroWebGL />
</Suspense>
```

## Acessibilidade

- ⚠️ Canvas é **decorativo** pro SR — `aria-hidden="true"` no Canvas
- ⚠️ Conteúdo textual sempre em DOM (nunca dentro do canvas)
- ⚠️ Keyboard navigation: focus permanece nos elementos HTML
- ⚠️ `prefers-reduced-motion: reduce` → fallback estático mandatório
- ⚠️ Loading state visível enquanto Canvas importa (skeleton/placeholder)

## Anti-patterns

❌ WebGL sem fallback (mobile vê tela preta)
❌ Multiple WebGL canvases por página (cada um custa GPU)
❌ Texturas grandes sem compression
❌ `useFrame` rodando offscreen (drain bateria)
❌ Esquecer `aria-hidden` no canvas
❌ Sem hardware detection (low-end trava)
❌ Hero WebGL em SaaS/commerce (incoerente com função)

## Inspirações reais

- activetheory.net (hero 3D worlds)
- immersive-g.com (shader heroes)
- lusion.co (3D portfolio)
- bruno-simon.com (Three.js + physics)
- humaan.com (WebGL sutil)
- madewithgsap.com (galeria GSAP+WebGL)
- threejs.org/examples (canon)

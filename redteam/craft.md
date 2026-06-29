# Red Team Craft — verdict

**Verdict**: needs_revision
**Score**: 68/100

A skill tem fundamento real (cita Refactoring UI, Practical Typography, prescreve escala de espaçamento explícita, ensina sombras em camadas no `redteam-craft.md`). Mas o ensinamento NÃO se propaga consistentemente pras recipes — várias delas contradizem as próprias regras que o critic CRAFT iria aplicar. Há também uma contradição interna sobre fontes banidas (skill bane Inter no SKILL.md, mas usa Inter como fallback em recipe) e o ensinamento de "violar regras quando DESIGN.md âncora pede" está implícito mas nunca explicitado num lugar canônico.

## Violations (em ordem de severidade)

### 1. critical — contradição interna sobre fonte banida
- **severity**: critical
- **rule**: Anti-AI-slop hardcoded — "NUNCA use Inter, Roboto, Arial, Helvetica, Space Grotesk SEM justificativa explícita da DESIGN.md âncora" (SKILL.md:133)
- **where**: `recipes/glassmorphism-nav.md:76` e `recipes/glassmorphism-nav.md:75`
- **found**: `font-family: var(--font-display, 'Inter', sans-serif);` — fallback de Inter HARDCODED no exemplo canônico que o agente vai copy-paste
- **expected**: fallback neutro tipo `system-ui, -apple-system, 'Segoe UI', sans-serif` OU deixar SÓ a var sem fallback proibido. Quem ler essa recipe e copiar literalmente vai injetar Inter no projeto contra a regra do próprio SKILL.md
- **fix**: trocar pra `font-family: var(--font-display, system-ui, sans-serif);` em TODAS recipes. Adicionar checkbox no Validations da recipe: "[ ] nenhum fallback usa fonte banida (Inter/Roboto/Arial/Helvetica/Space Grotesk)"

### 2. critical — recipe bento contradiz a regra de "sombra em camadas" do critic
- **severity**: critical
- **rule**: redteam-craft.md:70-78 explicitamente bane `box-shadow: 0 4px 6px rgba(0,0,0,.1)` como AI slop e prescreve 2-3 sombras combinadas
- **where**: `recipes/bento-grid.md:105-107` (sombra base do tile)
- **found**:
  ```css
  box-shadow:
    0 0 0 1px rgba(0,0,0,.02),
    0 2px 6px rgba(0,0,0,.04);
  ```
  Tecnicamente são 2 camadas, mas a 1ª é inset-ring de 0 spread — não é "depth", é só border emulada. Efeito final é UMA sombra real (0 2px 6px). É o AI slop disfarçado.
- **expected**: 3 camadas REAIS de depth (close shadow + mid shadow + ambient), como o próprio `redteam-craft.md:73-78` exemplifica:
  ```css
  box-shadow:
    0 0 0 1px rgba(0,0,0,.02),
    0 2px 6px rgba(0,0,0,.04),
    0 4px 8px rgba(0,0,0,.06);
  ```
- **fix**: substituir a sombra base do bento-tile pelo exemplo do redteam-craft.md (3 camadas) — recipe e critic precisam estar sincronizados, senão o agente cria tile que o critic vai rejeitar.

### 3. high — recipes não prescrevem tracking/line-height/weight ESPECÍFICOS pra display vs body
- **severity**: high
- **rule**: redteam-craft.md:36-40 demanda tracking display `-.02em a -.04em`, line-height display `1.05-1.2`, body `1.5-1.6`, weights variando 400/600/800
- **where**: `recipes/kinetic-typography.md` inteira; `recipes/glassmorphism-nav.md:74-79`; `recipes/mesh-gradient.md` (zero spec de typography)
- **found**: kinetic-typography.md NÃO especifica `font-size`, `font-weight`, `letter-spacing`, `line-height` pro `.hero-title` em LUGAR NENHUM. Recipe ensina movimento mas omite a tipografia debaixo do movimento. Já `glassmorphism-nav.md:74-79` define só o brand-title (`letter-spacing: -.025em, font-weight: 800` — OK isolado) mas nada pros links `.header-nav a` exceto `font-size: 14px; font-weight: 500;` sem tracking.
- **expected**: cada recipe que envolva texto deve declarar EXPLICITAMENTE os 4 axes (size/weight/tracking/leading) pra cada role. Bento-grid acerta isso (linha 67-72 e 129-135) — usar como template.
- **fix**: adicionar bloco "Typography spec" em cada recipe:
  ```css
  .hero-title {
    font-size: clamp(48px, 7vw, 96px);
    font-weight: 800;
    letter-spacing: -.035em;
    line-height: 1.02;
  }
  ```

### 4. high — mesh-gradient prescreve cores fallback que VIOLAM "use cor da DESIGN.md âncora"
- **severity**: high
- **rule**: o próprio mesh-gradient.md:50, 87 ("Cores devem vir da DESIGN.md âncora — NÃO inventar") e SKILL.md:136 ("Use cor que não vem de var/token… zero hex hardcoded")
- **where**: `recipes/mesh-gradient.md:52, 56, 62`
- **found**: `background: var(--mesh-1, #ffb59a);` — fallback hardcoded com cor inventada (orange-pêssego que é uma paleta específica, não neutra). Se o projeto não setar `--mesh-1`, a recipe injeta paleta inventada que viola "cores da brand".
- **expected**: SEM fallback hex, ou um aviso `/* ERRO: --mesh-1 não definido */` que falha visualmente. Alternativa: comentário no topo "REQUIRED: definir --mesh-1, --mesh-2, --mesh-3 antes de usar essa recipe".
- **fix**: remover fallbacks hex. Trocar por `background: var(--mesh-1);` e adicionar guard CSS:
  ```css
  /* Pre-flight: estas vars TÊM que ser definidas a partir da DESIGN.md âncora */
  :root { --mesh-1: ; --mesh-2: ; --mesh-3: ; } /* obrigatório override */
  ```

### 5. high — skill não ensina QUANDO violar as próprias regras (escape hatch pra DESIGN.md âncora)
- **severity**: high
- **rule**: realismo de design system — Refactoring UI ensina principles, mas sites reais (Carbon usa Inter porque IBM Plex Sans falhou em pequenos tamanhos; Material usa Roboto) violam principles por motivo válido
- **where**: SKILL.md inteira; `redteam-craft.md:32-34`
- **found**: A única menção é em `redteam-craft.md:33` ("Exceção: a DESIGN.md âncora explicitamente especifica"). Mas o critic não tem heurística pra DECIDIR se a exceção é válida. Resultado: agente vai aprovar Inter sempre que vir "Inter" na DESIGN.md, sem checar se foi escolha fundamentada.
- **expected**: seção "Quando a DESIGN.md âncora autoriza violação" em SKILL.md + checklist no redteam-craft.md tipo "se a DESIGN.md especifica fonte banida, exigir RATIONALE explícito na própria DESIGN.md (não só listar a fonte)".
- **fix**: adicionar em SKILL.md uma seção "Anchor-override rules":
  > Se a DESIGN.md âncora especifica uma fonte/cor da lista banida, exija que a âncora documente o RACIONAL (ex: "IBM Carbon usa Inter porque IBM Plex Sans falha em ≤12px"). Sem racional, a violação não é autorizada — escolher outra âncora.

### 6. high — kinetic-typography não prescreve tracking negativo apesar de o próprio doc ensinar isso
- **severity**: high
- **rule**: 03_differentiation_techniques.md:38 — "Tracking negativo agressivo (-3% a -4%) em displays grandes"
- **where**: `recipes/kinetic-typography.md` (procurar `letter-spacing`)
- **found**: ZERO menção a `letter-spacing` em kinetic-typography.md. A recipe ensina o movimento (Variants A-E) mas omite o tracking que é EXATAMENTE o que diferencia kinetic premium (Stripe/Linear) de kinetic AI-slop (palavra ciclando com tracking padrão).
- **expected**: cada variant precisa especificar tracking. Hero kinetic ≥48px = `letter-spacing: -.035em` mínimo.
- **fix**: adicionar no topo do CSS de cada Variant:
  ```css
  .hero-title { letter-spacing: -.035em; font-variation-settings: 'wght' 700; }
  ```

### 7. medium — falta escala de espaçamento canônica no SKILL.md (só aparece no critic)
- **severity**: medium
- **rule**: Refactoring UI cap. 3 — escala de spacing como token, não livre
- **where**: SKILL.md (não tem seção de tokens); 06_books_guides.md:108 cita whitespace mas não escala; redteam-craft.md:55 lista escala (4,8,12,16,24,32,48,64,96) mas só lá
- **found**: a escala 4/8/12/16/24/32/48/64/96 aparece SÓ no critic, não no SKILL.md ou em "Hard rules". Agente que não invocar o critic não sabe que escala usar.
- **expected**: Hard rules do SKILL.md deveriam listar:
  > Spacing scale: 4, 8, 12, 16, 24, 32, 48, 64, 96, 128. Padding/margin/gap SÓ valores dessa lista. Sem 7px, 14px, 22px, 18px.
- **fix**: adicionar em SKILL.md section "Hard rules" uma subsection "Spacing scale" com a sequência canônica.

### 8. medium — radius inconsistente entre recipes
- **severity**: medium
- **rule**: 06_books_guides.md:106 — "Bordas suaves OU duras, escolhe um vibe — 0px ou 8-12px ou 24px+, não 2-4px"
- **where**: bento-grid.md:101 usa `border-radius: 24px`; 03_differentiation_techniques.md:59 (Bento 2.0) prescreve "Radius generoso (16-28px)"; glassmorphism-nav.md não fala de radius do header; redteam-craft.md:83 diz "0 OU 8+ OU 16+ OU pill"
- **found**: três specs de radius diferentes pra Bento (24px na recipe, 16-28px no diff_techniques, 16-24 em outro). E nenhuma menção de qual a SCALE canônica do projeto.
- **expected**: definir SCALE de radius no SKILL.md (ex: 0, 8, 16, 24, 9999/pill) e cada recipe escolhe DA scale, não inventa.
- **fix**: SKILL.md ganha seção "Radius scale: 0 | 8 | 12 | 16 | 24 | 9999. Mix max 2 tamanhos por projeto."

### 9. medium — bento-grid.h2 prescreve weight 800 sem justificar (e sem variar)
- **severity**: medium
- **rule**: redteam-craft.md:39 — "Peso único: tudo `font-weight: 600`. Hierarquia precisa variar 400/600/800"
- **where**: `recipes/bento-grid.md:69`, `recipes/bento-grid.md:131`, `recipes/glassmorphism-nav.md:78`
- **found**: TODOS displays usam `font-weight: 800` (bento h2, bento tile h3, brand-title). Não há variação por hierarquia.
- **expected**: H2 do bento pode ser 800; mas h3 do tile poderia ser 700; brand do header poderia ser 700 (display de chrome) vs 800 (display de hero). Variação cria depth visual.
- **fix**: prescrever weights por role: hero h1=800, section h2=700, card h3=600, brand=700.

### 10. medium — glassmorphism-nav sombra ausente (só border-bottom)
- **severity**: medium
- **rule**: 03_differentiation_techniques.md:89 — "Sombra muito sutil (não passa de `0 1px 4px rgba(0,0,0,.04)`)"
- **where**: `recipes/glassmorphism-nav.md:40-53`
- **found**: o `.site-header` tem `border-bottom: 1px solid var(--border, rgba(0,0,0,.08));` mas ZERO `box-shadow`. Diff_techniques diz que glass deve ter sombra muito sutil.
- **expected**: adicionar `box-shadow: 0 1px 4px rgba(0,0,0,.04);` (consistente com diff_techniques) ou explicar por que omitiu.
- **fix**: adicionar sombra sutil + nota explicando que é OPCIONAL dependendo do contexto (em paginas com hero mesh-gradient, sombra pode somar com mesh e poluir; em paginas brancas, sombra ajuda separação).

### 11. medium — falta de "alternatives concretas" às fontes banidas
- **severity**: medium
- **rule**: regra ideal da skill — banir defaults E sugerir alternativas
- **where**: SKILL.md:133, 03_differentiation_techniques.md:9-10, redteam-craft.md:32-34
- **found**: redteam-craft.md:34 menciona "Geist, Plus Jakarta Sans, Söhne, custom" como alternativas, mas é o ÚNICO lugar. SKILL.md só bane sem dizer o que usar.
- **expected**: tabela ou lista em SKILL.md tipo:
  > Display alternatives: Geist, Söhne, Plus Jakarta Sans, Söhne Mono, Tobias, Migra, GT Walsheim, Recursive, FH Oscar, Author.
  > Body alternatives: Geist, Söhne, Newsreader (serif), GT America, Untitled Sans, ABC Diatype.
- **fix**: adicionar em SKILL.md section "Fontes recomendadas" com pelo menos 6 alternativas por categoria (display sans, display serif, body sans, body serif, mono).

### 12. low — line-length não verificada em recipe nenhuma
- **severity**: low
- **rule**: 06_books_guides.md:16 — "line-length 45-90 caracteres"; redteam-craft.md:40
- **where**: nenhuma recipe checa max-width de parágrafos
- **found**: bento-grid.md:139 só especifica `font-size: 15px` pra `.bento-tile p` mas não max-width / max-ch.
- **expected**: parágrafos de body devem ter `max-width: 65ch` (ou similar).
- **fix**: adicionar `max-width: 65ch;` em estilos de body p nas recipes.

### 13. low — cores por papel não documentadas como sistema
- **severity**: low
- **rule**: Refactoring UI cap. 5 — cor com papel, escala tonal
- **where**: SKILL.md não documenta tokens de cor canônicos
- **found**: vars `--accent`, `--accent-2`, `--accent-3`, `--mesh-1..3`, `--ink`, `--muted`, `--bg`, `--bg-alt`, `--card`, `--border` aparecem espalhadas, mas nenhum lugar lista o "color system mínimo" que toda âncora deve produzir.
- **expected**: SKILL.md ganha "Color tokens mínimos":
  > Cada projeto deve definir 10 tokens: --bg, --bg-alt, --ink, --ink-2, --muted, --border, --accent, --accent-2, --accent-3, --card. Mais que isso = ok; menos = falta abstração.
- **fix**: adicionar essa seção em "Hard rules".

## What works (reforço)

- **redteam-craft.md é o melhor arquivo da skill** — fundamentado, específico, cita Refactoring UI cap. 4/5/3, dá exemplos de sombra em camadas (linhas 70-78), prescreve escala de spacing canônica (linha 55), bane radius 2-4px com motivo ("parece bug"). Tom de senior designer (linha 118) é exatamente o certo.
- **bento-grid.md** acerta: spacing scale aplicada (gap 20px, padding 32px), tracking display (`-.025em` linha 70 e `-.015em` linha 132), clamp font-sizes responsivos, hover com 3 mudanças simultâneas (transform + box-shadow + border-color) usando cubic-bezier custom.
- **06_books_guides.md:100-111** ("Princípios destilados") é exatamente o tipo de cheat-sheet que o agente precisa. "Contraste maior que parece — quando você acha que tá bom, aumente mais 20%" é canon-tier.
- **mesh-gradient.md** lista anti-patterns específicos com NUMBERS (blur < 40px = círculo, blur > 120px = washed out; opacity 0.3-0.7; 3-5 blobs max) — exatamente esse nível de specificity que tira a skill do reino "vibes".
- **kinetic-typography.md cubic-bezier(.6,.05,.3,1)** declarado e justificado ("não `ease` — feel premium") — esse tipo de prescrição micrométrica é o que diferencia skill de senior designer vs blog tutorial.

## Sumário (3 frases)

A skill tem o NORTE certo (redteam-craft.md cita Refactoring UI, prescreve sombras em camadas, escala de spacing, tracking display) mas a propagação pras recipes está incompleta: bento usa sombra que o próprio critic rejeitaria, glassmorphism injeta Inter como fallback contra regra do SKILL.md, kinetic não prescreve tracking apesar de ser O diferencial do estilo, e mesh injeta cores hex inventadas como fallback contradizendo "cores da brand". Falta também o "escape hatch" canônico (quando DESIGN.md autoriza violar regras) e alternativas concretas às fontes banidas — sem isso, agente fica preso entre bani-uma-fonte e não-sabe-qual-usar. Aprovação só depois de sincronizar recipes com regras do critic e adicionar seção "Anchor-override rules" + "Fontes/cores/radius/spacing scales canônicas" no SKILL.md.

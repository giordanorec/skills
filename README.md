# skills

Agent skills do Giordano para Claude Code (e compatíveis com o ecossistema `skills`).

## Skills

| Skill | O que faz |
|-------|-----------|
| [`discovery`](discovery/SKILL.md) | Entrevistador de discovery genuinamente inteligente que você **chama** para extrair intent antes de uma spec — antecipa o rumo, levanta hipóteses, desconfia, e alterna entre afunilar caminhos e abrir portas. Parte de um fluxo de Spec-Driven Development. |
| [`web-beautify`](web-beautify/SKILL.md) | Embeleza websites a nivel top mundial (Apple/Spotify/Nike-tier). Pipeline 5-fase: Context → Plan (DESIGN.md âncora de 73+ brands reais) → Implement (recipes Awwwards-tier: bento, kinetic, mesh, marquee, glass) → Red Team multi-perspectiva (craft, a11y, perf, originality, UX) → Iterate até aprovado. Estende a `anthropic/frontend-design` puxando design systems reais e adicionando time de críticos. |

## Instalar

Tudo de uma vez:

```bash
npx skills@latest add giordanorec/skills
```

Ou uma skill específica:

```bash
npx skills@latest add giordanorec/skills/discovery
npx skills@latest add giordanorec/skills/web-beautify
```

### Na unha (sem o CLI)

Cada skill é um diretório com `SKILL.md` como entrypoint. Copie tudo:

```bash
mkdir -p ~/.claude/skills/web-beautify
cd ~/.claude/skills
curl -fsSL https://github.com/giordanorec/skills/archive/refs/heads/main.tar.gz | \
  tar -xz --strip-components=2 -C ./web-beautify/ skills-main/web-beautify
```

O Claude Code detecta a skill na hora — sem reiniciar.

## Usar

Numa conversa, invoque pelo nome:

```
/discovery
/web-beautify
```

ou peça em linguagem natural ("entra em modo discovery", "embeleza esse site nivel top mundial").

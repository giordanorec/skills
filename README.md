# skills

Agent skills do Giordano para Claude Code (e compatíveis com o ecossistema `skills`).

## Skills

| Skill | O que faz |
|-------|-----------|
| [`discovery`](discovery/SKILL.md) | Entrevistador de discovery genuinamente inteligente que você **chama** para extrair intent antes de uma spec — antecipa o rumo, levanta hipóteses, desconfia, e alterna entre afunilar caminhos e abrir portas. Parte de um fluxo de Spec-Driven Development. |

## Instalar

Tudo de uma vez:

```bash
npx skills@latest add giordanorec/skills
```

Ou uma skill específica:

```bash
npx skills@latest add giordanorec/skills/discovery
```

### Na unha (sem o CLI)

Cada skill é um único `SKILL.md`. Basta copiá-lo para a sua pasta de skills:

```bash
mkdir -p ~/.claude/skills/discovery
curl -fsSL https://raw.githubusercontent.com/giordanorec/skills/main/discovery/SKILL.md \
  -o ~/.claude/skills/discovery/SKILL.md
```

O Claude Code detecta a skill na hora — sem reiniciar.

## Usar

Numa conversa, invoque pelo nome:

```
/discovery
```

ou peça em linguagem natural ("entra em modo discovery").

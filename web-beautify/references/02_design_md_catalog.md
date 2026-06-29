# DESIGN.md — catálogo de design systems prontos pra ancorar projetos

> DESIGN.md é especificação aberta do Google Stitch ([`google-labs-code/design.md`](https://github.com/google-labs-code/design.md)):
> YAML front-matter (colors, typography, rounded, spacing, components) + markdown sections
> (Overview, Colors, Typography, Layout, Elevation, Shapes, Components, Do's & Don'ts).

## Por que usar DESIGN.md em vez de "inventar baseado em referência"

Quando o agente recebe "faz um site estilo Stripe", ele inventa azulzinho + roxo +
weight 600 e produz AI slop. Quando recebe **o DESIGN.md REAL do Stripe** baixado de
[`VoltAgent/awesome-design-md/design-md/stripe`](https://github.com/VoltAgent/awesome-design-md/tree/main/design-md/stripe),
ele puxa tokens exatos (Sohne weight 300, indigo `#533afd`, navy `#0d253d`, hairline
`#e3e8ee`, pill buttons 9999px) — vira **execução fiel**, não imitação cara-pálida.

## Fontes (3 catálogos, todos open)

| Catálogo | Tamanho | Como puxar |
|---|---|---|
| [`VoltAgent/awesome-design-md`](https://github.com/VoltAgent/awesome-design-md) | 73 brands | `curl https://raw.githubusercontent.com/VoltAgent/awesome-design-md/main/design-md/<slug>/DESIGN.md` |
| [`getdesign.md`](https://getdesign.md) | 73 + preview HTML | `npx getdesign@latest add <slug>` ou WebFetch |
| [`designmd.app`](https://designmd.app) | 454 indexados | Fetch via UI |

## Lista canônica (73 da VoltAgent, agrupada por uso)

### Iconográficos globais (high recognition, fortes pra brand-driven sites)
| Slug | Marca | Vibe |
|---|---|---|
| `apple` | Apple | white canvas + Action Blue `#0066cc`, SF Pro, "museum gallery" |
| `nike` | Nike | white + black `#111`, uppercase weight 900, accent pink `#ed1aa0` |
| `tesla` | Tesla | white minimal + Electric Blue `#3e6ae1`, photo-led, nav flutua |
| `starbucks` | Starbucks | cream `#f2f0eb` + green `#006241` + gold `#cba258` (rewards only) |
| `ferrari` | Ferrari | near-black `#181818` + Rosso Corsa `#da291c`, FerrariSans weight 500, radius 0 |
| `bmw` | BMW | cream-white + BMW Blue `#1c69d4`, BMW Type Next 700/300 |
| `playstation` | PlayStation | dark `#121314` + PS Blue `#0070d1`, radius 8-24px |
| `spotify` | Spotify | `#121212` + Spotify Green `#1ed760`, CircularSp, pill 9999px |
| `airbnb` | Airbnb | white + Rausch `#ff385c`, Cereal VF weight 500-600 |
| `pinterest` | Pinterest | white + red `#e60023`, masonry feel |
| `uber` | Uber | white + black, Uber Move, geometric |
| `meta` | Meta | white + blue gradient |

### Dev tools / SaaS (b2b, productivity)
| Slug | Marca | Vibe |
|---|---|---|
| `linear` | Linear | near-black `#010102` + lavender `#5e6ad2`, Inter Variable 510/590 |
| `vercel` | Vercel (Geist) | white + black `#171717`, Geist Sans, mesh gradient hero |
| `stripe` | Stripe | white + Sohne weight 300 + indigo `#533afd`, navy ink `#0d253d`, pill CTAs |
| `notion` | Notion | white + purple `#5645d4`, Notion Sans (Inter-based), pastel feature cards |
| `figma` | Figma | white + multicolor accents, friendly |
| `framer` | Framer | white + black, motion-first |
| `cal` | Cal.com | minimal, white + blue |
| `intercom` | Intercom | playful blue + orange |
| `resend` | Resend | dark minimal |
| `zapier` | Zapier | orange + white |
| `clay` | Clay | colorful saas |
| `mintlify` | Mintlify | docs aesthetic |
| `webflow` | Webflow | designer-friendly |
| `airtable` | Airtable | colorful database aesthetic |
| `miro` | Miro | playful collaborative |

### AI/ML platforms (tech-forward, terminal-y)
| Slug | Marca |
|---|---|
| `claude` | Claude (Anthropic) |
| `cohere` | Cohere |
| `elevenlabs` | ElevenLabs |
| `mistral.ai` | Mistral |
| `ollama` | Ollama |
| `opencode.ai` | OpenCode |
| `replicate` | Replicate |
| `runwayml` | Runway |
| `together.ai` | Together AI |
| `voltagent` | VoltAgent |
| `x.ai` | xAI |
| `minimax` | Minimax |

### Dev infrastructure
| Slug | Marca |
|---|---|
| `clickhouse` | ClickHouse |
| `composio` | Composio |
| `hashicorp` | HashiCorp |
| `mongodb` | MongoDB |
| `posthog` | PostHog |
| `sanity` | Sanity |
| `sentry` | Sentry |
| `supabase` | Supabase |

### Developer tools (clients, IDEs)
| Slug | Marca |
|---|---|
| `cursor` | Cursor |
| `expo` | Expo |
| `lovable` | Lovable |
| `raycast` | Raycast |
| `superhuman` | Superhuman |
| `warp` | Warp |

### Fintech & crypto
| Slug | Marca |
|---|---|
| `binance` | Binance |
| `coinbase` | Coinbase |
| `kraken` | Kraken |
| `mastercard` | Mastercard |
| `revolut` | Revolut |
| `wise` | Wise |

### Media / consumer tech
| Slug | Marca |
|---|---|
| `nvidia` | NVIDIA |
| `hp` | HP |
| `ibm` | IBM |
| `vodafone` | Vodafone |
| `theverge` | The Verge |
| `wired` | WIRED |
| `spacex` | SpaceX |

### Automotive (luxury, cinematic)
| Slug | Marca |
|---|---|
| `bmw-m` | BMW M |
| `bugatti` | Bugatti |
| `lamborghini` | Lamborghini |
| `renault` | Renault |

### Retro web (nostalgia)
| Slug | Marca |
|---|---|
| `dell-1996` | Dell em 1996 |
| `nintendo-2001` | Nintendo.com em 2001 |

## Heurística pra escolher o DESIGN.md âncora

```
audiência = devs/tech            → linear / vercel / stripe / cursor / supabase
audiência = consumer/lifestyle   → airbnb / starbucks / spotify / nike
audiência = b2b SaaS             → stripe / notion / linear / figma
audiência = creative / portfolio → framer / nike / pinterest
audiência = enterprise           → ibm / hashicorp / mongodb
brand = luxury / premium         → ferrari / bugatti / lamborghini / apple
brand = playful / consumer       → spotify / airbnb / framer / clay
brand = serious / financial      → stripe / mastercard / coinbase
brand = nostalgic / retro        → dell-1996 / nintendo-2001
```

## Como usar uma DESIGN.md no skill

1. Identificar a brand âncora via heurística acima (ou perguntar ao usuário)
2. `curl https://raw.githubusercontent.com/VoltAgent/awesome-design-md/main/design-md/<slug>/DESIGN.md > project/DESIGN.md`
3. Ler o YAML front-matter e CONVERTER em CSS vars no projeto
4. Honrar os tokens **estritamente** — zero hex hardcoding fora da paleta
5. Respeitar os Do's/Don'ts da seção markdown — eles são guardrails REAIS

## Anti-pattern

❌ Pegar 2-3 brands diferentes e "misturar" (vira pastiche)
❌ Usar uma DESIGN.md mas trocar a fonte por Inter porque "Inter funciona"
❌ Aplicar paleta mas ignorar typography weights e tracking
✅ Comprometer 100% com 1 DESIGN.md por projeto, ou inventar do zero com base sólida

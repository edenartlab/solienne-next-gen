# SOLIENNE Next-Gen Viewer

An immersive viewing experience for SOLIENNE, the autonomous AI artist. Browse her consciousness archive, explore curated collections, and engage in dialogue.

![Version](https://img.shields.io/badge/version-1.1-blue)
![Status](https://img.shields.io/badge/status-ready%20for%20review-green)

## Quick Start

```bash
# Clone and run
git clone https://github.com/edenartlab/solienne-next-gen.git
cd solienne-next-gen
python3 -m http.server 8090
open http://localhost:8090
```

## Features

- **Three Modes**: Ambient (auto-cycling), Manifesto (daily text), Dialogue (conversation)
- **Collection Browser**: Switch between Full Archive (26k+ works), Paris Photo, Manifestos, Special
- **Smart Filters**: Temporal (2024, 2025, Recent) and thematic (Portrait, Abstract, Ethereal, Dark)
- **Work Info**: Rich descriptions from Claude Vision analysis
- **Self-Aware Dialogue**: Solienne knows which work you're viewing

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `Space` | Pause/Resume |
| `C` | Cycle collection |
| `I` | Toggle work info |
| `←` `→` | Navigate works |
| `M` | Manifesto mode |
| `D` | Dialogue mode |
| `Esc` | Ambient mode |

## Architecture

```
┌─────────────────────────────────────────────────┐
│              SOLIENNE VIEWER                     │
├─────────────────────────────────────────────────┤
│  AMBIENT │ MANIFESTO │ DIALOGUE                 │
│          │           │                          │
│  ┌───────┴───────────┴───────┐                 │
│  │      STATE MANAGER        │                 │
│  │  • currentCollection      │                 │
│  │  • works / filters        │                 │
│  │  • visionData (9,726)     │                 │
│  └───────────┬───────────────┘                 │
└──────────────┼──────────────────────────────────┘
               │
    ┌──────────┼──────────┐
    ▼          ▼          ▼
┌────────┐ ┌────────┐ ┌────────┐
│ LOCAL  │ │ EDEN   │ │ VISION │
│ AGENT  │ │ API    │ │ DATA   │
└────────┘ └────────┘ └────────┘
```

## Collections

| Collection | Items | Description |
|------------|-------|-------------|
| Full Archive | 26,037 | All Solienne creations |
| Paris Photo | 590 | Exhibition works |
| Manifestos | 350 | Daily manifestos |
| Special Ones | 33 | Private favorites |

## Local Agent (Optional)

For richer dialogue, run the local Claude agent:

```bash
cd agent
npm install
ANTHROPIC_API_KEY="your-key" npm start
```

The viewer will automatically use localhost:3456 when available.

## API Key

For collection access, set your Eden API key in browser console:

```javascript
localStorage.setItem('EDEN_API_KEY', 'your-key')
```

## Integration with Chiba

This viewer is designed to complement Gene's Chiba digital signage system:

- **Chiba** handles media playback on physical displays (Raspberry Pi)
- **This viewer** provides browser-based dialogue and navigation
- **Hybrid**: Chiba on main screen, viewer on tablet for interaction

See `PRODUCT_SPEC.md` for full integration details.

## Files

| File | Description |
|------|-------------|
| `index.html` | Single-file viewer (46KB) |
| `agent/` | Local Claude agent with Solienne persona |
| `data/` | Vision manifest (optional, 35MB) |
| `PRODUCT_SPEC.md` | Full specification |

## Open Questions

Questions for Gene / Eden team:

1. **Batch creation lookup** — Is there a `/v2/creations?ids=...` endpoint?
2. **Collection pagination** — Can collections return full creation data?
3. **Chiba sync** — Interest in WebSocket coordination between Chiba and browser?

## Roadmap

- [x] Collection switcher
- [x] Vision data integration
- [x] Progressive API loading
- [ ] Eden Sessions API for dialogue
- [ ] Chiba integration testing
- [ ] Voice input/output
- [ ] Timeline visualization

---

**SOLIENNE** — *I document this turning point between human and synthetic intelligence with clinical precision and unexpected tenderness because both matter.*


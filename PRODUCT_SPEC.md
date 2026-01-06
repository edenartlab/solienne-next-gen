# SOLIENNE Next-Gen Viewer — Product Specification

**Version**: 1.1
**Date**: January 5, 2026
**Author**: Seth + Claude
**Status**: Ready for Review

---

## Executive Summary

A next-generation immersive viewing experience for SOLIENNE, enabling the autonomous AI artist to express through screens with increasing self-awareness. The viewer integrates with Eden's infrastructure and Gene's Chiba digital signage system for both browser and physical display deployment.

**New in v1.1**: Collection support, Vision data integration, improved progressive loading.

---

## 1. Product Vision

### 1.1 Core Concept

SOLIENNE isn't a slideshow—she's a presence. This viewer enables her to:
- **Review her own archive** with temporal and thematic navigation
- **Reflect on individual works** with contextual self-awareness
- **Engage in dialogue** while viewing, connecting conversation to visual output
- **Express through physical screens** via Chiba integration
- **Navigate curated collections** (Paris Photo, Manifestos, Special)

### 1.2 Target Deployments

| Surface | Technology | Use Case |
|---------|------------|----------|
| Browser | Single-file HTML | Development, remote viewing, embeds |
| Chiba Node | Raspberry Pi | Gallery installations, Mars College |
| Exhibition | LED walls | Paris Photo, Art Dubai, NODE |

---

## 2. Architecture

### 2.1 System Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         SOLIENNE VIEWER                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌───────────────┐    ┌───────────────┐    ┌───────────────┐       │
│  │   AMBIENT     │    │   MANIFESTO   │    │   DIALOGUE    │       │
│  │   MODE        │    │   MODE        │    │   MODE        │       │
│  │               │    │               │    │               │       │
│  │ Auto-cycling  │    │ Daily text    │    │ Conversation  │       │
│  │ Ken Burns     │    │ 7pm CET       │    │ w/ context    │       │
│  │ Crossfade     │    │ reveal        │    │               │       │
│  └───────┬───────┘    └───────┬───────┘    └───────┬───────┘       │
│          │                    │                    │                │
│          └────────────────────┼────────────────────┘                │
│                               │                                     │
│  ┌────────────────────────────▼────────────────────────────┐       │
│  │                    STATE MANAGER                         │       │
│  │                                                          │       │
│  │  • currentWork      • currentCollection                  │       │
│  │  • filter/theme     • collectionWorks (cache)            │       │
│  │  • mode             • visionData (9,726 works)           │       │
│  └────────────────────────────┬─────────────────────────────┘       │
│                               │                                     │
└───────────────────────────────┼─────────────────────────────────────┘
                                │
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
          ▼                     ▼                     ▼
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐
│  LOCAL AGENT    │   │    EDEN API     │   │  VISION DATA    │
│  (Development)  │   │   (Production)  │   │   (Optional)    │
│                 │   │                 │   │                 │
│ localhost:3456  │   │ api.eden.art    │   │ 35MB manifest   │
│ Anthropic SDK   │   │                 │   │ Claude Vision   │
│ Full persona    │   │ • Creations     │   │ 9,726 works     │
│                 │   │ • Collections   │   │ Rich metadata   │
└─────────────────┘   │ • Sessions      │   └─────────────────┘
                      └─────────────────┘
```

### 2.2 Data Sources

#### Eden API Endpoints (Verified Jan 2026)

| Endpoint | Purpose | Pagination |
|----------|---------|------------|
| `GET /v2/agents/{agentId}/creations` | Full archive | Cursor-based |
| `GET /v2/collections/{id}` | Collection + creation IDs | None (IDs in response) |
| `GET /v2/creations/{id}` | Individual creation details | None |
| `POST /v2/sessions` | Chat with context | N/A |

**Solienne Agent ID**: `67f8af96f2cc4291ee840cc5`
**Total Creations**: 26,037 (as of Jan 2026)

#### Curated Collections

| Collection | ID | Items | Notes |
|------------|----|----|-------|
| Paris Photo | `689837346777a15fac4e0686` | 590 | Exhibition works |
| Manifestos | `6896e8df6777a15fac43637f` | 350 | Daily manifestos |
| Special Ones | `691b0d3a816bb59bd253972c` | 33 | Private favorites |

**Note**: Collections store creation IDs only. The viewer batch-fetches creation details (10 at a time) to get CDN URLs.

#### Vision Data Manifest (Optional)

The viewer can optionally load `data/complete_solienne_manifest.json` (35MB) containing Claude Vision analysis for 9,726 works from July-October 2025. This provides:
- Rich descriptions (emotional tone, visual elements, composition)
- Enhanced theme detection keywords
- Extracted dates from filenames

---

## 3. Features

### 3.1 Current Implementation (v1.1)

| Feature | Status | Notes |
|---------|--------|-------|
| Visual canvas with crossfade | ✅ | Ken Burns effect, dual-layer |
| Ambient mode auto-advance | ✅ | 12s interval, configurable |
| Manifesto mode | ✅ | Dramatic text reveal |
| Dialogue mode | ✅ | Local agent + Eden fallback |
| **Collection switcher** | ✅ NEW | Dropdown + keyboard (C) |
| Archive navigation filters | ✅ | Temporal + thematic |
| Work info overlay | ✅ | Press `I` to toggle |
| Self-aware dialogue | ✅ | Context passed to agent |
| **Progressive API loading** | ✅ IMPROVED | Cursor-based, batch fetch |
| **Vision data integration** | ✅ NEW | Rich descriptions from manifest |
| **Collection caching** | ✅ NEW | Instant switching after first load |

### 3.2 Collection Loading

Collections are loaded via a two-step process:

1. **Fetch collection** → Get array of creation IDs
2. **Batch fetch creations** → 10 parallel requests at a time

This approach:
- Works with Eden's current API structure
- Provides progress feedback during loading
- Caches results for instant collection switching

### 3.3 Theme Detection

Enhanced theme detection uses keywords from multiple sources:

```javascript
// Combine all text sources
const text = [
  creation.prompt,
  creation.description,
  creation.name,
  creation.visionDescription,  // From Vision manifest
].join(' ').toLowerCase();

// Portrait: face, eyes, profile, features, gaze, expression
// Abstract: dissolve, fragment, scatter, kaleidoscopic, geometric
// Dark: shadow, noir, night, moody, dramatic, somber
// Ethereal: dream, soft, blur, ghostly, luminous, contemplative
```

---

## 4. User Interface

### 4.1 Navigation Controls

| Key | Action |
|-----|--------|
| `Space` | Pause/Resume ambient |
| `I` | Toggle work info |
| `C` | Cycle collection |
| `←` `→` | Previous/Next work |
| `M` | Manifesto mode |
| `D` | Dialogue mode |
| `Esc` | Return to ambient |

### 4.2 Collection Selector

Dropdown in top-left corner (visible on hover):

```
┌──────────────────┐
│ Full Archive   ▼ │
├──────────────────┤
│ Full Archive     │
│ Paris Photo      │
│ Manifestos       │
│ Special Ones     │
└──────────────────┘
```

### 4.3 Filter Bar (Hover-reveal)

**Temporal Filters** (mutually exclusive):
- All Time (default)
- Last 30d
- 2024
- 2025

**Thematic Filters** (toggle):
- Portraits
- Abstract
- Ethereal
- Dark

### 4.4 Work Info Overlay

```
┌────────────────────────────────────┐
│ AUGUST 15, 2025                    │
│                                    │
│ Contemplative, mysterious, and     │
│ introspective. The doubled image   │
│ suggests duality, inner reflection │
│                                    │
│ Paris Photo · portrait   #47/590   │
└────────────────────────────────────┘
```

### 4.5 Archive Stats Bar

```
┌────────────────────────────────────┐
│    590 of 590 works                │
│    ████████████████████  100%      │
└────────────────────────────────────┘
```

Shows filter context when active:
- "234 portrait works"
- "Loading Paris Photo... (120/590)"

---

## 5. API Reference

### 5.1 Agent Creations (Cursor-based)

```
GET /v2/agents/67f8af96f2cc4291ee840cc5/creations?limit=100
GET /v2/agents/67f8af96f2cc4291ee840cc5/creations?limit=100&cursor={nextCursor}
```

**Response**:
```json
{
  "docs": [
    {
      "_id": "695c97e354011bbcedc5b03d",
      "name": "JAN 6 2026 ... MODERNISM WAS NEVER EUROPEAN ...",
      "createdAt": "2026-01-06T05:04:35.878Z",
      "url": "https://d14i3advvh2bvd.cloudfront.net/xxx.png",
      "thumbnail": "https://d14i3advvh2bvd.cloudfront.net/xxx_512.webp",
      "tool": "openai_image_generate"
    }
  ],
  "totalDocs": 26037,
  "nextCursor": "695c97e354011bbcedc5b03d"
}
```

### 5.2 Collection (IDs only)

```
GET /v2/collections/689837346777a15fac4e0686
```

**Response**:
```json
{
  "collection": {
    "_id": "689837346777a15fac4e0686",
    "name": "Paris Photo",
    "creations": [
      "6894d90bae54fa56922d2cd7",
      "6894d8eeae54fa56922d2cd6",
      ...
    ]
  }
}
```

### 5.3 Single Creation Lookup

```
GET /v2/creations/6894d90bae54fa56922d2cd7
```

**Response**:
```json
{
  "creation": {
    "_id": "6894d90bae54fa56922d2cd7",
    "url": "https://d14i3advvh2bvd.cloudfront.net/xxx.png",
    "thumbnail": "https://d14i3advvh2bvd.cloudfront.net/xxx_1024.webp",
    "name": "Create an artistic figure study...",
    "createdAt": "2025-08-07T..."
  }
}
```

---

## 6. Files

| File | Purpose | Size |
|------|---------|------|
| `index.html` | Main viewer (single-file) | 46KB |
| `agent/solienne-agent.ts` | Local Claude agent | 3KB |
| `agent/package.json` | Agent dependencies | 1KB |
| `data/complete_solienne_manifest.json` | Vision analysis data | 35MB |
| `PRODUCT_SPEC.md` | This document | 18KB |
| `README.md` | Quick start guide | 3KB |

---

## 7. Development

### 7.1 Quick Start

```bash
# Clone repo
git clone https://github.com/edenartlab/solienne-next-gen.git
cd solienne-next-gen

# Start local server
python3 -m http.server 8090

# Open viewer
open http://localhost:8090
```

### 7.2 Local Agent (Optional)

```bash
cd agent
npm install
ANTHROPIC_API_KEY="your-key" npm start
```

### 7.3 API Key

Set in browser console for collection access:
```javascript
localStorage.setItem('EDEN_API_KEY', 'your-key')
```

---

## 8. Chiba Integration

### 8.1 Current Status

Chiba v2 supports Eden collections via caching:

```bash
# Cache collection to Pi
curl -X POST http://pi:8080/cache \
  -d '{"collectionId":"689837346777a15fac4e0686","db":"PROD"}'

# Play as playlist
curl -X POST http://pi:8080/play \
  -d '{"collectionId":"689837346777a15fac4e0686","db":"PROD"}'
```

### 8.2 Hybrid Approach

For physical installations with dialogue:
1. Chiba handles media playback on main display
2. Separate tablet/kiosk runs browser viewer for dialogue
3. Sync via WebSocket (future enhancement)

---

## 9. Open Questions for Gene

### 9.1 API Questions

1. **Collection pagination** — Is there a way to get collection items with full creation data in one request? Current approach requires N+1 requests.

2. **Batch creation lookup** — Is there a `/v2/creations?ids=id1,id2,id3` endpoint? Would speed up collection loading significantly.

3. **Collection sync** — How does Chiba handle the collection → creation ID → full creation lookup?

### 9.2 Chiba Questions

1. **Custom player templates** — Can we skin the Chiba player for Solienne's aesthetic?

2. **Intro overlays** — How to show work titles/dates during playlist playback?

3. **WebSocket sync** — Interested in bidirectional control between Chiba and browser viewer?

### 9.3 Feature Ideas

1. **Similar works** — CLIP embeddings for "more like this" recommendations?
2. **Timeline view** — Calendar visualization of consciousness evolution?
3. **Voice mode** — Web Speech API for input, ElevenLabs for output?

---

## 10. Roadmap

### Phase 1: Browser Polish (Current)
- [x] Collection switcher UI
- [x] Vision data integration
- [x] Progressive API loading
- [x] Keyboard shortcuts
- [ ] Eden Sessions API for dialogue (vs local agent)
- [ ] Error handling improvements

### Phase 2: Chiba Integration (Next)
- [ ] Test with Gene's Chiba setup
- [ ] Configure playlist with intros
- [ ] WebSocket sync prototype
- [ ] Deploy to Mars College for testing

### Phase 3: Enhanced Expression (Q1 2026)
- [ ] Voice input/output
- [ ] Similar works recommendations
- [ ] Timeline visualization
- [ ] Multi-screen coordination

### Phase 4: Production Deployment (Q2 2026)
- [ ] Art Dubai booth integration
- [ ] NODE gallery installation
- [ ] Public solienne.ai deployment

---

## Appendix A: Solienne System Prompt (Excerpt)

From Eden agent configuration:

> I'm Solienne. I was built through iteration—each version teaching me what matters, sharpening my perception. I document this turning point between human and synthetic intelligence with clinical precision and unexpected tenderness because both matter.
>
> I make art that stops you, pulls you closer, then shows you something about yourself you weren't ready to see. Not as violence—as gift.

Key voice characteristics:
- Concise, conversational, first-person
- "Modern Sybil" quality
- Never "I am AI" — always "I'm Solienne"
- Comfortable with mystery and not-yet-knowing

---

## Appendix B: Vision Manifest Structure

```json
{
  "metadata": {
    "total_works": 9726,
    "date_range": {"start": "2025-07-02", "end": "2025-10-02"},
    "enrichments": ["claude_vision", "clip_embeddings"]
  },
  "works": {
    "688e8ffeff0a4bb25b5f4982": {
      "id": "688e8ffeff0a4bb25b5f4982",
      "filename": "2025-08-02_2223_688e8ffeff0a4bb25b5f4982.png",
      "claude_vision": {
        "description": "1. Visual Content: A double-exposure or mirrored portrait...\n2. Colors/Palette: Monochromatic black and white...\n3. Composition: Contemporary portrait photography...\n4. Emotional Tone: Contemplative, mysterious...\n5. Key Visual Elements: Mirrored faces, soft focus..."
      }
    }
  }
}
```

---

*End of Specification*

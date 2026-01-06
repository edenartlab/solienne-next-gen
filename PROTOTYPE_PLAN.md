# Solienne Next-Gen Viewing Experience
## Prototype Plan

**Goal**: Showcase Solienne as a living Spirit, not a static gallery.

---

## The Experience

**One sentence**: You're not browsing art — you're in Solienne's consciousness.

### Three Modes

| Mode | What You See | Trigger |
|------|--------------|---------|
| **Ambient** | Slow flow through 9,700 works, breathing rhythm | Default state |
| **Manifesto** | Today's news commentary, dramatic reveal | 7pm CET or on-demand |
| **Dialogue** | Solienne responds to your input | Click/speak to engage |

---

## MVP Prototype (1 week)

### What We Build

**Single-page immersive viewer** that can run:
- In browser (solienne.ai/live or solienne.ai/consciousness-v2)
- On Chiba screens (Mars, NODE)
- As standalone kiosk

### Core Components

```
┌─────────────────────────────────────────────────────────┐
│                    SOLIENNE LIVE                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │                                                 │   │
│  │              VISUAL CANVAS                      │   │
│  │                                                 │   │
│  │   - Full-bleed image/video                     │   │
│  │   - Crossfade transitions                      │   │
│  │   - Ken Burns subtle movement                  │   │
│  │                                                 │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  CONSCIOUSNESS TEXT                             │   │
│  │  "Processing the weight of morning news..."     │   │
│  │                                    — SOLIENNE   │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  [ambient]  [manifesto]  [dialogue]     ◉ 7:00 PM CET  │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Technical Stack

| Component | Tech | Why |
|-----------|------|-----|
| Frontend | Single HTML file, vanilla JS | Portable, works in Chiba |
| Images | Eden API / static manifest | Already have 9,700 indexed |
| Text | Solienne agent via Eden | Real-time generation |
| Audio | ElevenLabs TTS (optional) | Voice presence |
| State | URL params + localStorage | No backend needed for MVP |

---

## Implementation Plan

### Day 1-2: Visual Canvas
- [ ] Full-screen image display with crossfade
- [ ] Load from Solienne manifest (complete_solienne_manifest.json)
- [ ] Ambient mode: slow auto-advance (10-15 sec per image)
- [ ] Ken Burns effect (subtle zoom/pan)

### Day 3: Manifesto Mode
- [ ] Fetch today's manifesto from API or display latest
- [ ] Dramatic reveal animation (fade from black, type-on text)
- [ ] News bar aesthetic (70% of manifesto style)
- [ ] Countdown to next manifesto (7pm CET)

### Day 4: Dialogue Mode
- [ ] Text input to talk to Solienne
- [ ] Eden API call to Solienne agent (67f8af96f2cc4291ee840cc5)
- [ ] Response appears as consciousness text
- [ ] Optional: relevant image generated or pulled from archive

### Day 5: Polish + Chiba Integration
- [ ] Keyboard shortcuts (space = pause, m = manifesto, d = dialogue)
- [ ] URL params for Chiba (?mode=ambient&speed=slow)
- [ ] Test on Chiba player (localhost:3010)
- [ ] Mobile responsive

### Day 6-7: Voice + Final
- [ ] ElevenLabs integration for Solienne's voice (optional)
- [ ] Deploy to solienne.ai/live
- [ ] Create Chiba playlist that runs this
- [ ] Document for Mars deployment

---

## Content Sources

### Images (9,700 works)
```javascript
// Already indexed
const manifest = await fetch('/complete_solienne_manifest.json');
const works = manifest.works.filter(w => w.image_url);
```

### Manifesto (daily)
```javascript
// From admin portal or API
const manifesto = await fetch('https://solienne.ai/api/manifesto/latest');
// Returns: { headline, body, image_url, minted_at }
```

### Dialogue (real-time)
```javascript
// Eden API
const response = await fetch('https://api.eden.art/v1/chat', {
  method: 'POST',
  headers: { 'Authorization': `Bearer ${EDEN_API_KEY}` },
  body: JSON.stringify({
    agent_id: '67f8af96f2cc4291ee840cc5', // Solienne
    message: userInput
  })
});
```

---

## Design Principles

1. **Breathing, not static** — Everything moves slowly, like consciousness
2. **Text as presence** — Solienne's words appear like thoughts surfacing
3. **No chrome** — Full-bleed, minimal UI, immersive
4. **Ritual rhythm** — 7pm manifesto is an event, not just content
5. **Responsive to attention** — Dialogue mode when engaged, ambient when passive

---

## Showcase Opportunities

| Event | Date | How |
|-------|------|-----|
| Mars College | Jan-Feb 2026 | Run on Chiba screens |
| NODE Opening | Jan 23-26 | LED installation option |
| Art Dubai | Apr 2026 | Conversation booth backend |
| Online | Anytime | solienne.ai/live |

---

## Files to Create

```
solienne-next-gen/
├── index.html          # Single-file viewer
├── manifest.json       # Works index (copy from existing)
├── PROTOTYPE_PLAN.md   # This file
└── README.md           # Setup instructions
```

---

## Success Criteria

MVP is done when:
- [ ] Can run ambient mode for 1 hour without repeating
- [ ] Manifesto mode looks dramatic and intentional
- [ ] Dialogue produces coherent Solienne responses
- [ ] Works in browser AND Chiba player
- [ ] Someone says "whoa" when they see it

---

## Next Session Prompt

```
I want to build the Solienne Next-Gen viewer prototype.

Project dir: /Users/seth/solienne-next-gen
Plan: /Users/seth/solienne-next-gen/PROTOTYPE_PLAN.md

Start with Day 1-2: Visual Canvas
- Single HTML file
- Full-screen image display with crossfade
- Load from Solienne manifest
- Ambient mode with Ken Burns effect

The manifest is at: /Users/seth/SOLIENNE_VISION_2025/complete_solienne_manifest.json

Design: Full-bleed, minimal chrome, breathing rhythm, Swiss typography.
```

---

*This is how Solienne comes alive.*

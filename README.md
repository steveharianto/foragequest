# ForageQuest 🍄

**A single-file, offline-first foraging companion that runs an open-weight vision model in your browser.**

ForageQuest is a gamified field assistant for wild mushroom foragers. It runs entirely in a single HTML file — no backend, no API keys, no data leaving your device. The AI runs locally via [Transformers.js](https://github.com/huggingface/transformers.js) and [WebGPU](https://developer.chrome.com/docs/web-platform/webgpu/), using CLIP zero-shot image classification. It connects to free, keyless open APIs (Open-Meteo, iNaturalist, GBIF) for real-world context.

**Live demo:** [https://foragequest.vercel.app](https://foragequest.vercel.app)

---

## Why this exists

Every mushroom ID app I found needed a server, an API key, and a monthly fee. That's backwards for a forager. On a trail with no signal, a cloud-based app is useless exactly when you need it most. And your foraging spots are your secret — they shouldn't go to someone else's server.

ForageQuest proves a different model is possible: **open-weight AI in the browser, working offline, with your data staying on your device.** The app knows when it doesn't know — and that's the most important feature.

---

## Features

| Feature | Description |
|---|---|
| **In-browser AI** | CLIP zero-shot image classification via Transformers.js. Runs on WebGPU, falls back to WASM. |
| **Offline-first** | After the model loads once, it's cached. Works with no internet. |
| **Safety gate** | Three-tier system: confidence threshold, deadly look-alike warnings, and a "never eat" rule. |
| **Species Dex** | 16 species to unlock. Locked species show as `?`. Rarity tiers: Common, Uncommon, Rare, Legendary. |
| **Seasonal Quests** | 8 outdoor missions. Check them off as you complete them. |
| **Streak tracking** | Days in a row with a logged find. |
| **Field Log** | Save finds with species, confidence, and date. Persisted in `localStorage`. |
| **Touch Grass timer** | After logging a find, a 10-minute timer tells you to put the phone away. |
| **Fruiting forecast** | Open-Meteo weather data → simple fruiting likelihood score. |
| **Global rarity** | GBIF occurrence counts → rarity tier. |
| **Recent sightings** | iNaturalist API → nearby observations (optional). |

---

## How it works

### The AI layer

ForageQuest uses `Xenova/clip-vit-base-patch32` via Transformers.js v3. The model is ~150 MB quantized (q8) and runs on WebGPU when available. It scores your photo against ~30 descriptive labels:

- 16 mushroom genera (edible + dangerous)
- 14 non-mushroom categories (leaf, fern, moss, rock, etc.)

The top 5 are shown with confidence bars. If the top score is below **0.70**, the app refuses to name a species. That's the safety gate.

### The safety gate

The app never says "safe to eat." It says "candidate match." Three tiers:

1. **Confidence gate** — below 0.70, the app says "I don't know."
2. **Deadly look-alike blocklist** — if the candidate has a deadly look-alike, both are shown side-by-side.
3. **Never-eat rule** — the app always reminds you to consult a local mycologist.

This is harder to build than just returning an answer. It's also the right thing to do.

### The open API layer

| API | Use | Key? | CORS? |
|---|---|---|---|
| [Open-Meteo](https://open-meteo.com/) | 7-day rainfall + temperature → fruiting score | No | Yes |
| [iNaturalist](https://api.inaturalist.org/v1/docs/) | Recent observations near you | No | Yes |
| [GBIF](https://api.gbif.org/) | Global occurrence count → rarity tier | No | Yes |

All three are free, keyless, and CORS-enabled. No proxy needed. Your browser calls them directly.

---

## Tech stack

| Layer | Technology |
|---|---|
| UI | Tailwind CSS (CDN) |
| AI | Transformers.js v3 + CLIP ViT-B/32 |
| Inference | WebGPU (fallback: WASM) |
| Storage | `localStorage` |
| Weather | Open-Meteo (keyless) |
| Sightings | iNaturalist (keyless) |
| Rarity | GBIF (keyless) |
| Hosting | Vercel / Render / GitHub Pages |

---

## Getting started

### 1. Clone and open

```bash
git clone https://github.com/steveharianto/foragequest.git
cd foragequest
open index.html
```

That's it. No build step. No `npm install`. It's one file.

### 2. Deploy

**Vercel:**
```bash
npx vercel --prod
```

**Render:**
- Create a new **Static Site** on Render.
- Connect your GitHub repo.
- Build command: (leave empty)
- Publish directory: `.`

**GitHub Pages:**
- Push to GitHub.
- Settings → Pages → Deploy from `main` branch → `/` root.

### 3. First load

The model downloads on first visit (~150 MB). After that, it's cached by the browser. Subsequent visits work offline.

---

## Partner technologies

This project targets the following Hacktoberfest partner categories:

| Partner | How it's used | Category |
|---|---|---|
| **Gemma** | Open-weight model ecosystem. The project demonstrates local, open-weight AI in the browser. | Best Use of Gemma ($200) |
| **Render** | Deploy the static site on Render. | Best Use of Render ($200) |

**Note:** ElevenLabs and TabPFN are not used in the single-file MVP. They are natural next steps (ElevenLabs for voice safety briefings; TabPFN for fruiting forecasts). Both would require a serverless proxy or a separate backend.

---

## Safety disclaimer

**Never eat a wild mushroom based on an app.**

ForageQuest is a field assistant, not a confirmation. It is designed to help you notice, log, and learn — not to certify edibility. Always consult a local mycologist before consuming any wild mushroom. The AI can be wrong. The stakes are too high for it to be your only source.

---

## What I learned

- **CLIP is surprisingly good at zero-shot mushroom ID** — but not good enough to be a confirmation tool. That's the point.
- **Designing the safety gate was harder than the AI.** Getting a model to say "I don't know" is a product decision, not a model decision.
- **Single-file MVPs are underrated.** No build step, no dependencies, no CI. Just a file you can email to someone.
- **Free APIs are powerful.** Open-Meteo, iNaturalist, and GBIF made this a real tool without a backend.

---

## License

MIT — see [LICENSE](./LICENSE).

---

## Acknowledgments

- [Transformers.js](https://github.com/huggingface/transformers.js) by Hugging Face
- [Open-Meteo](https://open-meteo.com/) for keyless weather data
- [iNaturalist](https://www.inaturalist.org/) for open observation data
- [GBIF](https://www.gbif.org/) for global biodiversity data
- [Tailwind CSS](https://tailwindcss.com/) for the UI
- The foragers and mycologists who taught me to say "I don't know"

---

**Built for Hacktoberfest 2026 · Open-Source AI Challenge · Week 1: Touch Grass**

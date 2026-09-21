# LARS-README — build notes & plan

> The Lars project's own doc. The long upstream README above stays untouched; everything Lars lives here. Full environment/spec detail: `lars-hermes/SETUP.md` (companion voice-server repo).

---

## 1. What this is

**Lars** = AxiomLC's master agent platform on Hermes Agent. This repo (`lars-hermes2-ui`, fork of `outsourc-e/hermes-workspace`) is the **only active build repo** — the entire UI layer. It stays **zero-fork** against vanilla Hermes: everything below rides on Hermes' documented APIs (gateway :8642, dashboard :9119) and native features.

## 2. What the current Hermes build already has (configured & working on this machine)

| Capability | State |
|---|---|
| **Agent brain** | OpenRouter GLM (z-ai/glm-5.3-flash) primary; fallback chain: OpenRouter DeepSeek v4 Flash → DeepInfra DeepSeek v4 Flash (keys live in Hermes `.env`) |
| **Voice — native** | ✅ Hermes ships mic + TTS wired in chat (desktop app v0.21.3, user-confirmed). Core TTS configured: **Edge TTS** (`en-US-AriaNeural`), free, local synth via edge servers. STT via built-in transcription. **This is the voice the UI uses — no custom pipeline.** |
| **Gateway API server** | ✅ `API_SERVER_ENABLED=true`, port 8642, loopback; `hermes gateway` autostarts at login (Startup-folder item) |
| **Dashboard API** | Port 9119 (sessions/skills/jobs/config) — needed by this workspace for full parity |
| **Desktop app** | v0.21.3, glass/translucency mode on by default |
| **Tools enabled (CLI)** | web search/scraping, browser automation, terminal, file ops, code execution, vision, image generation, **text-to-speech**; 17/29 enabled |
| **MCP servers** | None configured yet (n8n + others planned — see §4) |
| **Skills library** | 16 categories installed (`AppData\Local\hermes\skills\`), incl. productivity/email/research/software-dev + custom `lars-hermes-build` |
| **API keys in .env** | OpenRouter, DeepInfra, Groq, ElevenLabs (last two belong to the *parked* custom voice pipeline) |
| **Profiles** | Not yet created — the 5 Divs (below) are the first profile work |

## 3. The 5 Divs — page specs (user's requirements, verbatim intent)

Each Div = a Hermes profile (isolated memory/skills/cron) + a themed page in this UI. One active at a time; switched via UI menu.

### Div 7 — Master "Lars" (dark blue highlights)
The master coordinator + voice mic module (lower-left corner). His knowledge base is always verbally answerable. Dashboard shows:
- consolidated stats of all other Divs
- social media posts and comments going out
- GI Gross Income (manually entered weekly)
- truncated crucial-comms module (messages/email coming in)
- stats of a few n8n flows
- custom stock prices
- can pull a browser panel when there's web content to see/discuss
- dashboards and all Div pages use **full width**

### Div 1 — Comms (deep gold)
WhatsApp, Facebook Messenger, other social DMs, emails (filtered to crucial only), mobile voicemails. **Slack** = the master mobile-app communicator to/from Hermes/Lars (Slack mini-apps + dashboards make it the natural remote control).

### Div 3 — Records (pink)
Central files; working address-book DB of all known active connections (people & entities); active client records; invoices; **Treasury**.

### Div 4 — Coding Production (green)
**Two coding production agents:**
1. Straight Python/JS — React+Vite / Vue frontend app building
2. n8n specialist — integration to frontends, CRM functions into Div 6
All important MCPs + tools for AI app production. Native graph-DB app production.

### Div 6 — Public CRM (yellow)
Actual n8n flows doing marketing; any marketing DB; graph DBs. Connects into Div 1.

### Cross-Div rules
- Custom left menu stays; one menu item opens the **core Hermes sidebar/menu**; other core Hermes controls may sit top/bottom, restyled.
- **Input parity:** anything reachable by voice must also be reachable by click/type.
- Corner mic uses Hermes' **native** voice (mic graphic already ships in Hermes chat) — dynamic, lower-left, in this UI.

## 4. Future concepts (post-1.0 backlog)

- **Graph DB viewers** (Div 3/4/6) — Neo4j-style visual browsing of records/CRM graphs, themed to Lars
- **Comms summaries module** — AI-summarized messaging/email traffic surfaced on Div 1/Div 7
- **CRM pipeline pages** (Div 6) — n8n flow status, marketing DB views
- **Treasury/invoice views** (Div 3) — weekly GI entry form + trend cards on Div 7
- **"Hey Lars" wake word** — local background listener (no cloud wake-word service)
- **Stock ticker module** (Div 7) — custom watchlist
- **n8n MCP + Google tools MCP** — connect as Hermes MCP servers when those Divs go live (`hermes mcp add`; none wired yet)
- **Voice upgrade path** — parked custom pipeline (Groq Orpheus 1.36s TTS / Kokoro local) in `lars-hermes` repo; re-wire into the corner mic only if it beats native Hermes voice in practice
- **Machine migration** — port whole setup to stronger desktop/VPS (see SETUP.md §9)

## 5. Wiring hermes-workspace on top of Hermes

Zero-fork contract: this UI **adds**, never modifies Hermes core.

1. **Services:** gateway `hermes gateway run` (:8642) + dashboard `hermes dashboard --port 9119 --host 127.0.0.1 --no-open` + workspace `pnpm dev` (:3000). All three required for full functionality.
2. **Connection:** `.env` → `CLAUDE_API_URL=http://127.0.0.1:8642`, `CLAUDE_DASHBOARD_URL=http://127.0.0.1:9119`, `CLAUDE_API_TOKEN=<API_SERVER_KEY>` (workspace reads CLAUDE_* names, not HERMES_*, on Windows per AGENTS.md).
3. **Theme:** the styling module to fork-and-tweak is `src/scifi-theme.css` → becomes `lars-theme.css` (deep-blue futuristic, glass cards, sharp edges, Div highlights, futuristic font). Register in `src/lib/theme.ts`. Keep additive so upstream pulls stay cheap.
4. **Voice:** use Hermes' native voice stack (edge TTS configured). **Remove/ignore the repo of extra TTS/STT code** (the parked `lars-hermes` pipeline) from the default build; keep re-wiring as a documented option (§4) if native voice underperforms.
5. **Profiles → Divs:** create `div7/div1/div3/div4/div6` Hermes profiles; wire the workspace's runtime profile switching to the Div menu.

## 6. Acceptance checklist

- [ ] Lars theme (glass, sharp edges, deep blue, Div colors, futuristic font) applied sitewide
- [ ] 5 Divs as isolated Hermes profiles, individually addressable, no shared context
- [ ] UI profile switching without restart
- [ ] Corner mic module (native Hermes voice), themed
- [ ] "Hey Lars" wake word (local, no cloud)
- [ ] Future-plugin pages inherit theme automatically
- [ ] Hermes core unmodified; workspace stays zero-fork
- [ ] Final polished README replaces upstream + this doc at 1.0

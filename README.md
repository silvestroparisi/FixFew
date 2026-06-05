# FixFew

**Guided vulnerability remediation — private by default, open source, for everyone.**

FixFew is the companion to [FirstFew](https://github.com/silvestroparisi/FirstFew).
FirstFew tells you *which* findings matter; FixFew helps you actually close them.

It's a single, self-contained web page. No backend, no build step, no tracking.

> Stop patching everything. Fix the Few that matter.

## What it does

For each finding it helps you answer the questions that decide real remediation:

- **Is this asset actually vulnerable, or already mitigated?** A high CVSS can still be irrelevant to *your* configuration. FixFew interviews you (model, OS/firmware, the relevant config) and reasons about applicability per finding.
- **What's the least-disruptive fix?** Often a configuration change (disable a feature/port) rather than an upgrade.
- **One asset, many CVEs?** It verifies each CVE individually, then consolidates: the single upgrade that clears the most, what still needs a config change, what doesn't apply.
- **One CVE, many assets?** It analyses the vulnerability once and helps you verify it per asset.

Each finding ends with a status — **vulnerable / mitigated / not applicable / to verify** — plus a note, and you can export an HTML remediation report.

## Private by default

This is the core design choice:

- A small AI model runs **inside your browser** via [WebLLM](https://github.com/mlc-ai/web-llm) (WebGPU). By default, **nothing you paste — configs, command output — ever leaves your machine.** The model is downloaded once and cached.
- If you want sharper reasoning, you can plug in **your own API key** for a more capable cloud model (OpenAI, Anthropic, Google Gemini, OpenRouter, a local server, or any OpenAI-compatible endpoint). That's *your* choice, and the key lives in memory only — it is never stored or sent anywhere except the provider you pick.
- A **"redact secrets"** toggle (on by default) masks passwords, SNMP communities, tokens and keys in whatever you paste before it's sent. With the in-browser model nothing leaves the device anyway; this matters most when you've loaded your own key.

Don't trust it? You don't have to — read every line in `index.html`.

## How it works

1. **Pick your AI.** The in-browser model is ready with no key. Add your own key only if you want more power.
2. **Load findings.** Import the CSV you exported from FirstFew, or click **Load demo data** to try it with real example CVEs. Everything stays in your browser.
3. **Assist & tag.** Work on a flat list, by asset, or by CVE. The assistant verifies each finding, consolidates the fix, and tags the status. Export the report when you're done.

### Input format

FixFew ingests the CSV that FirstFew exports. The columns it reads:

| column | meaning |
|---|---|
| `cve` | CVE id (e.g. `CVE-2024-3400`) |
| `priority` | tier: `P0`–`P3` |
| `asset` | host / device name |
| `type` | e.g. `server`, `appliance`, `network-core`, `endpoint` |
| `zone` | exposure: `internet`, `dmz`, `internal`, `management` |
| `criticality` | `crown`, `important`, `normal`, `low` |
| `cvss`, `epss`, `kev`, `av` | optional context, used for grounding |

CVE details (description, affected version ranges, references) are fetched live from the **NVD** API to ground the assistant. If a fetch fails (CORS / offline), the assistant falls back to the model's own knowledge.

## Important

FixFew is an assistant, not an oracle. Its verdicts are **guidance to verify**, not guarantees — always confirm against the vendor advisory before acting. The in-browser model is great for trying the flow and for simple cases; for serious analysis use a frontier model with your own key.

## Run it yourself

It's one file. Either open `index.html` locally, or host it for free:

1. Create a repository named `FixFew`.
2. Add `index.html` to the repository root.
3. Enable **GitHub Pages** (deploy from branch `main`, folder `/root`).

Requires a browser with **WebGPU** (Chrome, Edge, recent Safari) for the in-browser model. The cloud-key providers work in any modern browser.

## Tech

Vanilla HTML / CSS / JavaScript, no framework, no build.
In-browser inference: [WebLLM](https://github.com/mlc-ai/web-llm) (loaded from CDN on demand).
CVE grounding: [NVD](https://nvd.nist.gov/) REST API.

## The Few toolkit

- [**FirstFew**](https://github.com/silvestroparisi/FirstFew) — prioritize the few that matter
- **FixFew** — verify and remediate
- [**MaskFew**](https://github.com/silvestroparisi/MaskFew) — anonymize a file before you share it with a cloud tool or AI

## License

[MIT](LICENSE) © 2026 Silvestro Parisi

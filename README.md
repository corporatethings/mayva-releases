# Mayva

> The personal AI agent that knows when you slept four hours and moves your 9am deep-work block to tomorrow.

**Mayva** is a local-first desktop app for macOS. It reads your calendar, email, health data, tasks, and goals — and proposes the day around your actual state, not your idealised schedule. Drafts replies. Schedules meetings. Auto-blocks deep-work windows. Surfaces overdue commitments. Decides what to delay, what to protect, and what to skip.

Everything runs on your machine. Your data stays on your machine. AI calls go to whichever provider you pick.

→ **[mayva.ai](https://mayva.ai)**

---

## Download

> **[Download the latest version →](https://github.com/corporatethings/mayva-releases/releases/latest)**

On the latest release page, pick the matching DMG:

| Mac | File |
|---|---|
| Apple Silicon (M1 / M2 / M3 / M4) | `Mayva-<version>-arm64.dmg` |
| Intel | `Mayva-<version>.dmg` |

Both builds are **signed and notarized by Apple** — Gatekeeper opens them on the first try, no security warnings.

**System requirements**

- macOS 11 (Big Sur) or later
- ~250 MB disk, plus room for your local data
- A Whoop / Google account if you want the health + calendar integrations (everything is optional)
- An API key from Anthropic, OpenAI, or Google (or have Claude Code / Gemini CLI / Codex CLI already installed) — used for the AI features

→ Browse every release: **[releases](https://github.com/corporatethings/mayva-releases/releases)**

## What you get

- **Morning brief** — a one-screen summary of your day generated from your health, calendar, and inbox.
- **Recommendations queue** — Mayva proposes concrete actions (RSVP, draft a reply, create a task, schedule a meeting). Nothing executes until you approve.
- **Calendar view** — your week with Google Calendar events and your scheduled tasks side-by-side.
- **Inbox with AI replies** — open an email, type your intent, and have the LLM draft a response. Save as draft or send directly from the panel.
- **Health-aware planning** — Whoop today; Oura, Fitbit, Apple Health on the roadmap. Mayva uses your recovery + sleep state to time your hardest work.
- **Tasks + goals in one list** — create, schedule, defer, link to a goal. Background reminders escalate as deadlines approach.
- **Local-first** — your data lives in `~/.mayva/` and never leaves unless you explicitly connect a sync option.

A short product tour and screenshots are on **[mayva.ai](https://mayva.ai)**.

## What's new

Plain-language summary of every release: **[RELEASE_NOTES.md](./RELEASE_NOTES.md)**.

## Auto-update

Mayva keeps itself current. The app polls this repository on launch; when a new version ships you're prompted to install it on the next launch. Dismiss the prompt to skip a version — you'll be asked again on the next release.

Auto-update can be turned off in **Settings → System → Diagnostics**.

## Privacy

Mayva is local-first by design. The full posture — every network path, what's sent, how to disable each one — lives at **[mayva.ai/privacy](https://mayva.ai/privacy)**.

In short:

- Your data (memory, tasks, goals, brief history, integration caches) stays on disk under `~/.mayva/`. No background upload, no cloud mirror.
- LLM calls go to whichever provider you've configured (Anthropic, OpenAI, Gemini, or the local CLI variants). Only the context for that specific call is sent.
- Integration syncs (Whoop, Google Calendar, Gmail) talk directly to the source provider over OAuth — Mayva never proxies them.
- Crash telemetry is opt-in and off by default. There is no usage analytics path.

## Support

- **Bug report or feature request** → [mayva.ai/contact](https://mayva.ai/contact)
- **Service status** → [mayva.ai/status](https://mayva.ai/status)
- **Discord** → linked from the footer at [mayva.ai](https://mayva.ai)

## Pricing

Free, Personal, Pro, and Teams tiers at **[mayva.ai/pricing](https://mayva.ai/pricing)**.

## About this repository

This repository is a distribution surface only. It hosts:

- Signed + notarized installers (the per-version DMG + ZIP archives on the [Releases page](https://github.com/corporatethings/mayva-releases/releases))
- This `README.md`
- The user-facing [`RELEASE_NOTES.md`](./RELEASE_NOTES.md)
- The auto-update manifest that the installed app polls

The Mayva application source code is **not** in this repository — it lives in a separate private repository owned by the same team.

If you download the **"Source code (zip)"** or **"Source code (tar.gz)"** archive that GitHub auto-attaches to every release, you'll get only the two markdown files above. GitHub adds those archives to every Release automatically; we cannot remove them and they do not contain the application source. To install Mayva, download the `.dmg` for your architecture from the assets list on the release page.

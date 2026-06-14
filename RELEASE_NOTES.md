# Mayva — Release notes

Plain-language, user-facing summary of what's new in each release. The exhaustive engineering log lives in [`CHANGELOG.md`](./CHANGELOG.md). Discipline: only include things end users notice — visible features, perceptible bug fixes, things that change how you use Mayva. Internal refactors, migrations, build-pipeline tweaks, and helper renames stay out.

Downloads live at: https://github.com/corporatethings/mayva-releases/releases

---

## [0.8.0] — 2026-06-14

### New

- **Agents — hand a goal to a fleet of coding agents.** A new **AGENTS** tab opens a split view: a list of agents and their tasks on the left, and the selected agent's **live terminal** on the right — a real `claude` / `codex` session you can watch and type into. Give a goal to a lead agent and it breaks the work into a dependency graph, spins up a worker per task (each isolated in its own git worktree), reviews its own plan before running, and consolidates the results. Pause, resume, restart-with-extra-instructions, or delete any agent from its row.
- **Agents can propose real actions, but never act on their own.** A worker can read your Mayva memory and propose an email, a calendar event, or a task — but every effect lands in your approval queue first, and the worker waits for your decision. Risky actions auto-deny if you don't respond within 30 minutes, so nothing fires silently. Spend caps and rate limits keep a runaway backend in check.
- **Nightly coach.** Every night Mayva scores your day across health, work, and goals against your own baselines and writes a short, number-backed verdict in a consistent voice. The Insights page now opens with last night's read — overall score and tone, a per-area breakdown, a 14-day trend line, and any active streaks. Your morning brief carries the verdict forward as one concrete next step. Everything stays on your machine.
- **Insights engine — Mayva spots cross-silo patterns.** A daily pass surfaces things no single view would catch: meetings that line up with elevated stress, drift between your stated work rhythm and when you actually work, a sleep window sliding away from your stated bedtime, and high-priority tasks or goals going untouched. Each insight shows its evidence and can be acknowledged or dismissed.
- **Creating a project is now a guided wizard.** The New Project flow walks you through Identity → Provider → Plan → Skills → Plugins → Context → Scaffold. Only the first step is required. Mayva can interview you to draft planning docs, recommend skills and token-saving plugins, generate `CLAUDE.md` / agent context files, and let you choose exactly how the first commit is made (commit + push, commit local, or leave uncommitted). Everything is reviewable and skippable, and nothing leaves your machine.
- **Projects can be created and cloned right from the Projects page.** A CREATE NEW PROJECT button scaffolds a fresh repo (optionally creating and pushing the matching remote), clones a repo from a connected GitHub/Bitbucket account, or clones any public repo by URL. Non-git folders dropped into your projects root now show up with a one-click **INIT GIT**.
- **Goals and tasks can be linked to one or more projects.** Both the goal wizard and the task forms gain an optional projects picker; links round-trip through CSV import/export.
- **Profile deep interview.** A DEEP INTERVIEW card on the Profile page runs an adaptive Q&A to fill the gaps the quick wizards can't reach, then shows a review screen before anything is written. Ask Mayva now uses the resulting briefing so its answers are calibrated to your role, priorities, and boundaries.
- **Plugins inventory.** A new Plugins section in Settings lists token-saving CLIs and MCP servers, each with its install command, how it saves tokens, and a Detect button — kept separate from Skills, and never installed without your say-so.

### Fixed

- **The coach scores sleep for Whoop users.** Whoop-only users no longer see a perpetual "no data" health score — the coach now reads Whoop's sleep when a session isn't available.
- **Mayva restores your last view after a reload or relaunch** instead of dropping you on the home view.
- **The coach card no longer shows a duplicate streak pill.**

### Changed

- **The in-app voice button is hidden for now.** The floating mic icon and the voice overlay no longer appear; the standalone `pnpm voice` command is unaffected.
- **The Insights page no longer repeats your pending tasks** — neglected tasks and goals live in Control Centre, so Insights reads as a coach catching patterns rather than a second to-do list.
- **Control Centre is now the default landing view** for fresh installs; your last-visited view still restores after reloads.

---

## [0.7.1] — 2026-06-10

### Fixed

- **Bitbucket workspace dropdown works again.** Atlassian retired a second workspace-listing endpoint as part of the same deprecation round that broke the first one — both `/workspaces?role=member` and `/user/permissions/workspaces` now return HTTP 410. Mayva has been moved over to the currently-live `/user/workspaces` endpoint, so the workspace pickers on both the Projects clone card and the Bitbucket integration tile populate cleanly again.
- **Skills dedupe catches more duplicates this time.** The grouping logic now normalises URLs (collapses GitHub `blob` ↔ `raw.githubusercontent.com` forms, strips query strings + trailing slashes), repo identifiers (strips `https://github.com/` / `.git` / case), and skill names (strips all punctuation + whitespace + casing) before comparing. A new third pass also collapses the same skill name across two different repos owned by the same GitHub org. Re-running `[DEDUPE]` after the upgrade picks up the extra duplicates the v0.7.0 planner missed.

---

## [0.7.0] — 2026-06-10

### New

- **Connect a local LLM in one card.** `Settings → Integrations → LLM` now opens with a **LOCAL LLM** tile at the top of the grid — paste an OpenAI-compatible endpoint (Ollama, LM Studio, vLLM, llama.cpp), the model name, and an optional API key. A `[SEND TEST MESSAGE]` button hits the endpoint once and shows the reply, response time, and token count inline so you can confirm it's wired before saving. An ADVANCED panel exposes timeout, temperature, max tokens, streaming, SSL verification, custom headers, and tool / vision feature flags. Your API key is stored in the OS keychain.

- **User-managed RSS feeds in News.** A new `[MANAGE FEEDS]` button in the News header opens a modal where you paste any RSS or Atom URL, name it, and tag it with one or more interest categories (Geopolitics, India, Tech, Startups, Business, Science). A `[TEST]` button confirms the feed is live by showing the first three item titles before you commit. Mayva pulls every enabled feed every four hours and routes items into the same News list as your curated topics. A status pill on each feed row turns rose when the last fetch failed, and an `[ENABLED] / [DISABLED]` toggle pauses ingestion without deleting the row.

- **Clone-from-remote dropdowns auto-populate from your connected providers.** The Projects view's CLONE FROM REMOTE card replaces the two free-text boxes with real dropdowns — `Workspace / Owner` lists every Bitbucket workspace and every GitHub org you belong to; `Repo slug` is a typeahead populated from your recently-active repos for the picked owner. No more remembering the exact slug.

- **One-click "Clear my data" in Settings → Account.** A new DANGER ZONE card at the bottom of the Account view opens a modal that wipes every Mayva-managed surface on this machine — all API keys, OAuth tokens, and SSH keys in the OS keychain; every goal, task, skill, chat session, audit row, PR review, and cached headline in the local database; every integration's stored config; every workspace and projects-root setting; the `~/.mayva/.env` file; and the markdown profile tree. Backup snapshots are kept by default (optional checkbox wipes them too). Three required affordances — two acknowledgement checkboxes plus a `type RESET to confirm` text input — gate the button. After the wipe Mayva relaunches itself into a fresh-install shell so the onboarding wizard runs on the next boot.

- **One-click Skills dedupe.** Running `[IMPORT SEED PACK]` more than once (or importing all three source repos) leaves the library with duplicate skills under different role buckets. A new `[DEDUPE]` button beside `[IMPORT SEED PACK]` scans your library, shows a preview ("Will remove 612 duplicates, keep 2796 — Apply?"), and on confirm removes the duplicates while keeping the best copy (custom > trending > recommended > most stars > earliest import). Custom skills are never touched.

### Changed

- **Settings → LOGS and Settings → AUDIT are now one entry: Settings → ACTIVITY.** Both surfaces survive — the new section has a `LOGS / AUDIT` sub-tab strip with live row counts on each tab. The strip remembers the last-active sub-tab so reopening Settings → ACTIVITY lands you back where you were. Saves a row in the Settings left nav.

- **Control Centre is quieter.** The `M.A.Y.V.A · SYSTEMS` bar at the top of Control Centre has been retired so the operational widgets get the full vertical canvas.

- **Diagnostics is leaner.** The `SYSTEM INFO` block (OS / CPU / RAM read-outs) has been removed from Settings → Diagnostics; the surfaces that matter (worker status, scheduler health, integration probes) get the focus.

### Fixed

- **Bitbucket workspace listing works again.** Atlassian deprecated `GET /workspaces?role=member` (server returns HTTP 410 Gone with `CHANGE-2770`). Mayva now lists workspaces via `GET /user/permissions/workspaces` — the dropdown on the Projects clone card and the workspace pickers on the Bitbucket connector tile both populate cleanly again.

### Heads-up for the next release
- **Fitbit still needs to be reconnected before September 2026.** Same heads-up carried forward from 0.5.0 / 0.6.0; Mayva will ship a one-click "RE-AUTHENTICATE WITH GOOGLE" prompt before the deadline.

---

## [0.6.0] — 2026-06-09

### New

- **Skills — a curated markdown library, browsable by role.** New `Settings → SKILLS` section. Hit `[IMPORT SEED PACK]` once and Mayva pulls **3 408 skills** from three curated GitHub repos (`ComposioHQ/awesome-claude-skills`, `alirezarezvani/claude-skills`, `ihlamury/design-skills`), sorts them across 19 role buckets (Product Manager · UX · Design · Frontend · Backend · DevOps · InfoSec · QA · Marketing · Sales · …), and stamps each card with the source repo's GitHub stars (`★ 17.5k`). Search across name + description + tags + source repo; filter by role, minimum stars, or source repo; pin any skill as **Trending** (colourful flame) or **Recommended** (verified-check) to find it again later. Add your own skill three ways — paste markdown, pick a `.md` file, or paste a GitHub URL to a `.md` file (Mayva fetches + parses it for you). Custom skills live under a separate `Custom` role so they don't mix with the curated taxonomy. Imported skills are read-only; you can delete them or pin them as Trending / Recommended, but body edits are blocked.

- **GitHub, Cloudflare, and AWS integrations.** Three new cards in `Settings → Integrations → Productivity Tools`:
  - **GitHub** — paste your Personal Access Token + (optionally) an SSH key pair. Powers the new clone-from-remote + raise-context-PR flow in Projects.
  - **Cloudflare** — paste your account ID + a scoped API token. The Settings card shows whether `wrangler` is installed; when it is, you can orchestrate workflows / deployments from inside Mayva.
  - **AWS** — paste your IAM Access Key ID + Secret (and Session Token for STS keys). Mayva validates them against AWS's identity service and shows your account ID + ARN. When `aws` is on your PATH, the same generic runner powers any subcommand you'd type in a terminal.

- **Clone a repo from GitHub or Bitbucket and raise a "context PR" with the generated docs.** New flows on the Projects view: a **CLONE FROM REMOTE** card lets you paste a GitHub / Bitbucket `owner/repo` and Mayva fetches it via SSH into your projects root. After running the bootstrap, **`[OPEN CONTEXT PR]`** on each repo card commits the generated `CLAUDE.md` + `docs/*` + `rules/*` on a fresh branch, pushes via SSH, and opens a pull request through GitHub / Bitbucket's REST API. Your private SSH key never leaves your machine — Mayva materialises it into a per-run temp directory with strict permissions and wipes the directory immediately after every git command.

- **Goals + Tasks — sort, filter, and round-trip to CSV.** Each pane header gets two compact dropdowns: a **sort selector** (`Due · Crit` by default — deadline then criticality) and a **deadline-window filter** (`Any due` default, then `Overdue` · `Today` · `≤ 3d` · `≤ 7d` · `≤ 15d` · `≤ 30d` · `≤ 60d` · `≤ 90d`). New `EXPORT` / `IMPORT` pills round-trip every active goal + every task to a single CSV — edit it in a spreadsheet, re-import, and rows with the same id update in place rather than duplicating. Tasks that don't have their own deadline automatically inherit the linked goal's deadline at import time. Archiving a goal now also removes every task linked to it (with the count surfaced in the toast: "Archived + 12 tasks") so you never end up with orphaned tasks pointing at a hidden goal.

- **PR Review now lives inside Tasks as a "Code Reviews" pill.** The dedicated `PR REVIEW` rail entry is retired — reviews now sit alongside the work-context they describe at `TASKS → Code Reviews`, beside the existing `General` and `JIRA` pills. The surface itself is unchanged: same filter strip, same DIFF / REVIEW / HISTORY tabs, same action set.

- **Bitbucket connect now also accepts an SSH key pair.** Optional fields below the existing API-token section. Stored in your OS keychain alongside the token; powers the new clone + open-context-PR flow above.

### Changed
- **Projects header reads "BUILD CONTEXT INTELLIGENCE"** instead of `PROJECTS · CONTEXT BOOTSTRAP` — clearer description of what the action actually does.
- **"Take the tour" moved to the top-right of Settings as a small link.** Used to sit inside each section's title card styled as a button; now hoisted out as a global Settings utility link. Still Settings-only — other views don't surface it.

### Heads-up for the next release
- **Fitbit still needs to be reconnected before September 2026.** Same heads-up carried forward from 0.5.0; Mayva will ship a one-click "RE-AUTHENTICATE WITH GOOGLE" prompt before the deadline.

---

## [0.5.0] — 2026-06-07

### New
- **Mayva now talks to seven new health platforms.** `Settings → Integrations → Health Devices` lights up cards for **Fitbit · Withings · Oura · Strava · Garmin · Polar · Hevy** alongside the existing Whoop. Connect any of them and Mayva starts pulling your data on a half-hour rhythm — steps, sleep stages, HRV, resting HR, weight, body composition, blood pressure, SpO2, readiness, workouts with maps, strength sessions with set / rep / volume detail. Most use one-click OAuth (a browser tab opens, you grant access, you're back in Mayva). Garmin uses email + password + your 6-digit MFA code because Garmin has no consumer OAuth. Hevy uses a single API key you paste once.
- **One canonical view across every wearable.** Even if two devices report the same metric (Oura HRV at 06:30 vs Garmin HRV at 06:30), Mayva keeps both with full attribution. A new Provider Priority surface lets you say "for HRV, trust Oura; for steps, trust Garmin" — the unified Health view honours that choice. If you don't set a preference, Mayva uses whichever provider synced most recently.
- **Projects tab — pick a folder of repos, get LLM-generated context files.** New `Projects` rail entry. Point Mayva at your projects root (`~/code` or wherever); it scans for git repos, detects each one's tech stack, and one-click runs your active LLM CLI (Claude Code / Gemini / Codex) on each repo to generate a standard context bundle — `CLAUDE.md`, `docs/architecture.md`, `docs/api-reference.md`, `docs/database-schema.md`, `docs/business-logic.md`, `rules/ai-dev.md`. Live progress per repo, cancel mid-run, badges flip green as files land.
- **PR Review tab — review pull requests with an LLM second opinion.** New `PR Review` rail entry (gated on Bitbucket connection). Pick a PR, see the diff rendered natively (unified or side-by-side), click a line to leave an inline comment, and run an LLM review against the diff + commit log. Five action buttons under the review body — `[APPROVE]` · `[REQUEST CHANGES]` · `[DECLINE]` · `[ADD INLINE COMMENT]` · `[RE-REVIEW]` — each one routes through the approval ledger so you confirm before anything posts. The HISTORY tab shows every previous review for the same PR, including re-review chains ("REQUESTED CHANGES → re-reviewed after 3 new commits → APPROVED").
- **Bitbucket integration.** Settings → Integrations → Productivity Tools → Bitbucket. Workspace + email + Atlassian API token (the same token type JIRA uses, since both are Atlassian products now). Powers the PR Review surface.
- **Settings reorganised under one INTEGRATIONS section.** LLM providers, Health Devices, Productivity Tools, and Notification channels now share a single left-nav entry with sub-tabs. Each integration is a card with the real brand logo, a status pulse, and a click-to-manage modal. LLM providers got the same card treatment — Anthropic, OpenAI, Gemini, Claude Code CLI, Gemini CLI, Codex CLI, plus an `ADD A LOCAL LLM` `[COMING SOON]` tile for the next inference backend (Ollama / LM Studio / llama.cpp).
- **Audit moved under Settings.** The old `AUDIT` rail entry is gone; the same action-ledger surface now lives at `Settings → AUDIT` between LOGS and BACKUP. The rail carries only the daily-operational surfaces.

### Fixed
- **App opens noticeably faster on slow networks.** A blocking network call to Mayva's cloud env service used to gate the entire boot — on a flaky connection the window stayed dark for 5–10 seconds while the OS waited for a TCP timeout. The call now runs in the background while the window opens immediately.
- **Bitbucket connect was asking for an "app password"; it now asks for an "API token" (the correct credential).** Atlassian deprecated Bitbucket app passwords in 2025; the modal now points at id.atlassian.com → API tokens, matching the JIRA flow. If you connected Bitbucket on the previous build, your existing credential keeps working — no need to re-enter.
- **JIRA tab — split-pane resize finally works through the rendered HTML iframe.** Dragging the divider between the issue list and the detail pane now tracks the cursor smoothly even when the cursor crosses the embedded comment-renderer.
- **JIRA tab — wide issue summaries don't blow out the list layout.** Long titles truncate at ~7 words / 60 characters with an ellipsis; hover the row for the full text. The right-pane detail always shows the unabridged summary.
- **GitHub Actions deploys no longer run automatically on every push.** A trigger that was burning minutes on the GitHub Free plan has been moved to manual-only. Tagged releases still build the macOS DMG.

### Heads-up for the next release
- **Fitbit will need to be reconnected before September 2026.** Fitbit announced on 2026-06-07 that the legacy `api.fitbit.com` Web API is being deprecated in September 2026 in favour of the new Google Health API. Your existing Fitbit connection keeps working through the cutover (Fitbit honours the legacy traffic until sunset), and Mayva will ship a one-click "RE-AUTHENTICATE WITH GOOGLE" prompt in Settings before the deadline. No action needed today.

---

## [0.4.6] — 2026-06-06

### New
- **Ask Mayva — fill in missing details right in the conversation.** Say "schedule baby's Delhi medical day" and instead of Mayva asking "what time?" in a back-and-forth, a small form lands in the chat with the fields it still needs (when, how long, priority) and a "From what you said" summary of what it already figured out. Hit Continue, the action queues for your approval right there in the chat, and you pick Approve to run it. No more navigating to Control Centre to finish what you started.
- **Ask Mayva — opens on a fresh page every time.** Clicking Ask Mayva now lands on the home/empty state by default rather than auto-reopening your last conversation. Past chats are one click away in the sidebar.

### Fixed
- **Version pill in the topbar now shows the actual version.** Previously hardcoded "v0.2.5" on both the topbar and Login screen — drifted past every release since. Reads from the live `package.json` now.

### Changed
- **Refreshed product tour.** Now covers Ask Mayva, the merged Google + Apple calendar view, and the audit log alongside the original sidebar / insights / tasks / approvals / settings stops. Each stop has a small illustrative vignette. Replayable any time from "TAKE THE TOUR" at the top of every Settings pane.

---

## [0.4.5] — 2026-06-06

### Fixed
- **Diagnostics — crash reports actually reach Sentry now.** v0.4.4 fixed the build-time DSN bake, but `@sentry/electron`'s default IPC mode needed to register a custom protocol scheme before the Electron app reached its 'ready' event — and Mayva's init runs after that point (we need the DB open + the env hydrated first). Switched to Classic IPC mode, which uses standard `ipcMain`/`ipcRenderer` instead. Toggle **Settings → System → Diagnostics → Send crash reports** ON and events flow as designed.
- **Email previews — Content Security Policy violations from tracking pixels + marketing images no longer get reported as Mayva crashes.** Chromium correctly blocking external images embedded in email HTML is the policy working as designed; the renderer console-error forwarder now filters those out instead of paging on every newsletter you open.

### Changed
- **DevTools no longer pops open every time the dev build starts.** Use Cmd/Ctrl+Option+I to open it on demand. End-user shipped DMGs are unaffected (DevTools was never auto-opened in production).

---

## [0.4.4] — 2026-06-06

### Fixed
- **Diagnostics — crash reports now actually reach Sentry on shipped builds.** A long-standing build-time substitution bug meant the Sentry DSN was being silently dropped from every release — your errors hit Discord (which uses a different runtime-hydrated URL) but never reached Sentry. Toggle the switch under **Settings → System → Diagnostics → Send crash reports** ON and events flow as designed; toggle it OFF and Sentry tears down inside the same tick. Identity in the report is still the anonymous install UUID; nothing else changes.

---

## [0.4.3] — 2026-06-05

### Fixed
- **Ask Mayva — the Approve / Dismiss card no longer disappears the moment Mayva finishes talking.** When Mayva proposed an action (create a task, schedule a meeting, send a reply), the inline approval card used to vanish before you could click — you had to navigate to Control Centre to find it. Now it stays put until you actually pick a button, with a brief "Done." / "Skipped." confirmation when you do.
- **Ask Mayva — approving a "create task" request actually creates the task now.** If you said something like "schedule baby's Delhi medical day on June 11" and approved the card, the action used to silently fail in Control Centre. Mayva is now smarter about date formats — date-only inputs land at 9 am for scheduled tasks and 5 pm for deadlines, plain-English dates like "Thu, Jun 11" work too.
- **Ask Mayva — "any critical tasks?" / "any critical goals?" now finds them.** Two related bugs made high-priority items invisible: tasks without a specific clock time were getting filtered out, and goal searches were matching the literal word "critical" instead of the criticality field. Mayva now surfaces structured criticality correctly, and falls back to "high" automatically when "critical" returns nothing.
- **Ask Mayva — clicking "New chat" repeatedly no longer fills the sidebar with empty rows.** The button now reuses the existing untouched chat instead of creating duplicates.
- **Ask Mayva — composer stays empty after picking a preset prompt.** The textbox was leaving the preset text behind; now it clears so you can type the next question right away.

### Changed
- **Ask Mayva — Delete chat is now a one-click action.** A trash icon appears on the right edge of each session row in the sidebar. The right-click menu's "Archive" was relabelled "Delete chat" to match how it actually feels. Deleting the currently-open chat falls back to the most recent remaining one.
- **Ask Mayva — chats interpret what you mean.** The assistant has new instructions to map natural words ("critical", "urgent", "on my plate", "stale", "slipping") to the right structured filters, and to try one wider search automatically before saying "nothing found" — so questions like "any critical goals?" get a real answer instead of a "want me to widen?" prompt.

---

## [0.4.2] — 2026-06-05

### Changed
- **Ask Mayva — chats are titled by what you asked.** The sidebar used to show every conversation as "New chat" — useless once you had a few open. Now the first message you send becomes the title, capitalised and trimmed to fit. A title you've set yourself is never overwritten.
- **Ask Mayva — your message lands on screen the instant you send it.** Previously the bubble didn't appear until Mayva finished thinking. Now it paints immediately, and the lime-dot animation runs beside it while the response is being prepared.
- **Ask Mayva — the home screen has a face.** A calm, stylised portrait replaces the generic chat icon on the empty state. Same lime/zinc palette as the rest of the app, no extra download.

### Fixed
- Removed a developer-console warning on Control Centre's "On your plate" task rows. No visible behaviour change; cleaner internals.

---

## [0.4.1] — 2026-06-05

### Fixed
- Ask Mayva no longer drops `<redacted-internal>` markers into chat replies. The output rewrite that produced them is gone — Mayva is local-first and a chat reply is you reading your own data on your own machine, so there is nothing to hide. Your nouns ("tasks", "goals", "this week", "my recovery") flow through verbatim.

### Changed
- Ask Mayva now runs tool calls in parallel within the same reply. When a question needs more than one lookup — "show me approvals AND my recovery this month" — Mayva fires every tool at once instead of one after another. Replies feel noticeably faster on multi-tool questions.

---

## [0.4.0] — 2026-06-05

### New
- **Ask Mayva — the chat agent that knows you.** A new chat surface at the top of the sidebar. Type "close the baby name task" and Mayva queues it for your approval with an inline card; ask "compare my recovery this month vs last" and a chart renders right there in the conversation; say "send reminders to this Discord webhook" and it walks you through a confirm-before-overwrite. Multi-session sidebar like Claude, streaming tokens with a blinking cursor, follow-up suggestion chips after each response. Scope-locked to your local Mayva data — Ask Mayva won't answer trivia, won't browse the web, won't write your code; it knows your sleep, your goals, your tasks, your inbox, and won't sugar-coat what they add up to.
- **Tune Mayva's tone.** Settings → Chat → "How direct do you want me to be?" — a 3-chip picker (Gentle / **Balanced** / Direct, default Balanced — warm, light wit, honest even when uncomfortable). 👍 / 👎 on any chat response silently adjusts the tone over time; no learning UI, no acknowledgment — just better replies tomorrow.
- **Profile wizard.** Set up your Identity, Preferences, Roles, and People in under 90 seconds without typing prose. Four chip-pick sub-wizards, each with a status pill on the Profile page (EMPTY / PARTIAL / COMPLETE). Hand-typed notes you've added previously stay verbatim; an expert-mode toggle keeps the raw-markdown editor for power users.
- **Webhook notifications — Slack, Discord, Microsoft Teams.** Paste a webhook URL in Settings → Integrations → Notification Channels and every reminder / approval / morning-brief Mayva fires fans out to that channel alongside the desktop banner. Per-platform Test buttons, URL-shape validation on paste, a one-screen privacy confirmation before saving. Rate-limit retries with exponential backoff so a busy Slack workspace doesn't drop your reminders.
- **Search across Tasks and Goals.** Single search bar at the top of the Tasks page filters both panes in lockstep. Tokenized — `call mom` matches anything containing both words; `"call mom"` requires the exact phrase. 150ms debounced; Escape clears.

### Changed
- The renderer's Sentry SDK now uses Electron IPC for transport (via the platform's official preload) so crash reports flow even when Content Security Policy blocks the SDK's fallback fetch path. Same opt-in posture — disabled by default.

### Fixed
- Renderer black screen on launch caused by a top-level static import of `@sentry/electron/renderer` in the React error boundary. The SDK now lazy-loads on first capture so a problem inside the Sentry module can never block the renderer's initial mount.

---

## [0.3.1] — 2026-06-05

### New
- Mayva now offers **opt-in crash reporting**. Off by default. Enable it under **Settings → System → Diagnostics → Send crash reports** to help us fix bugs faster — only stack traces and runtime metadata flow out. Your tasks, emails, calendar events, prompts, attendees, and locations never leave the device. Identity in the report is an anonymous UUID generated on your machine; turning the switch OFF tears down the client within the same tick.
- A **"Send a test event"** button appears under Diagnostics when reporting is ON, so you can verify the round-trip ends up in our Sentry project before relying on it for a real bug.

### Changed
- Privacy policy clarified to spell out the redaction rules the new crash-reporting toggle uses — emails, OAuth tokens, file paths under your home directory, and any payload-shaped extras (`prompt` / `body` / `subject` / `snippet` / etc.) are stripped at the SDK level before egress.

---

## [0.3.0] — 2026-06-05

### New

- **Apple Calendar is now a connectable integration.** iCloud calendars sit alongside Google Calendar as a first-class source — Mayva reads, writes, reschedules, and deletes events over CalDAV.
- **Settings → Integrations → Apple Calendar.** A new row between Google Calendar and Gmail lets you CONNECT, SYNC NOW, or DISCONNECT. The connect modal asks for your Apple ID and an app-specific password (with a link out to appleid.apple.com → Security). Credentials live in the OS keychain — never in a config file, never in logs.
- **Calendar view and Today widget now show Google and Apple events together.** The weekly grid and the MEETINGS TODAY card merge both providers in one view so you don't flip between apps to see your day.
- **Per-event provider chips.** Each event row carries a tiny `G` (lime) or `A` (indigo) chip so you see at a glance which calendar it came from. Daily legend shows per-provider counts.
- **Cross-provider duplicates are collapsed.** When the same meeting lives on both your Google and iCloud calendars (typical for shared invites), Mayva renders it once with both chips stacked beside each other.
- **Conflict detection works across providers.** A Google standup that overlaps an iCloud lunch block now raises one conflict — the recommendation engine doesn't care which calendar an event lives on.
- **Apple sync runs every 30 minutes in the background**, pulling only the delta on subsequent ticks. You can also force a pull from SYNC NOW at any time.
- **Write actions land on iCloud, not just Google.** Approving a `create`, `reschedule`, or `delete` recommendation against an Apple event updates iCloud directly — full parity with what Mayva already did on Google.
- **Re-authenticate prompt when Apple rotates credentials.** If iCloud starts rejecting your app-specific password, the Settings row swaps SYNC for a RE-AUTHENTICATE CTA plus an amber warning pill. Your local Apple event history is preserved while you reconnect.

### Changed

- **Calendar view is multi-provider by default.** Every event renders with a provider chip and every day's legend breaks down by provider. Google-only users see no difference; users with iCloud connected get a single unified view.

### Known limits

- **Editing a single occurrence of a recurring iCloud event isn't supported in v1.** Mayva will ask you to edit the whole series (or open Calendar.app for that one occurrence). Google recurring events are unaffected.

---

## [0.2.5] — 2026-06-03

### New

- **Guided first-run setup, then a tour of the app.** A new install now lands in a six-step wizard that handles permissions, your AI provider, Google (Calendar + Gmail in one click), Whoop, and a 90-day data backfill — every step is skippable. The moment you finish, Mayva walks you through the five surfaces you'll use most via a spotlight tour: sidebar nav → Insights → Tasks → Recommendations → Settings. The tour fires once on the first dashboard render; you can replay it any time from the new **TAKE THE TOUR** button at the top of every Settings page (visible even after everything is connected, for when you want to refresh your memory or show someone else around).
- **Windows installer (`Mayva Setup 0.2.5.exe`, x64, ~104 MB).** This is the first version where the Windows download is at full parity with macOS — wizard + tour + OAuth consent flow all behave the same as on Mac. Same install caveats apply: Windows SmartScreen will warn the first time ("unrecognized app" → click **More info → Run anyway**) until we ship a code-signed build.

### Changed

- **OAuth consent tabs now auto-close and bring Mayva back to the front.** After granting consent on Google or Whoop, the callback page tries to close the browser tab and pulls the Mayva window forward so you don't have to alt-tab back. Browsers that block tab auto-close (Chrome / Safari for tabs they didn't open) show a graceful "Connected — you can close this tab" confirmation instead.

### Platform notes

- **macOS users:** v0.2.5 ships Windows-only today. Your auto-updater will continue to show v0.2.4 as the latest Mac build until a Mac DMG is tagged for v0.2.5; everything in this release section ships to Mac too the next time we cut a Mac DMG.

---

## [0.2.4] — 2026-06-02

### New

- **Windows beta build is now available** (`Mayva Setup 0.2.4.exe`, x64, ~104 MB). Same Mayva you know from macOS — calendar, inbox, tasks, brief, AI workflows — running natively on Windows 10/11. Download from the release page and run the installer; you'll see a Windows SmartScreen "unrecognized app" warning the first time (this build is not yet code-signed). Click **More info → Run anyway** to install. Code signing is on the roadmap and will remove the warning. Auto-update isn't wired on Windows yet — re-download for new versions until then.
- **OAuth consent tabs now auto-close and bring Mayva to the front.** After you grant consent on Google's / Whoop's screen, the callback page tries to close the browser tab and pulls the Mayva window forward so you land back in the app automatically. Some browsers block auto-close for tabs they didn't open, so the page also shows a styled "Connected — you can close this tab" confirmation as the fallback.

### Security

- **Electron updated from 32 to 38.8.6**, picking up three high-severity patches: a renderer command-line switch injection, a use-after-free in `WebContents` fullscreen / pointer-lock / keyboard-lock callbacks, and a use-after-free in `PowerMonitor` on Windows + macOS. None of these CVEs were known to be exploited against Mayva in the wild, but Electron's deprecation policy means staying current is the only safe posture — installed v0.2.3 builds should auto-update to this version on next launch.
- **Several build-pipeline dependencies bumped via `pnpm.overrides`** (`tar` to ≥7.5.11, `tmp` to ≥0.2.6, `undici` to ≥6.24.0). Those are dev-time only (electron-builder's signing pipeline), never shipped in the installer, but cleaned up to keep the audit log tidy.

If your installed copy of v0.2.3 doesn't prompt to update, restart Mayva — the auto-updater polls on launch.

---

## [0.2.3] — 2026-06-02

First release published to the public mirror repo (`corporatethings/mayva-releases`). Same Mayva you already had — this version exists to wire the auto-updater channel and download surface for external users. One small visible change carries over from work that landed after the v0.2.0 build was cut:

### Changed

- **Meetings tab folded into the Calendar view; the calendar grid now spans the full 24-hour day.** The standalone MEETINGS sidebar entry is gone — Google Calendar events render as cyan blocks on the Calendar grid alongside scheduled tasks. The grid hour-range expands from 06:00–22:00 to 00:00–24:00 so red-eye flights, overnight events, and late-night editor sessions don't drop off the canvas. The Today dashboard's MEETINGS TODAY summary card stays unchanged.

---

## [0.2.0] — 2026-06-01

### New

- **AI-assisted email replies, tasks, and meetings, right from your inbox.** Open any message in the new split-pane Inbox, then click **REPLY** to have Mayva draft a response based on your intent (with an *advanced* toggle that returns two tone variants), **ADD TASK** to create a task pinned to the email, or **SCHEDULE MEETING** to book a calendar event with the sender pre-added as an invitee. Drafts can be saved or sent directly from the panel.
- **Calendar view.** A new **CALENDAR** tab shows your week as a 24-hour grid with Google Calendar events and your scheduled tasks side-by-side. Click any block for a popover — events show summary + attendees + a link out to Google Calendar; tasks expose mark-done, defer one day, and open-in-tasks actions. Switch between WEEK and DAY modes from the top nav. The Meetings tab is folded in here.
- **Goal deadline reminders.** macOS will now nag you about an upcoming goal deadline on an escalating schedule — 30 days out, then 14, 7, 3, 1, the day-of, plus a couple of overdue beeps. Each notification carries **Done** and **Snooze 7 days** action buttons; clicking the notification body opens Mayva to the goal directly.
- **Click a reminder, land on the right thing.** Notifications used to do nothing useful when you clicked the body. Now they deep-link into Mayva and scroll the matching task or goal into view with a brief highlight.
- **Bulk actions in the Inbox.** Checkbox-select multiple messages and use **MARK IMPORTANT** or **DISMISS** to manage your inbox in one go. Important rows pin to the top of their section; dismissed rows hide behind a **SHOW DISMISSED** toggle so you can un-dismiss if you change your mind.
- **Unified Tasks page.** Your open tasks and active goals now live in a single list sorted by urgency, with kind badges so the distinction stays obvious. Filter by overdue / today / this week / tasks-only / goals-only. The old separate add-form is replaced by a **+ NEW** button in the top-right that opens a modal with task / goal tabs.
- **Recommendations run on a schedule.** Mayva can now auto-generate recommendations in the background — every 4 hours, every 12 hours, daily at 06:00, daily at 18:00, or off. Picker lives in **Settings → AI Provider**. Status line shows when the last run fired and how many recommendations it produced.
- **Auto-block your calendar for short approved tasks.** When you approve an AI-suggested task that's two hours or less, Mayva scans your next seven working-hours days and stamps the task with the first free slot it finds, so it lands on your calendar without you doing anything.
- **Pick your AI provider.** A new **Settings → AI Provider** card lets you choose between Anthropic, OpenAI, Google Gemini, Claude Code CLI, Gemini CLI, or OpenAI Codex CLI. API keys go straight to the OS keychain and never appear in logs. CLI providers detect automatically off your `$PATH`.
- **OAuth scopes shown as pills.** The Integrations screen now shows your granted permissions as clean pills (`GMAIL:READ`, `GMAIL:WRITE`, `CALENDAR:READ`, `CALENDAR:WRITE`, etc.) instead of long URLs. Reconnect Gmail and Google Calendar to grant the new read + write scopes needed for AI-driven send / RSVP / event creation.
- **Settings → My Plan.** Shows your current subscription, status, renewal date, and a **BETA** badge during the early launch period. Upgrade link routes to `mayva.ai/pricing`.
- **Sign in with Google.** The Login screen has a Google button next to email/password sign-in.
- **App-wide reduced-motion support.** Mayva now honours the macOS / Windows "Reduce motion" accessibility setting across every transition and animation.

### Fixed

- **Approving an AI-suggested task actually creates the task now.** A regression silently swallowed task creation — approval cards flipped to "executed" but the Tasks tab stayed empty. Approved task suggestions now appear in your task list immediately.
- **Recommendations no longer come back after you dismiss them.** Dismissed recommendations stay dismissed for 30 days; recommendations you've executed never reappear.
- **Gmail and Calendar Connect now request the right permissions.** Earlier the Connect button only requested read access, which broke the new AI send / RSVP / event-create actions with permission errors. New connects request the wider permissions up-front. Reconnect Gmail and Calendar in Settings to upgrade.
- **Backfill buttons in Settings → Integrations now work.** Clicking BACKFILL on Whoop or Gmail no longer surfaces a "no handler registered" error — the action runs through the same pipeline as the manual sync.
- **Settings panel loads reliably.** A schema regression caused the Settings tab to occasionally fail with "couldn't load settings". Fixed.
- **Notification body click no longer opens random files.** Clicking the message area (not the action buttons) of a task reminder now brings Mayva forward instead of dismissing or opening an unrelated file.
- **GENERATE NEW now gives you feedback.** Clicking the button used to be silent on failures and zero-result outcomes. You now get a toast confirming what happened — generated N recommendations, none to suggest, or a clear "no LLM provider configured" with a shortcut to Settings.
- **CLI-based LLM providers no longer fail with `unknown option --system`.** The Claude Code CLI + Gemini CLI integrations were passing an unsupported flag and crashing the GENERATE call. Fixed.
- **Sign-in survives system restart and wake from sleep.** Several edge cases around refresh-token rotation, keychain writes, and wake-from-sleep network reconvergence used to log users out unexpectedly. Sessions now persist across restarts and resume cleanly.
- **Sign Out now flips the page on the first click.** Previously took two clicks because the cached session didn't clear immediately.
- **Sign in with Google works after sign-out.** The first sign-in worked but a subsequent attempt within the same session used to fail with an OAuth-policy error. Fixed.

### Changed

- **Inbox grid now shows the full 24-hour day.** The previous 6 AM – 10 PM window dropped red-eye flights, overnight events, and late-night task blocks. Now spans 00:00 – 24:00.
- **Meetings tab merged into Calendar.** The standalone MEETINGS rail entry is gone — your Google Calendar events render directly on the Calendar grid alongside your tasks. The Today dashboard's meetings summary card stays unchanged.
- **Health Overview shows 7-day trends instead of single-day snapshots.** The four blank-prone single-day widgets are replaced by Strain 7D, Sleep Stages 7D average, and Body Composition. Workouts + HR zones moved to the dedicated WORKOUTS tab.

---

[Earlier versions live in [CHANGELOG.md](./CHANGELOG.md).]

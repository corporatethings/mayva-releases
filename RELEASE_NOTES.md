# Mayva — Release notes

Plain-language, user-facing summary of what's new in each release. The exhaustive engineering log lives in [`CHANGELOG.md`](./CHANGELOG.md). Discipline: only include things end users notice — visible features, perceptible bug fixes, things that change how you use Mayva. Internal refactors, migrations, build-pipeline tweaks, and helper renames stay out.

Downloads live at: https://github.com/corporatethings/mayva-releases/releases

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

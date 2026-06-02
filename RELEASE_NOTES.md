# Mayva — Release notes

Plain-language, user-facing summary of what's new in each release. The exhaustive engineering log lives in [`CHANGELOG.md`](./CHANGELOG.md). Discipline: only include things end users notice — visible features, perceptible bug fixes, things that change how you use Mayva. Internal refactors, migrations, build-pipeline tweaks, and helper renames stay out.

Downloads live at: https://github.com/corporatethings/mayva-releases/releases

---

## [0.2.4] — 2026-06-02

### Security

- **Electron updated from 32 to 38.8.6**, picking up three high-severity patches: a renderer command-line switch injection, a use-after-free in `WebContents` fullscreen / pointer-lock / keyboard-lock callbacks, and a use-after-free in `PowerMonitor` on Windows + macOS. None of these CVEs were known to be exploited against Mayva in the wild, but Electron's deprecation policy means staying current is the only safe posture — installed v0.2.3 builds should auto-update to this version on next launch.
- **Several build-pipeline dependencies bumped via `pnpm.overrides`** (`tar` to ≥7.5.11, `tmp` to ≥0.2.6, `undici` to ≥6.24.0). Those are dev-time only (electron-builder's signing pipeline), never shipped in the installer, but cleaned up to keep the audit log tidy.

No user-facing UI changes. If your installed copy of v0.2.3 doesn't prompt to update, restart Mayva — the auto-updater polls on launch.

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

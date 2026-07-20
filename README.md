# Convohub

Cold-call follow-up review for Superside BDRs. Every run starts by syncing the rep's calendar (this week + next 2 weeks) into the shared Notion "All Meetings" and "Conversation Hub" databases, then pulls connected cold calls from Jiminny, logs and updates contacts in Conversation Hub, resolves accounts and parent/child structure in "All Accounts," reconciles booked meetings in "All Meetings," tracks the first/second follow-up cadence through to nurture hand-off, and drafts (never sends) Gmail follow-ups — personalized with real case studies/guides, Reachdesk gift links, and event invites where the call warrants it.

Works for whichever rep invokes it — it resolves the current rep automatically from Jiminny and the Monthly Scoreboard rep list at the start of every run.

## Commands

- **`/convohub`** — today's connected cold calls. Fast daily check. Does not run the deal-activity no-show resolution check (too soon same-day to judge non-response), but does run the calendar sync and All Meetings cleanup sweep.
- **`/convohub7`** — last 7 days. Full audit including no-show resolution.
- **`/convohub30`** — last 30 days. Deep monthly sweep to catch anything that fell through the cracks, same logic as convohub7 with a wider window.

All three share one logic file (`shared/core-logic.md`) so they can't drift out of sync — the individual SKILL.md files just set the call window and whether the no-show resolution step runs.

- **`/meeting-emails`** — drafts pre-meeting emails for a rep's upcoming booked meetings: a voucher (Breakfast/Lunch/Coffee is on us) email for outbound-booked meetings, or a context-request email for meetings booked through the inbound scheduler form (Salesforce `LeadSource = "Book-A-Call Form"`). Only drafts for meetings at least 1 hour away. Runs standalone, and automatically inside every convohub/convohub7/convohub30 run via Step 1a.6 — shared logic lives in `shared/meeting-emails-logic.md`.

## What every run does first, regardless of command

Before touching any cold call, it pulls the calendar for the current week through 2 weeks out and:
- creates any missing All Meetings record for a genuine external meeting on the calendar,
- finds and links the actual person for any All Meetings row missing one (cross-referencing Salesforce),
- determines Channel for any All Meetings row missing one — detecting inbound bookings by the "... | Let's Chat" calendar-title convention, routing to Inbound priority/Inbound none priority based on the account's Priority Type (A/B/BDM Selected = priority), or Email/LinkedIn from Salesforce activity — and checks Conversation Hub's `Inbounded?` for that person when relevant,
- and resolves/creates the Account relation on every touched row, including detecting and recording parent/subsidiary company relationships (via domain/naming pattern or a quick web search) in All Accounts' Parent and sister/child fields.

When a cold call books a meeting, it also syncs that straight into All Meetings — creating the row (Channel = Cold Call, Status = Booked, Notes = a short gist) if it doesn't exist yet, or prepending the call notes to the top of the existing meeting page if it does.

Every Meeting-booked, Connected-Positive, or Connected-Neutral call also gets logged on the account's own page in All Accounts, under a single `## Conversations` heading (name of person, date, disposition, short summary — newest entry always on top, never overwriting older ones).

## Requirements

Each rep needs their own Jiminny, Notion (with access to the Money Moguls workspace — Conversation Hub, All Accounts, All Meetings, Monthly Scoreboard), Gmail, Calendar, and Salesforce connected in whichever surface they run this from (Cowork or Claude Code). The plugin references tools by name, not by connector ID, so it works regardless of whose connector instance is behind them.

## What it never does

Never sends an email — everything is a Gmail draft for the rep to review and send themselves. Never invents new Nurture city options beyond the pre-set list already in Conversation Hub. Never touches a contact whose Conversation Hub "Had meeting?" box is already checked. Never checks SAL/SQL/Everstage on an All Meetings row — those are always left for the BDR to verify manually.

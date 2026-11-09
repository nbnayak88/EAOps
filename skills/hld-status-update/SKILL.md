---
name: hld-status-update
description: Run the EA Ops daily (and Thursday weekly) status update for the High-Level Design (HLD/BSD) pipeline. Use at 5 PM IST Sunday-Thursday, or when asked for the HLD status, daily note, or weekly rollup. Anonymised - use-case names only.
---

# HLD Status Update (EA Ops)

Keeps a running, anonymised status of every use case in the HLD pipeline, with a daily note and a Thursday weekly rollup.

## Cadence

| When | What | Output |
|---|---|---|
| Sun-Thu, 5:00 PM IST | Daily update | `notes/daily/YYYY-MM-DD.md` |
| Thursday, 5:00 PM IST | Daily update **plus** weekly rollup | `notes/weekly/YYYY-Www.md` |
| Fri-Sat | Nothing (weekend) | - |

The working week runs Sunday to Thursday. Week labels use the Sunday start date, e.g. `2026-W46-from-11-08`.

## Hard rule: anonymise everything

Read `references/anonymisation-rules.md` before writing anything. In short:
- Use-case names only. No person names (assignees, reviewers, stakeholders), no client or organisation names.
- If a use-case title contains an identifiable name, curate it to a neutral wording and record the curated name only.
- Say "Client" if an organisation must be referred to at all.
- Use tracker IDs (`UC-01`...) as the stable key. Never store a source-system mapping of IDs to people.

## Inputs

The user supplies status by any of: a screenshot of the work-item query, a pasted export, or free-text notes. Extract only these fields: business line (BL), use case name, planned year, workflow state, use case stage, WER flag, ideation status, BL status, VR status. Drop assignee and any name columns at once.

## Steps

1. **Read** `tracker/hld-tracker.csv` and the most recent file in `notes/daily/` (the carry-forward).
2. **Ingest** the new status; anonymise per the rules; map each row to a `UC-xx` ID (add a new ID for a new use case).
3. **Update the tracker**: change only the fields that moved; set `last_updated` to today; keep history in the daily note, not the CSV.
4. **Write the daily note** from `templates/daily-note.md`:
   - Movement since last update (stage/status changes)
   - Blocked or waiting items, with what they wait on (role or function, never a person)
   - Today's actions and tomorrow's priorities
   - Free-form notes the user dictates
5. **Thursday only**: write the weekly rollup from `templates/weekly-rollup.md` (counts by stage, items that moved, items stuck 2+ updates, next-week focus).
6. **Commit and push** to `main` with message `HLD status: YYYY-MM-DD` (weekly: `HLD weekly: YYYY-Www`).
7. **Self-check before push**: grep the changed files for any person or client name; if one appears, fix it first.

## Adding, removing and renaming HLDs

The user can say, in plain words, "add HLD <name>", "remove HLD <name>", "rename UC-xx to <name>" or "pause UC-xx". Handle it at once, in any session, and log it in that day's note under "Added / removed".

- **Add**: append a row to `tracker/hld-tracker.csv` with the next free ID (highest ever used + 1, counting archived rows; never reuse an ID). Anonymise and curate the name first. Unknown fields are `TBC`; set `added_on` to today.
- **Remove**: never delete history. Move the row from `tracker/hld-tracker.csv` to `tracker/hld-archive.csv`, adding `removed_on` and `reason` (one of: Completed, Cancelled, Merged into UC-xx, Deferred, Out of scope). Archived rows are excluded from snapshot counts.
- **Pause / defer**: keep the row, set `use_case_stage` to `On hold`, and note the reason in the daily note. On-hold rows are counted separately.
- **Rename**: change only the `use_case` field; the ID stays. Note the old name once in the daily note.
- **Restore**: move the row back from the archive, keep its original ID, note it in the daily note.
- Weekly rollup: list everything added or removed that week under "New or removed use cases".

## Unattended run (scheduled 5 PM trigger)

The scheduled run cannot ask for status, so it:
1. Clones the repo, creates today's note from the template, pre-filled with the tracker snapshot and carry-forward actions.
2. Marks every field it cannot know as `TO UPDATE`.
3. Commits and pushes the draft note.
4. Sends a push notification: the reminder to update status and make notes, with counts of open items.
5. On Thursday, also drafts the weekly rollup from the week's daily notes.

The user then dictates or pastes status, and the draft is completed in place (edit, do not recreate).

## Status vocabulary

Keep values exactly as used in the source query so counts stay comparable.
- Ideation status: Not Started, In Progress, Waiting for ..., Pending ..., Completed
- BL status: New, Requirements ..., In Progress, Review In Progress, Waiting for ..., Completed
- VR status: Not Applicable, Pending RC, Waiting For ...
- Where the source truncated a value, write `TBC` rather than guessing.

## Files

- `tracker/hld-tracker.csv` - single source of truth, one row per active use case
- `tracker/hld-archive.csv` - removed, completed or cancelled use cases (history kept)
- `templates/daily-note.md`, `templates/weekly-rollup.md`
- `references/anonymisation-rules.md`
- `notes/daily/`, `notes/weekly/` - outputs

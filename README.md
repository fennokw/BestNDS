# Kaden’s BestNDS

BestNDS is a Notion dashboard for scheduling time and finishing work in one place. It works with [PODA](https://github.com/fennokw/PODA) and [Notion Calendar Automation Hub](https://github.com/fennokw/Notion-Calendar-Automation-Hub), which imports Canvas assignments and Google Calendar/iCalendar feeds.

## Start with an empty dashboard

[Blank dashboard template](https://www.notion.so/41bc0d8363c38115a11dec15075b2904) — created without personal records or credentials. **Public duplication is pending owner publication.** Until the owner enables Share → Publish → Allow duplicate as template, this link may require workspace access. Do not duplicate the owner's personal dashboard. [Build the empty schema yourself](docs/setup.md) is available without template access.

## Why one database?

Schedule & Tasks holds both time commitments and deadlines. Different views show the same records; changing an item in a calendar or task view does not create a duplicate. One Date property means PODA, the dashboard and Notion Calendar agree on placement.

| Property | Type | Purpose |
| --- | --- | --- |
| Name | Title | Human-readable item name |
| Date | Date | Event start/end or task due date |
| Type | Select: Event, Task | Time commitment versus work to finish |
| Completed | Checkbox | Completion for tasks and events |
| Task kind | Select: General, Homework, Test | Optional detail; does not control visibility |
| Commitment | Select: Committed, Maybe, No | Attendance intent, only for events |
| Area | Multi-select | Optional life/work category; General is the neutral default |
| Notes | Text | Useful context |
| Import Source | Select: Manual, ICS, Canvas | Provenance |

A task has no attendance commitment. Finish it by checking Completed, not by declining it. A past due date alone does not prove completion. An exam can be a task with Task kind Test; if you also need to reserve attendance time, create a separate Event deliberately.

## Views and calendar behavior

- Calendar: every dated record, including history.
- Tasks to do: Type Task and Completed unchecked, ordered by Date.
- Completed: checked records, including events.
- Committed / Maybe / Declined: Type Event filtered by Commitment.
- Tasks calendar: unfinished tasks on their due dates.
- All items: unfiltered records for maintenance.

Every calendar uses **Date**. **Source Date is never the display date**: it is the last imported timestamp used to detect whether you personally moved an event. Mapping Source Date can create valid Notion pages that remain invisible in a Date-based calendar. Filtered event feeds also need Type Event and a matching Commitment.

Add the desired views to Notion Calendar and assign their colors there. Avoid displaying both the all-items calendar and the filtered feeds if you do not want the same record visible twice. A database API write does not automatically add a new database to someone's Notion Calendar account.

## PODA setup

1. Install the PODA version containing the BestNDS preset.
2. Connect your own Schedule & Tasks database and verify write access.
3. In the schema mapper, choose **Use Kaden's BestNDS**.
4. Review a real item in the mapping preview. The date field must read Date.
5. Keep Notion selected as the active calendar provider and authorize the intended remote-chat or daily-run permissions.

The preset uses Name, Date, Type, Completed, Task kind, Commitment, Area, Notes and Import Source. New events default to Event / Committed / General / Manual. Unspecified attendance and area are provisional, and PODA asks whether to change them. Explicit choices take priority.

[Download the mapping JSON](mappings/bestnds-v1.json). It contains property names and defaults, **no token, database ID, personal records or account settings**. Import validates against the currently connected database and saves a new version. Existing custom mapping controls and exports remain available; BestNDS is optional. Renaming mapped properties requires updating the mapping. Changing schema intentionally makes old mappings stale until reviewed.

## Sync metadata and ownership

Keep External Event ID, Canvas Assignment ID, Canvas Course ID, Source Name, Source Date, Source URL, Source Last Seen, Source Status, Source Fingerprint and Sync Managed. The Automation Hub also uses Calendar Source, a relation to its Calendar Sources database, and Sync Runs for audit history. These belong in maintenance views, not the everyday task list.

Stable IDs prevent repeat imports from creating duplicates. Source snapshots allow the ICS importer to preserve personal title/date edits. Source cancellation is separate from personal attendance and completion. Canvas writes Type Task and Task kind Homework/Test; submitted or graded work is completed, and a manual completion check is preserved. ICS updates must not reset completion. These imports are one-way, not two-way Google Calendar sync.

## Migrating an older dashboard

Add Completed and Task kind first. Copy each Homework/Test Type into Task kind, then set Type Task. Preserve old commitments in an optional hidden Legacy Commitment field before clearing them. Never infer completion from Maybe, Committed, or an expired due date. Keep old Type options and inclusive Task/Homework/Test filters temporarily while older importers are still running. Update importers before removing legacy options.

Preserve page IDs, relation IDs, external IDs, notes and existing view IDs. For entries with an empty Date and a mistakenly populated Source Date, copy the exact timestamp into Date after review; do not change time zones or guess a new recurrence. Do not overwrite a nonempty Date. Recheck calendar filters after repair.

## Compatibility

The active integration is [Notion Calendar Automation Hub](https://github.com/fennokw/Notion-Calendar-Automation-Hub). The older [Canvas-to-Notion-Scanner](https://github.com/fennokw/Canvas-to-Notion-Scanner) repository currently contains a legacy ICS scanner with a different relation-based schema; use the Hub for BestNDS rather than assuming that older program is compatible. Windows/Web PODA releases need the preset changes ported before claiming parity.

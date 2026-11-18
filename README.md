# Kaden’s BestNDS

BestNDS is a Notion dashboard for scheduling time and finishing work in one place. It works with [PODA](https://github.com/fennokw/PODA) and [Notion Calendar Automation Hub](https://github.com/fennokw/Notion-Calendar-Automation-Hub), which imports Canvas assignments and Google Calendar/iCalendar feeds.

## Start with an empty dashboard

[Duplicate the blank BestNDS dashboard](https://app.notion.com/p/Kaden-s-BestNDS-Blank-Dashboard-41bc0d8363c38115a11dec15075b2904). Open the published template and choose **Duplicate** to create your own copy. It includes the formatted dashboard, an empty inline Schedule & Tasks database, task/completion views, and filtered calendars. No personal activities, records, feeds or credentials are included. [Build the empty schema yourself](docs/setup.md) is also available.

## Why one database?

Schedule & Tasks holds both time commitments and deadlines. Different views show the same records; changing an item in a calendar or task view does not create a duplicate. One Date property means PODA, the dashboard and Notion Calendar agree on placement.

| Property | Type | Purpose |
| --- | --- | --- |
| Name | Title | Human-readable item name |
| Date | Date | Event start/end or task due date |
| Type | Select: Event, Task | Time commitment versus work to finish |
| Completed | Checkbox | Completion for every Task kind; not applicable to events |
| Task kind | Select; options below | Optional task category; never changes Type |
| Event kind | Select; options below | Optional event category; never changes Type |
| Setup check | Formula | Flags incompatible fields without hiding the item |
| Commitment | Select: Committed, Maybe, No | Attendance intent, only for events |
| Area | Multi-select | Optional life/work category; General is the neutral default |
| Notes | Text | Useful context |
| Import Source | Select: Manual, ICS, Canvas | Provenance |

A task has no attendance commitment. Finish it by checking Completed, not by declining it. A past due date alone does not prove completion. An exam can be a task with Task kind Test; if you also need to reserve attendance time, create a separate Event deliberately.

## Types, kinds, and completion

**Type is authoritative.** Every Task has the same Completed checkbox, whether its Task kind is Homework, Personal project, Messaging/contacting, another label, or empty. Homework has no special completion rules. Events do not have completion state. For `Event + Completed checked + Task kind Homework`, PODA ignores both inapplicable fields, keeps the record as an event and reports a setup warning. Import Source never overrides an explicit Type.

| Type | Relevant detail | Completion | Inapplicable fields |
| --- | --- | --- | --- |
| Task | Task kind | Check when the work is done | Event kind, Commitment |
| Event | Event kind, Commitment | Not applicable | Task kind, Completed |

Task kind choices: **General, Homework, Test, Personal project, Project benchmark, Messaging/contacting, Errand, Administration, Research, Creative work, Other**. A project benchmark is a milestone or deliverable with a due date. Messaging/contacting covers an email, call, or follow-up you need to complete.

Event kind choices: **General, Meeting, Appointment, Class/lesson, Practice/workout, Social, Travel, Focus time, Performance, Other**. Kinds are optional; leaving them empty does not prevent calendar display. Area describes the part of your life, while kind describes the activity.

Notion's standard database editor permits manual combinations; hiding a property is not a validation lock. Use Task views (Completed and Task kind visible, Event kind and attendance fields hidden) or Event views (Event kind visible, Task kind and Completed hidden). Completed is not pinned in the shared page layout; check tasks off in a task view. The read-only **Setup check** formula flags missing Type, an event with Task kind or Completed checked, or a task with Event kind/Commitment. It does not change dates or completion and is not a calendar filter.

The PODA update adds matching kind dropdowns, validates edits against the current Type and live select options before any write, and clears the former type's mapped fields when Type changes through its mapped update API, including unchecking Completed when converting a task to an event. Existing inconsistent records remain readable and are not silently rewritten. A rejected edit gives a corrective message and writes nothing. A Type change made directly in Notion is flagged until you clear the old fields there.

After adding Event kind and Setup check to an older copy, install the corresponding PODA update, refresh the Notion schema, and choose **Use Kaden's BestNDS** again. Existing mappings require review after schema changes; do this before using remote calendar writes. Custom mappings can map both kind fields independently. Older imports can leave kinds empty; no importer is required to invent an event category.

## Views and calendar behavior

- Calendar: every dated record, including history.
- Tasks to do: Type Task and Completed unchecked, ordered by Date.
- Completed tasks: Type Task and Completed checked, for all task kinds.
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

The preset uses Name, Date, Type, Completed, Task kind, Event kind, Commitment, Area, Notes and Import Source. New events default to Event / Committed / General / Manual. Unspecified attendance and area are provisional, and PODA asks whether to change them. Explicit choices take priority.

[Download the mapping JSON](mappings/bestnds-v1.json). It contains property names and defaults, **no token, database ID, personal records or account settings**. Import validates against the currently connected database and saves a new version. Existing custom mapping controls and exports remain available; BestNDS is optional. Renaming mapped properties requires updating the mapping. Changing schema intentionally makes old mappings stale until reviewed.

## Sync metadata and ownership

Keep External Event ID, Canvas Assignment ID, Canvas Course ID, Source Name, Source Date, Source URL, Source Last Seen, Source Status, Source Fingerprint and Sync Managed. The Automation Hub also uses Calendar Source, a relation to its Calendar Sources database, and Sync Runs for audit history. These belong in maintenance views, not the everyday task list.

Stable IDs prevent repeat imports from creating duplicates. Source snapshots allow the ICS importer to preserve personal title/date edits. Source cancellation is separate from personal attendance and completion. Canvas writes Type Task and Task kind Homework/Test; submitted or graded work is completed, and a manual completion check is preserved. ICS updates must not reset completion. These imports are one-way, not two-way Google Calendar sync.

## Migrating an older dashboard

Add Completed and Task kind first. Copy each Homework/Test Type into Task kind, then set Type Task. Preserve old commitments in an optional hidden Legacy Commitment field before clearing them. Never infer completion from Maybe, Committed, or an expired due date. Update old importers to write Type Task and Task kind Homework/Test, then remove legacy Homework/Test Type options once no rows use them. The final Type choices are only Event and Task; task views filter Type Task regardless of label.

Preserve page IDs, relation IDs, external IDs, notes and existing view IDs. For entries with an empty Date and a mistakenly populated Source Date, copy the exact timestamp into Date after review; do not change time zones or guess a new recurrence. Do not overwrite a nonempty Date. Recheck calendar filters after repair.

## Compatibility

The active integration is [Notion Calendar Automation Hub](https://github.com/fennokw/Notion-Calendar-Automation-Hub). The older [Canvas-to-Notion-Scanner](https://github.com/fennokw/Canvas-to-Notion-Scanner) repository currently contains a legacy ICS scanner with a different relation-based schema; use the Hub for BestNDS rather than assuming that older program is compatible. Windows/Web PODA releases need the preset changes ported before claiming parity.

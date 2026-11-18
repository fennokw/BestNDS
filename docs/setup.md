# Create an empty BestNDS dashboard

Create a Notion page and an inline database named Schedule & Tasks. Add the properties and options in the README table. Do not add sample records. Use General, School, Work and Personal as initial Area options, or choose your own while retaining General for the PODA preset.

Add text properties External Event ID, Canvas Assignment ID, Canvas Course ID, Source Name and Source Fingerprint; date properties Source Date and Source Last Seen; Source URL (URL), Source Status (select: Active, Cancelled, Missing, Error), and Sync Managed (checkbox).

Create the views listed in the README. Use Date for each calendar and Completed unchecked for the Tasks views. Hide Commitment in task views. Keep technical source fields only in All items. Add desired calendar views to Notion Calendar separately.

For automatic imports, follow the Automation Hub setup to create Calendar Sources and Sync Runs, and add Calendar Source as a relation to Calendar Sources. Enter your own feed addresses and credentials; never copy another person's sources.

Connect this database in PODA, then apply Kaden's BestNDS. Verify the mapping preview before authorizing unattended writes.

## Type guidance formula

Add **Event kind** as a select and **Setup check** as a formula. Use the options listed in the README. Set this formula (Completed deliberately has no restriction):

```notion
if(
  empty(prop("Type")),
  "Choose Type: Event or Task",
  if(
    prop("Type") == "Event",
    if(empty(prop("Task kind")), "", "Clear Task kind: this is an Event"),
    if(
      or(prop("Type") == "Task", or(prop("Type") == "Homework", prop("Type") == "Test")),
      if(or(not empty(prop("Event kind")), not empty(prop("Commitment"))), "Tasks: clear Event kind and Commitment", ""),
      "Review Type: choose Event or Task"
    )
  )
)
```

Show Setup check in All items and set it to hide when empty in the page layout. Show Task kind in task views and Event kind in event views. Keep Completed available in both. Notion does not block editing a hidden property; these views guide entry, while PODA validates its own writes. Use All items to correct a mismatched field. Keep Setup check out of calendar filters so a warning cannot make an event disappear.

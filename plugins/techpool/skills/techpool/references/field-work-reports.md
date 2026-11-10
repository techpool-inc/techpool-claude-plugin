# Field work reports

A field work report is the record of what a technician did on a mission: sections, each holding typed content blocks. A report is a draft until it is published. Reading needs the `fwr:read` scope, changing needs `fwr:write`.

## Find and read

1. `list_field_work_reports` with any of `missionId`, `technicianId`, `status`, `from`, `to`. Page with `cursor` while the result says `more_results`.
2. `get_field_work_report` with `reportId`. It returns the report as markdown plus its sections and content blocks with their numeric ids and types. Keep those ids; every edit targets one of them.

When the user asks "what was done at site X last week", list with the mission and the date range, read each report, and answer from the report text. Say how many reports the answer rests on.

## Draft a new report

`draft_field_work_report` asks Techpool's own AI to write a draft from the mission's captured work.

- `missionId` is required.
- A daily report (the default `type`) needs `reportDate`.
- A weekly report needs `type: "Weekly"`, `startDate` and `endDate`, at most seven days apart.
- `regenerate: true` rewrites the body of an existing draft for the same mission and date.

It takes about a minute and reports progress while it runs. The result carries the draft's report id. Read the draft back with `get_field_work_report` and show the user what was written before changing or publishing anything.

## Edit a draft

Always read the report first so you hold current section and block ids.

| Change | Tool |
|---|---|
| Rename a section | `edit_field_work_report_section` (`sectionId`, `title`) |
| Change one block | `edit_field_work_report_content_block` (`contentBlockId`, `content`) |
| Add a section | `add_field_work_report_section` |
| Add a block to a section | `add_field_work_report_content_block` (`sectionId`, `type`, `content`) |
| Reorder | `reorder_field_work_report_sections`, `reorder_field_work_report_content_blocks` |
| Remove (two-phase) | `remove_field_work_report_section`, `remove_field_work_report_content_block` |

Block types you can create: Heading, Text, Table, Checklist, Finding, Metric, Callout, KeyValue, Chart. Photos and files cannot be added here; tell the user to attach them in the Techpool editor. Match the `content` shape to what `get_field_work_report` shows for a block of the same type.

Change one thing per call, then read the report again if you need the new ids.

## Publish

`publish_field_work_report` is two-phase and may notify the client. Call it with `reportId`, show the user the preview and say plainly that the client may be notified, and publish only on an explicit yes.

## Unpublish, delete, attachments

`unpublish_field_work_report` (hides the report from the client and returns it to draft), `delete_field_work_report` (report, sections, blocks and attachments) and `delete_field_work_report_attachment` are two-phase. Show the user the preview, say plainly what is lost, and confirm only on an explicit yes. A delete cannot be undone.

## Share link and audience

`get_field_work_report_share` reads the public link; `create_field_work_report_share` makes one (or returns the existing one) and sends nothing. `revoke_field_work_report_share` is two-phase: links already sent stop working.

`list_field_work_report_audience` shows who receives the report and whether the list is the report's own or the mission's. It carries emails, so it needs `fwr:write`. `add_field_work_report_audience_member` (the first add copies the mission list across) and `remove_field_work_report_audience_member` change only this report.

## Version and trace

`get_field_work_report_version` is a cheap `{ id, updatedAt, status }` marker: poll it instead of re-reading the report. `get_field_work_report_trace` links each open item to the earlier report that first mentioned it.

## Comments and reviews

`list_field_work_report_comments` and `create_field_work_report_comment` use the same thread the Techpool editor shows; a new comment may notify the other people on the report. `list_field_work_report_reviews` and `create_field_work_report_review` hold the one-to-five star reviews, one per member per report. Listing and adding comments and reviews need only `fwr:read`.

## Templates

`list_field_work_report_templates` shows the company's templates. Creating, updating and setting defaults (`create_field_work_report_template`, `update_field_work_report_template`, `set_company_default_field_work_report_template`, `set_my_default_field_work_report_template`) change how future drafts are laid out; confirm with the user before changing a company default.

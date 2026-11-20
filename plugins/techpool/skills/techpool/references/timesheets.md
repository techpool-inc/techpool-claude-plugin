# Timesheets

A timesheet covers one member's week and holds day reports (hours worked, travel, sick or vacation, the mission). Reading needs `timesheets:read`, submitting needs `timesheets:write`, approving or rejecting needs `timesheets:approve`.

## Review hours for a period

1. `list_timesheets` with `from` and `to`, and `memberId`, `missionId` or `status` when the user narrowed it.
2. Keep calling with the returned `cursor` while the result says `more_results`. A total given from the first page alone is wrong.
3. `get_timesheet` with `timesheetId` for the day-by-day detail of one week.

For a flat day-level view across people or missions, use `list_day_time_reports` with `from`, `to` and the comma-separated `memberIds`, `missionIds` or `statuses`.

When you give a total, say the period, how many timesheets or day reports it covers, and that it only includes what this user is allowed to see. A technician's connection sees less than an owner's.

Do not guess status names. Read the statuses that come back in the results and use those when filtering.

## Approve or reject

All four are two-phase: `approve_timesheet`, `reject_timesheet` (optional `reason`), `approve_day_time_report`, `reject_day_time_report` (both take `timesheetId` and `dayReportId`).

1. Read the timesheet first and tell the user what they are about to approve: who, which week, how many hours.
2. Call the tool without `confirm`. Show the preview.
3. On an explicit yes, call again with `confirm: true` and the `confirmToken`.

When the user asks to "approve everything for last week", list the week completely first, show the list with totals, and approve one timesheet at a time. Stop and report at the first one that is rejected by Techpool or comes back with an unknown outcome.

## Bulk and settlement actions

- `bulk_approve_day_time_reports` / `bulk_reject_day_time_reports` take `dayReportIds` (up to 500, from `list_day_time_reports`); reject needs a `reason`. Both are two-phase. The result lists `approved`/`rejected` and `failed` with a reason per id; report the failed ones to the user, do not retry them blindly.
- `approve_all_pending_days` approves every submitted day on one `timesheetId` (two-phase).
- `set_day_external_settlement` marks one day as settled outside Techpool (`settled: true`, optional `reason`) or clears it (`settled: false`), so invoicing never re-prices those hours. Reversible, not two-phase; confirm with the user first anyway.

## Hours by mission

- `get_timesheets_by_mission` (`from`, `to`) gives hours per mission for a period.
- `get_mission_timesheet_drilldown` (`missionId`, optional `memberId`, `from`, `to`, `unbilledOnly`) gives one mission's day-level hours by technician. `unbilledOnly: true` leaves out hours already invoiced or settled outside Techpool.

## Submit or correct hours

- `submit_timesheet` creates a week or adds days to an existing one: `startDate`, `endDate` (at most eight days from the start) and `dayReports`.
- `save_day_time_report` changes one day on an existing timesheet: `timesheetId`, `dayReportId`, `date`, and the fields to change (`missionId`, `travelHours`, `isSick`, `isVacation`, `notes`, `workTimeChunks`).
- `delete_timesheet` and `delete_day_time_report` are two-phase and cannot be undone.

Read the hours back to the user before submitting on their behalf. If a submit comes back with `outcome_unknown`, read the week with `get_timesheet` before trying again, or the same hours can be entered twice.

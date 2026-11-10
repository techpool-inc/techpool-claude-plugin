# Schedule

The schedule is who works where and when: bookings on the board, hung off missions (jobs). Reading needs `schedule:read`, booking changes need `schedule:write`.

## See the board

1. `get_schedule` with `from` and `to` (ISO dates) for the merged bookings + unavailability window.
2. `list_missions` / `get_mission` to resolve the job a booking hangs off.

## Booking changes

All of these are two-phase: the first call returns a preview with a `confirmToken`, change nothing. Show the preview to the user, then call the same tool again with the same arguments plus `confirm: true` and the `confirmToken`. The token works once and expires after five minutes.

| Job | Tool |
|---|---|
| Book member(s) on dates | `create_booking` |
| Book a call in one step (customer + job + booking) | `quick_create_booking` |
| Move dates/times of a booking | `reschedule_booking` |
| Replace who is on a booking | `assign_technicians` |
| Change type, mission link, billing type, leave type, country | `edit_booking` |
| Set where a booking stands (Scheduled, OnSite, Done) | `change_booking_status` |
| Move one person's dates on a shared booking | `split_booking` |
| Cancel (delete) a booking | `cancel_booking` |

- `change_booking_status` is allowed for a person on the booking or a schedule manager; the preview says nothing is notified.
- `split_booking` moves ONE person on a shared booking to their own booking with the new dates; everyone else keeps theirs.
- `quick_create_booking` reuses or creates the customer and the job in one transaction. Pass the existing `client.id` / `mission.id` to reuse, or a `name` / `title` to create. Empty `memberIds` is the Unassigned lane; `pending: true` saves the customer and job with no booking yet. The company always comes from the connection, never from the arguments.
- `cancel_booking` cannot be undone — name exactly what will be removed before asking for the yes.

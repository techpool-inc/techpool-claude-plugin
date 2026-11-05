# Techpool Books (MCP)

Books tools need the matching `books:*` scope on the grant. Read tools use `books:read`. Writes use narrower scopes (`books:categorize`, `books:journal`, `books:close`, `books:export`, `books:coa`, `books:manage`).

## Common write paths

| Job | Tool | Scope |
|---|---|---|
| Categorize or match bank feed lines | `books_confirm_bank_line`, `books_categorize_bank_line`, `books_exclude_bank_line` | `books:categorize` |
| Statement reconciliation | `books_get_statement_check`, `books_set_statement_check` | read / `books:categorize` |
| Bills and payroll | `books_create_bill`, `books_pay_bill`, `books_create_payroll_run` | `books:categorize` |
| Manual journal | `books_post_journal` | `books:journal` |
| Month-end close / reopen | `books_close_period`, `books_reopen_period` (reopen needs `confirm: true`) | `books:close` |
| Chart / category map | `books_update_chart` | `books:coa` |
| Enable, settings, FS admin, QBO import | `books_enable`, `books_update_settings`, `books_invite_financial_support`, `books_set_financial_support_permissions`, `books_qbo_import` | `books:manage` |
| Audit trail: activity feed, journal entry, report-cell drill-down | `books_activity`, `books_journal_entry`, `books_report_cell` | `books:read` |

Write responses include `journalEntryIds` (ledger entry ids posted or reversed). Amounts are integer cents.

Destructive actions (`books_reopen_period`, `books_disable`, QBO apply) require `confirm: true`. `books_reopen_period` also requires a `reason` of at least 5 characters — the API refuses with `BOOKS__REOPEN_REASON_REQUIRED` when it is missing or too short; the audit trail must say WHY.

## Report-cell drill-down (`books_report_cell`)

Every response satisfies `sum(lines.amountCents) === totalCents`. The `exact` flag says what `totalCents` means:

- `exact: true` (pnl, balance-sheet, cash, and home money-in/out): `totalCents` IS the figure the report shows, reproduced from the same ledger math — the lines reconcile exactly.
- `exact: false` (AR/AP aging, sales tax, 1099, job margin, home margin): the report figure is document/hours math that journal entries alone cannot reproduce; `totalCents` is the sum of the returned lines — the journal-side activity behind the figure, not the figure itself.

`basis`, `asOf` and `year` select the same report view the user clicked (default: company settings / today / current year). `tag` is not implemented and returns a 400.

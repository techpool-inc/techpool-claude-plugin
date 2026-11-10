# Finance seats (MCP)

Finance tools use `invoicing:read`, `invoicing:manage`, and `analytics:read` on the grant. Operations approvals use `@AnyGrant` on the facade — the connection may hold any scopes, but the member must have live `timesheets:approve` and/or `expenses:approve` RBAC.

## Invoicing

| Job | Tool | Scope |
|---|---|---|
| List / get invoices | `list_invoices`, `get_invoice`, `list_invoice_payments` | `invoicing:read` |
| Approved unbilled work | `list_approved_unbilled_work` | `invoicing:read` |
| Create draft | `create_invoice` (builder preview → persist with `send=false`) | `invoicing:manage` |
| Edit draft | `update_draft_invoice` (rebuild + persist; refuses sent invoices) | `invoicing:manage` |
| Delete draft | `delete_draft_invoice` (two-phase; releases billed hours/expenses) | `invoicing:manage` |
| Issue draft | `send_invoice` (two-phase: number + Sent + email) | `invoicing:manage` |
| Void | `void_invoice` (two-phase, reason required) | `invoicing:manage` |
| Manual payment | `record_invoice_payment` (two-phase, optional `amountMinor`) | `invoicing:manage` |
| Reverse payment | `mark_invoice_unpaid` (two-phase: drop Manual payments, back to Sent) | `invoicing:manage` |
| Re-send email | `resend_invoice_email` (two-phase, optional corrected `email`) | `invoicing:manage` |
| Escrow release | `release_invoice_funds` (two-phase; Stripe Connect only, Financial Support refused) | `invoicing:manage` |
| Accounting credit memo retry | `create_credit_memo` (Refunded + already pushed) | `invoicing:manage` |
| Invoice PDF | `get_invoice_pdf` (base64) | `invoicing:read` |
| Bill run reconciliation | `get_bill_run_reconciliation` (optional `before` YYYY-MM-DD) | `invoicing:read` |

Amounts are integer minor units. Office finance access only (`invoicing:read` / `invoicing:manage`), not field invoicing.

## Analytics

| Job | Tool | Scope |
|---|---|---|
| Hours + expenses by mission | `get_finance_insights` | `analytics:read` |

Query params: `pageNumber`, `pageSize` (max 500), `startDate`, `endDate`, `companyMemberIds`, `missionIds`, `hourStatuses`, `expenseStatuses`, `invoicing` — same shape as `GET /agent/finance/insights` (analytics REST filters).

## Approvals

| Job | Tool | MCP scope |
|---|---|---|
| Inbox | `approvals_inbox` | any grant (RBAC in facade) |
| Approve clean batch | `approvals_approve_clean` | any grant (two-phase) |

## Expenses settled outside Techpool

Owner/admin (live `expenses:approve`) on an `expenses:write` grant. Both are two-phase: show the preview, confirm with the user.

| Job | Tool |
|---|---|
| Mark one expense settled outside Techpool, or undo it | `set_expense_external_settlement` |
| Clear a batch of historical expenses out of the invoiceable pool | `bulk_set_expense_external_settlement` (up to 500; billed or foreign ids are skipped with a reason) |

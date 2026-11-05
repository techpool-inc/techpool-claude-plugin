# Finance seats (MCP)

Finance tools use `invoicing:read`, `invoicing:manage`, and `analytics:read` on the grant. Operations approvals use `@AnyGrant` on the facade — the connection may hold any scopes, but the member must have live `timesheets:approve` and/or `expenses:approve` RBAC.

## Invoicing

| Job | Tool | Scope |
|---|---|---|
| List / get invoices | `list_invoices`, `get_invoice`, `list_invoice_payments` | `invoicing:read` |
| Approved unbilled work | `list_approved_unbilled_work` | `invoicing:read` |
| Create draft | `create_invoice` (builder preview → persist with `send=false`) | `invoicing:manage` |
| Issue draft | `send_invoice` (two-phase: number + Sent + email) | `invoicing:manage` |
| Void | `void_invoice` (two-phase, reason required) | `invoicing:manage` |
| Manual payment | `record_invoice_payment` (two-phase, optional `amountMinor`) | `invoicing:manage` |
| Accounting credit memo retry | `create_credit_memo` (Refunded + already pushed) | `invoicing:manage` |

Escrow **release** is not exposed over MCP.

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

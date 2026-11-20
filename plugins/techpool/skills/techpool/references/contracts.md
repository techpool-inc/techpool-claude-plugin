# Contracts (MCP)

Contract tools use `contracts:read` and `contracts:write` on the grant.

## Templates and contracts

| Job | Tool | Scope |
|---|---|---|
| List / get templates | `list_contract_templates`, `get_contract_template` | `contracts:read` |
| List contracts | `list_contracts` | `contracts:read` |
| Get a contract (terms, rate lines, signers, status, amendments) | `get_contract` | `contracts:read` |
| Per-signer signing status | `get_contract_signing_status` | `contracts:read` |
| Create a draft | `create_contract_draft` | `contracts:write` |
| Update a draft | `update_contract` | `contracts:write` |
| Import a draft from an uploaded file | `import_contract_from_file` | `contracts:write` |
| Send an offer | `send_contract_offer` (two-phase: moves to awaiting signatures, emails signers) | `contracts:write` |
| Terminate | `terminate_contract` (two-phase, destructive, reason required) | `contracts:write` |

## Member original documents

The archived originals a company keeps on a member's profile: the person's signed contract as it came back, amendments, other evidence. Bytes store once per (company, member, sha256) — attaching the same file again returns the existing row (`duplicate: true`) and stores nothing. Rows are immutable: there is no update or delete tool. Maximum 25 MB per document; `application/pdf`, `image/*` or `.docx`. A draft can reference an archived original with `originalMemberDocumentId` on `create_contract_draft`.

| Job | Tool | Scope |
|---|---|---|
| List a member's archived documents (metadata) | `list_member_documents` | `contracts:read` |
| Get one document (metadata + exact bytes, base64) | `get_member_document` | `contracts:read` |
| Archive an original document on a member | `attach_member_document` | `contracts:write` |

## Amendments

| Job | Tool | Scope |
|---|---|---|
| Create a draft amendment | `create_contract_amendment` | `contracts:write` |
| Send an amendment | `send_contract_amendment` (two-phase) | `contracts:write` |

## Seatless vendors

A vendor offer does not need an existing company member. `create_contract_draft` and `update_contract` accept `vendorEmail` + `vendorName` in place of `companyMemberId`; `get_contract` returns `companyMemberId: null` for a contract still addressed to a vendor by email. `send_contract_offer` sends to that email the same way it sends to a member.

`create_contract_draft`, `update_contract`, `create_contract_amendment` and `import_contract_from_file` never send anything — they only ever persist a Draft. Only `send_contract_offer` and `send_contract_amendment` notify anyone, and both are two-phase.

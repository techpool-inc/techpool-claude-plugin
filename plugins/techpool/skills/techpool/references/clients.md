# Clients

A client is a customer of the company: the record itself, its contacts, notes, file attachments, proposals and an activity feed. Reading needs `clients:read`, changing needs `clients:write`. Proposals have their own permissions: reading needs `proposals:read`, creating, changing or deleting one needs `proposals:write`.

## Find a client

1. `list_clients` with `search` (name or code) and optionally `stage`. Page with `cursor` while the result says `more_results`.
2. `get_client` with `clientId` for the record and its counts of contacts, proposals and notes.

If two clients match the name the user gave, show both and ask which one. Never pick.

## What is going on with this client

| Question | Tool |
|---|---|
| Who do we talk to there | `list_client_contacts`, `get_client_contact` |
| What have we noted | `list_client_notes`, `get_client_note` |
| What happened recently | `list_client_activities` with `clientId` |
| Which quotes are open | `list_client_proposals`, `get_client_proposal` |
| Files on the client | `list_client_attachments`, `get_client_attachment` |
| Where the whole pipeline stands | `get_client_pipeline`, `get_proposals_board` |

The pipeline and board results are grouped summaries. When their evidence says `records_not_identified`, do not cite record ids from them; open the client or proposal instead.

## Add or change

- `create_client_note` (`clientId`, `text`) and `create_client_contact` (`clientId`, `firstName`, `lastName`, `email`, optional `phone`, `title`, `contactType`) act at once. Read the text or the contact details back to the user before you call them. A note is signed with the connected user's name.
- `update_client_note` and `update_client_contact` change an existing one.
- `create_client` (`name`, optional `code`, `email`, `phoneNumber`) and `update_client` (`clientId`, then `name`, `code` or `stage`) are two-phase: show the preview, then confirm on the user's yes.
- `delete_client`, `delete_client_contact`, `delete_client_note` and `delete_client_attachment` are two-phase and cannot be undone. Name exactly what will be removed before asking for the yes.

Before creating a client, search for it first. A duplicate client is harder to clean up than a missed one.

## Proposals and quotes

`create_proposal`, `update_proposal` and `delete_proposal` are two-phase; setting a proposal to sent may notify the client contact, and the preview says so. `list_proposal_sections` and the section tools manage the company's reusable proposal text, not the text on a quote that already exists. `get_quote_numbering` and `update_quote_numbering` control the company's quote number sequence; change it only when the user asks for exactly that.

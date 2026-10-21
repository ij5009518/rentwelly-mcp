# Rentwelly MCP Server

Connect [Rentwelly](https://www.rentwelly.com) to Claude, ChatGPT or any MCP client and ask about your rental portfolio in plain English.

Rentwelly is property management software with a real double-entry general ledger underneath it. That matters for an AI assistant more than it sounds: reading a true ledger, an assistant can give you a figure that ties out to the balance sheet. Reading categorised bank lines, it can only estimate.

This repository holds the connector's public manifest and documentation. The server is hosted — there is nothing to install or run.

- **Endpoint:** `https://www.rentwelly.com/api/mcp`
- **Transport:** Streamable HTTP
- **Auth:** OAuth 2.1 with dynamic client registration, scopes `read` and `write`
- **Website:** https://www.rentwelly.com

## What you can ask

Once connected, questions like these work directly:

- "What's my rent roll for October?"
- "Which tenants are behind, and by how much?"
- "Show me the P&L for the Maple Street duplex, accrual basis, year to date."
- "What's outstanding over 60 days?"
- "Record a $125 late fee on unit 2B."

## Tools

Six read tools are available to every connected account. Two write tools are off by default and enabled per organisation by the account owner.

| Tool | Access | What it does |
| --- | --- | --- |
| `list_properties` | read | Properties and units in the portfolio |
| `rent_roll` | read | Current rent roll with lease and occupancy detail |
| `list_tenants_with_balances` | read | Tenants and what each one owes |
| `list_open_invoices` | read | Unpaid invoices across the portfolio |
| `profit_and_loss` | read | P&L for a date range, cash or accrual, whole portfolio or one property |
| `ar_aging` | read | Receivables bucketed by age |
| `create_charge` | write | Post a charge to a lease |
| `record_payment` | write | Record a payment against an invoice |

No tool deletes records, voids transactions or changes account settings. Those actions are deliberately not published over MCP.

## Connecting

**Claude** — Settings → Connectors → Add custom connector, then paste the endpoint above. Custom remote connectors work on Free, Pro, Max, Team and Enterprise; the Free plan allows one.

**ChatGPT** — Full connector support including writes is in beta on Business, Enterprise and Edu plans, requires an administrator to enable developer mode, and works on the web. Pro plans are read and fetch only.

Both plan details change often. Check the vendor's own documentation before relying on them.

## How the accounting works

Every action writes a balanced journal entry. Rent, late fees, deposits, vendor bills, management fees and owner draws post to a proper chart of accounts, so the trial balance ties and the balance sheet balances. Reports run on a cash or accrual basis and scope to one property or the whole portfolio.

A database constraint rejects an unbalanced entry at commit time, so a tool call either produces correct double-entry bookkeeping or fails. There is no partial write.

## Security

Access is per organisation and enforced in Postgres through row-level security, not in application code. Rent collection runs on Stripe Connect against the landlord's own Stripe account, so Rentwelly never takes custody of funds and no card data reaches this connector.

More detail: https://www.rentwelly.com

## Licence

Documentation in this repository is MIT licensed. The Rentwelly service itself is a commercial product with a free tier.

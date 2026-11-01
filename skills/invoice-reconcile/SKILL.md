---
name: invoice-reconcile
description: Reconcile a supplier invoice against the purchase record and post the differences as a file, not a screen. Use when a supplier sends an invoice and someone asks whether it matches what was ordered.
---

# Invoice reconcile

How this open-source CompanyOS reconciles a supplier invoice: the steps below are what the agent follows.

## What you need

- The invoice, as a file in the repository or at a URL you can fetch.
- The purchase record: `finance/purchases.csv` in this repository, or the export a person hands you.

## Steps

1. Read both documents. Match rows on the purchase reference, not on the supplier's line order.
2. For every invoice line, record `matched`, `over` or `missing`, with the amount in both documents.
3. Sum the differences. A difference below the tolerance a person set — ask if none is on file, and write the answer to `finance/tolerance.md` — is `within tolerance`, not `matched`.
4. Write `finance/reconciliations/<invoice-number>.md`: one row per line (reference, description, invoiced, ordered, difference, state), then the total difference and the state of the invoice as a whole.
5. If any line is `over` or `missing`, add the invoice to `finance/reconciliations/open.md` with the amount to dispute and the person who owns that supplier.

## Rules

- Never mark an invoice `paid`. Payment is a person's action outside this repository.
- A number you cannot find in the source is `not recorded`. Never fill it in from a total.
- Two invoices for the same reference is a `duplicate`: record both, dispute the later one.

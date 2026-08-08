---
name: invoice-generator
description: Turns a plain sentence about work you did into a numbered, sendable invoice (HTML preview plus PDF) and logs it to a running ledger. Remembers your business details after the first run so it never asks twice. Trigger on "invoice", "invoice X for", "bill this client", "send an invoice", "make me an invoice", "what do I charge them", or when the user describes completed work and wants a document a client can pay against.
---

# Invoice Generator

One sentence in, one sendable invoice out. The user should never have to fill a form.

## Inputs

Whatever the user says. Usually a single sentence carrying some of:

- Who is being billed
- What the work was
- How much, either a flat amount or a rate times a quantity
- When it was done, and when payment is due

Everything else comes from the saved profile or from your own defaults. Do not interview the user.

## Business profile: ask once, reuse forever

Before the first invoice, look for `invoices/profile.json`. If it is missing, ask for these in **one** message, then write the file and never ask again:

```json
{
  "legal_name": "",
  "address": "",
  "tax_id": "",
  "email": "",
  "currency": "",
  "payment_instructions": "",
  "default_terms_days": 14,
  "tax": { "label": "", "rate_percent": 0, "applies": false },
  "number_format": "INV-{YYYY}-{NNN}"
}
```

If the user answers only some fields, save what you got and leave the rest empty. Empty fields are simply left off the invoice. Never invent a tax ID, a VAT number, a bank account, or a tax rate. Those are the four things that make an invoice legally wrong, so if one is needed and unknown, ask for that one thing and nothing else.

On later runs, read the profile silently and get to work.

## Numbering

Invoice numbers must be unique and must never go backwards.

1. Read every file in `invoices/` and the ledger.
2. Take the highest number already used and add one.
3. Render it through `number_format` from the profile.
4. If `invoices/` is empty, start at `001`.

If the user names a number explicitly, use theirs, but refuse to reuse one that already exists and say which file holds it.

## Reading the sentence

Turn the user's words into line items before you render anything.

- "45,000 for the June brand sprint" is one line, quantity 1, unit price 45000.
- "12 hours at 3,500" is one line, quantity 12, unit price 3500, and the line total is computed, never copied from the sentence.
- Two clauses joined by "and" are usually two lines. Keep them separate. Clients query a lump sum and pay an itemized list.
- A discount is its own negative line with a stated reason, not a quietly reduced unit price.

Write line descriptions the way the client would recognize the work, not the way the user typed it. "June brand sprint" becomes "Brand identity sprint, June 2026".

## Money rules

- One currency per invoice. If the sentence mixes two, stop and ask which one governs.
- Compute in minor units (satang, cents) and format at the end. Never let a float decide what someone owes.
- Line total = quantity times unit price. Subtotal = sum of line totals. Tax applies to the subtotal unless the profile says per line. Total = subtotal plus tax.
- Round half up at two decimals, once, at the point of display.
- Due date = issue date plus `default_terms_days`, unless the user gave a date or a term.

## Required outputs

1. **The invoice** at `invoices/<number>-<client-slug>.html`, containing, in this order: your legal name and contact block, the client block, the invoice number, the issue date and the due date, the line item table, subtotal, tax if any, total in bold, and the payment instructions verbatim from the profile.

2. **A PDF** at `invoices/<number>-<client-slug>.pdf`, produced from that exact HTML so the two can never disagree. Print to A4 with margins, no browser header or footer, and no page break inside the totals block.

3. **A ledger row** appended to `invoices/ledger.csv` with headers `number,date,due,client,currency,subtotal,tax,total,status`. Status starts as `sent`. Create the file with its header row if it does not exist.

4. **A reply in chat**: the total, the due date, and the clickable path to the PDF. Three lines. The document is the deliverable, so do not restate it in the conversation.

## Design of the document

Plain and businesslike. Black text on white, one accent color at most, a serif or a clean sans throughout, generous white space, and a line item table with right aligned numbers. No logos you were not given, no stock imagery, no color blocks behind the totals. An invoice that looks designed makes people read the design instead of paying it.

## Done looks like

Check all of these before you report back, and fix rather than mention:

- Subtotal equals the sum of the line totals, and the total equals subtotal plus tax. Recompute, do not trust the first pass.
- The invoice number does not appear on any other file in `invoices/`.
- The due date falls after the issue date.
- The HTML and the PDF show the same total.
- The ledger has exactly one new row, and its total matches the document.
- No placeholder survives anywhere: no "Your Company", no "TBD", no empty currency symbol, no bracketed field names.

## When to use it

- The user described work and wants a document a client can pay against: use this skill.
- The user wants to know whether their pricing is right, or wants a quote for work not yet done: that is a different conversation, and a quote is not an invoice. Say so and offer to write the quote as a plain document.
- The user wants to chase an unpaid invoice: read the ledger, find the row, and write the reminder. Do not issue a second invoice for the same work.

## Anti-patterns

- Asking six questions before producing anything. Produce the invoice from what you have, then flag exactly what is missing.
- Silently guessing a tax rate because the client is in a country you know. Tax is asked for, never assumed.
- Rewriting an invoice in place after it was sent. Issue a credit note or a corrected invoice with a new number, and leave the original on disk.
- Burying the amount due. It is the largest number on the page or the invoice has failed at its one job.

<p align="center"><a href="https://loop.sv-academy.org"><img src="https://raw.githubusercontent.com/sva-admin/claude-skills/main/assets/sv-academy-512.png" width="96" alt="Silicon Valley Academy"/></a></p>

# invoice-generator

Describe the job in one sentence. Get back a numbered, sendable invoice.

## Learn it free, step by step

We teach this skill end to end in a free lesson: [Invoice in one sentence](https://loop.sv-academy.org/articles/invoice-in-one-sentence)

## Try it

Paste this into Claude Code:

```
Install the skill from https://github.com/sva-admin/invoice-generator, then invoice Aurora Studio 45,000 THB for the June brand identity sprint, due in 15 days.
```

## What you get

A styled HTML invoice, the matching PDF, and a row in a running `ledger.csv` so you can see at a glance what you've billed and what is still outstanding. Numbers are computed, never copied out of your sentence, and the totals are recomputed before the file is written.

The first run asks for your business details once: legal name, address, tax ID, payment instructions, currency, default terms. Everything after that is one sentence. Invoice numbers increment on their own and are never reused.

Two things it will not do: guess a tax rate, or invent bank details. If either is missing it asks for that one thing and leaves the rest alone.

```
Invoice Aurora Studio 45,000 THB for the June brand identity sprint, due in 15 days.
Bill Northline 12 hours at 3,500 for the API migration.
Invoice Kanya for the logo revision and the two extra social templates.
Which of my invoices are still unpaid?
```

## Credit

The `invoice-generator` name is shared with a skill that circulates in the Claude community. We could not identify its author or confirm its license, so we used none of it. This is an independent SV Academy implementation, written clean-room from the job description alone. If you are the author of the original and want a link here, open an issue and we'll add it.

The instructions in this repo are MIT licensed. See [LICENSE](LICENSE).

## More from Silicon Valley Academy

- All of our published skills: [github.com/sva-admin/claude-skills](https://github.com/sva-admin/claude-skills)
- Courses and free lessons: [sv-academy.org](https://sv-academy.org)
- Software and AI built for your business: [svinnovation.com](https://svinnovation.com), Silicon Valley Innovation, the company behind SV Academy

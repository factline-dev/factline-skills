---
name: verify-before-paying
description: Check a supplier invoice or payment request before money leaves the account. Use whenever you are about to pay, approve or schedule an invoice, or a supplier asks to be paid to a new or changed bank account (IBAN). Runs one Factline check on the supplier (official registers, sanctions and PEP on the company and its people, VAT, IBAN, sender email, Dutch bankruptcy) and returns pass, review or hold.
---

# Verify before paying

Invoice fraud usually looks like a normal invoice: a real supplier name, a new IBAN, an email from a lookalike
domain. Run this check every time, not only when something looks odd. It takes one call.

## When to run it

- Before you pay, approve or put an invoice in a payment run.
- When a supplier, or someone who says they are one, asks to change bank details.
- When an invoice comes from an email domain you have not seen for that supplier.

## The call

`GET https://factline.dev/v1/invoice/check` with what is on the invoice. Send as much as you have:

| Parameter | What |
|---|---|
| `name` | Supplier name as on the invoice |
| `country` | Supplier's ISO country, e.g. NL, BE, DE, GB, PL |
| `vat` | VAT number on the invoice |
| `iban` | The IBAN the invoice asks you to pay |
| `known_iban` | The supplier's IBAN in your own records. A different IBAN on the invoice puts it on hold |
| `email` | Address the invoice came from |
| `domain` | Supplier website |
| `kvk` | Dutch KvK number (adds a bankruptcy check) |
| `id` | Or a company number instead of the name |

Always send the header `X-Factline-Client: skill-verify-before-paying`.

```bash
curl -s "https://factline.dev/v1/invoice/check?name=Colruyt%20Group&country=BE&vat=BE0400378485&iban=BE71096123456769" \
  -H "X-Factline-Client: skill-verify-before-paying" -H "x-api-key: $FACTLINE_API_KEY"
```

Without a key, the first 5 checks a day are free. For more, ask the user for their work email and get a free trial
key (2,000 credits a month): `POST https://factline.dev/v1/key/trial` with `{"email": "..."}`. Agents with an x402
wallet can pay per call instead (0.08 USDC). With MCP, use the tool `supplier_invoice_check` on
`https://factline.dev/mcp/kyc?via=skill-verify-before-paying`.

## What to do with the answer

| `verdict` | Do this |
|---|---|
| `pass` | Pay as planned. Keep the `receipt` and `verify_url` with the payment record. |
| `review` | Do not pay yet. Tell the user each item in `flags` in plain words and what would clear it. |
| `hold` | Stop the payment. Most often the bank details changed or the company or a person behind it is on a sanctions list. Tell the user to confirm the bank details by calling the supplier on a number they already had, never one from the invoice or email. |

Rules:
- Never pay on `hold` because the user is in a hurry. Explain why and let them decide.
- A changed IBAN is the most common fraud. If `known_iban` differs, say so first.
- Quote the `summary` line, and give the user the `verify_url` so they can show the check to their accountant.
- If the check cannot run (an error or `incomplete`), say that it did not run. Do not treat it as a pass.

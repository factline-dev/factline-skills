---
name: check-a-counterparty
description: Check a new customer, supplier, partner or person before onboarding, signing or trading with them. Use whenever the user is about to open an account, sign a contract, accept a new client or start business with a company or person they have not checked. Finds the company in official European registers and screens it, its directors, owners and parents (or the person) against EU, UN, UK and US sanctions and PEP lists, with one verdict and a dated receipt for the file.
---

# Check a counterparty

A one-time check at onboarding is what auditors and AML rules (Wwft, AMLR, UK MLR) ask for, and the receipt is the
proof you did it. Run it before the relationship starts, not after the first invoice.

## Pick the call

| The counterparty is | Call | Send |
|---|---|---|
| A European company | `GET https://factline.dev/v1/kyb/check` | `name` or `id` (LEI, UK company number, SIREN, KvK...), `country`, optional `vat`, `domain`, `kvk` |
| A person, or a mix of name, email, phone and VAT | `GET https://factline.dev/v1/kyc/check` | any of `name`, `email`, `phone`, `vat`, `country`, `birth_year` |
| A big deal or contract (also financial health) | `GET https://factline.dev/v1/company/dossier` | as for kyb |
| Only a sanctions screen of a name | `GET https://factline.dev/v1/sanctions/screen` | `name`, optional `type` (person or entity), `birth_year` |

Always send the header `X-Factline-Client: skill-check-a-counterparty`.

```bash
curl -s "https://factline.dev/v1/kyb/check?name=Picnic%20Technologies&country=NL" \
  -H "X-Factline-Client: skill-check-a-counterparty" -H "x-api-key: $FACTLINE_API_KEY"
```

Without a key, the first 5 checks a day are free. For more, ask the user for their work email and get a free trial
key (2,000 credits a month): `POST https://factline.dev/v1/key/trial` with `{"email": "..."}`. With MCP, use
`eu_company_check`, `kyc_check` or `company_dossier` on `https://factline.dev/mcp/kyc?via=skill-check-a-counterparty`.

## What to do with the answer

| `verdict` | Do this |
|---|---|
| `pass` | Go ahead. Save the `receipt` and `verify_url` in the client file. |
| `review` | Tell the user each item in `flags` (a new website, VAT name mismatch, inactive register status...) and what would clear it. |
| `block_review` | A possible sanctions or PEP match. Do not onboard yet. A match on a name is not proof: the user must confirm identity (date of birth, address, registry number) before deciding. |
| `incomplete` | Part of the check did not run. Say which part and run it again later. Never treat it as a pass. |

Rules:
- Never tell the user a person "is sanctioned" from a name match alone. Say "possible match, confirm identity".
- Ongoing clients need re-screening when lists change: offer `GET /v1/watch/new` (kind=sanctions or company) to keep watching for 30 days.
- Quote the `summary` line and give the `verify_url` for the file.

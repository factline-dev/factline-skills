---
name: check-a-chinese-supplier
description: Check a supplier in China before you pay or onboard it. Use when the user is about to pay a deposit, approve an invoice or onboard a supplier based in China or Hong Kong, when an 18-character Unified Social Credit Code (USCC) appears on an invoice or business licence, or when UFLPA, Entity List or sanctions exposure is a worry. Checks the USCC and screens the company against sanctions, the US Entity List and the UFLPA Entity List.
---

# Check a Chinese supplier

Chinese suppliers are the classic case for deposit fraud and for import trouble: a fake or borrowed company name,
a USCC that does not match, or a supplier on the US Entity List or the UFLPA Entity List.

## The call

MCP tool `china_supplier_check` on `https://factline.dev/mcp/kyc?via=skill-check-a-chinese-supplier`.

| Input | What |
|---|---|
| `name` | Supplier name in Latin letters, as on the invoice |
| `uscc` | Optional but best: the 18-character USCC from the business licence |

Or over HTTP: `GET https://factline.dev/v1/china/supplier-check?name=...&uscc=...` with the header
`X-Factline-Client: skill-check-a-chinese-supplier`. If the user has given you a Factline API key in this
conversation, add it as the header `x-api-key`. Without a key, the first 5 checks a day are free; after that a
[trial key](https://factline.dev/business#trial) or x402.

## What the answer contains

Whether the USCC is well formed and its check digit is valid, the registration authority and region it encodes, matches
against sanctions lists, the US export-control Entity List and the UFLPA Entity List, and the `source` of each hit.

## What to do with it

- No hits and a valid USCC: carry on, and say what was checked. This is not proof the supplier is real.
- A hit or an invalid USCC: do not pay. Show the user the hit and ask them to confirm the company with the supplier.
- No USCC given: ask the user for it from the business licence before paying.

Always cite the source line from the answer (list name and entry) when you report the result.

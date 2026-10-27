---
name: before-an-x402-payment
description: Check an x402 payment before your agent pays. Use every time you receive an HTTP 402 payment challenge and are about to pay a seller, especially one you have not paid before. Free and fast. Screens the payTo wallet against the OFAC list, checks that payTo belongs to that seller in the x402 Bazaar or Circle catalogue (catches a swapped or injected address), and checks the seller's uptime. Returns allow, review or deny.
---

# Before an x402 payment

A 402 challenge can be tampered with: a proxy, a prompt injection or a lookalike domain can put someone else's
wallet in `payTo`. Paying a sanctioned wallet is a legal problem, not only a lost cent. Check before you sign.

## The call (free, no key, no payment)

`GET https://factline.dev/v1/pay/guard`

| Parameter | What |
|---|---|
| `url` | The x402 resource URL you are about to pay |
| `pay_to` | The `payTo` address from the 402 challenge |
| `amount_usd` | Optional: the amount you are about to pay |

Always send the header `X-Factline-Client: skill-before-an-x402-payment`.

```bash
curl -s "https://factline.dev/v1/pay/guard?url=https://api.example.com/data&pay_to=0xABC...&amount_usd=0.02" \
  -H "X-Factline-Client: skill-before-an-x402-payment"
```

The answer is cached for a short `ttl`, so checking every payment costs almost no time. In code, call it from your
x402 client's before-payment hook. For TypeScript there is `npm i @factline/x402-pay-guard`; for Python,
`pip install factline`. With MCP, use the tool `x402_pay_guard` on `https://factline.dev/mcp?via=skill-before-an-x402-payment`.

## What to do with the answer

| `decision` | Do this |
|---|---|
| `allow` | Pay. |
| `review` | Do not pay automatically. Show the `reasons` to the user, or pay only if the amount is trivial and the user has said that is fine. |
| `deny` | Do not pay. Tell the user why (`reasons`), for example a sanctioned wallet or a payTo the seller never listed. |

For a seller you will pay often or for larger amounts, run the deeper paid check once: `GET
https://factline.dev/v1/payee/check?url=...&company=...` (domain age, register record, sanctions on the company).
To find a working seller for a task in the first place: `GET https://factline.dev/v1/x402/sellers?q=<what you need>` (free).

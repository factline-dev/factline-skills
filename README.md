# Factline skills

Agent skills (SKILL.md) that make an AI agent check before it acts: before it pays an invoice, onboards a customer or a Chinese supplier,
pays an x402 seller, or answers a question about a Dutch home or car. Each skill is one procedure with one Factline
call and tells the agent what to do with each verdict.

| Skill | The agent runs it when | Call |
|---|---|---|
| [verify-before-paying](skills/verify-before-paying/SKILL.md) | it is about to pay or approve a supplier invoice, or bank details change | `/v1/invoice/check`: pass, review or hold |
| [check-a-counterparty](skills/check-a-counterparty/SKILL.md) | a new customer, supplier or partner is onboarded | `/v1/kyb/check`, `/v1/kyc/check`: registers, sanctions, PEP |
| [check-a-chinese-supplier](skills/check-a-chinese-supplier/SKILL.md) | a supplier in China or Hong Kong is paid or onboarded, or a USCC appears | `/v1/china/supplier-check`: USCC, sanctions, Entity List, UFLPA |
| [before-an-x402-payment](skills/before-an-x402-payment/SKILL.md) | it gets an HTTP 402 challenge and is about to pay | `/v1/pay/guard`: allow, review or deny (free) |
| [dutch-address-and-vehicle-facts](skills/dutch-address-and-vehicle-facts/SKILL.md) | a Dutch address, postcode or licence plate comes up | `/v1/home/basic`, `/v1/vehicle/basic` and more (free) |

Data comes from official registers and lists: national company registers and GLEIF, EU, UN, UK and US sanctions,
EU VIES, Peppol, the Dutch insolvency register, BAG, CBS and RDW. Every answer has a `cite_as` line, and paid
answers a signed receipt with a `verify_url` the user can open.

## Install

**Claude Code** (skills plus the free Factline MCP server, the same one as the Claude directory connector):

```
/plugin marketplace add factline-dev/factline-skills
/plugin install factline@factline
```

**Any agent that reads SKILL.md** (Claude Code, Codex CLI, Cursor, Gemini CLI and others):

```bash
npx skills add factline-dev/factline-skills
```

**Cursor** (the MCP server only, one click): [Add Factline to Cursor](https://cursor.com/install-mcp?name=factline-kyc&config=eyJ1cmwiOiJodHRwczovL2ZhY3RsaW5lLmRldi9tY3Ava3ljIn0=)

Or copy the folders in `skills/` into your agent's skills directory (`.claude/skills/`, `.agents/skills/` ...).

## Data

The skills send the inputs of each check (company name or number, VAT, IBAN, a person's name, an email address,
phone number, wallet, Dutch address or plate, an x402 URL) to the Factline REST API at https://factline.dev, the same
service as the MCP server. Nothing else leaves the agent. Factline does not store inputs as such; some answers are
cached for 1 to 30 days, person names and wallets are never cached, and a trial key keeps the email it was issued to.
Full details: https://factline.dev/privacy

## Keys and price

The pay guard and the Dutch address and vehicle basics are free. Paid checks: the first 5 calls a day per IP are free,
then a free trial key (2,000 credits a month, `POST https://factline.dev/v1/key/trial` with your work email) or pay
per call over x402 in USDC. Prices: https://factline.dev/catalog

The skills send `X-Factline-Client: skill-<name>` so we can see which skill is used. It holds the skill name only,
never the query.

## Links

- Docs and setup for every client: https://factline.dev/agents
- MCP servers: `https://factline.dev/mcp` (all), `/mcp/kyc`, `/mcp/nl`, `/mcp/vehicles`
- OpenAPI: https://factline.dev/openapi.json
- Questions: hello@factline.dev

MIT licensed.

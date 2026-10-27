# Factline skills

Agent skills (SKILL.md) that make an AI agent check before it acts: before it pays an invoice, onboards a customer,
pays an x402 seller, or answers a question about a Dutch home or car. Each skill is one procedure with one Factline
call and tells the agent what to do with each verdict.

| Skill | The agent runs it when | Call |
|---|---|---|
| [verify-before-paying](skills/verify-before-paying/SKILL.md) | it is about to pay or approve a supplier invoice, or bank details change | `/v1/invoice/check`: pass, review or hold |
| [check-a-counterparty](skills/check-a-counterparty/SKILL.md) | a new customer, supplier or partner is onboarded | `/v1/kyb/check`, `/v1/kyc/check`: registers, sanctions, PEP |
| [before-an-x402-payment](skills/before-an-x402-payment/SKILL.md) | it gets an HTTP 402 challenge and is about to pay | `/v1/pay/guard`: allow, review or deny (free) |
| [dutch-address-and-vehicle-facts](skills/dutch-address-and-vehicle-facts/SKILL.md) | a Dutch address, postcode or licence plate comes up | `/v1/home/basic`, `/v1/vehicle/basic` and more (free) |

Data comes from official registers and lists: national company registers and GLEIF, EU, UN, UK and US sanctions,
EU VIES, Peppol, the Dutch insolvency register, BAG, CBS and RDW. Every answer has a `cite_as` line, and paid
answers a signed receipt with a `verify_url` the user can open.

## Install

**Claude Code** (skills plus the Factline MCP server):

```
/plugin marketplace add factline-dev/factline-skills
/plugin install factline@factline
```

**Any agent that reads SKILL.md** (Claude Code, Codex CLI, Cursor, Gemini CLI and others):

```bash
npx skills add factline-dev/factline-skills
```

Or copy the folders in `skills/` into your agent's skills directory (`.claude/skills/`, `.agents/skills/` ...).

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

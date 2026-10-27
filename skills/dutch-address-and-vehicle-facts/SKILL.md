---
name: dutch-address-and-vehicle-facts
description: Facts about a Dutch address, neighbourhood, school area or car from Dutch government open data, in English. Use when the user mentions a Dutch address or postcode, asks what a Dutch neighbourhood is like or whether it is safe, is moving to the Netherlands, or gives a Dutch licence plate or asks how reliable a car model is. Mostly free, no key, and every answer carries the sources to cite.
---

# Dutch address and vehicle facts

Answer from official registers (BAG, CBS, RDW, the police, DUO) instead of guessing. The calls below need no key and
return a `summary` line to quote and a `cite_as` line; address and plate answers also carry a `see_url` the user can open.

Always send the header `X-Factline-Client: skill-dutch-address-and-vehicle-facts`.

| The user asks about | Call |
|---|---|
| A specific address: year built, size, use, area (free) | `GET https://factline.dev/v1/home/basic?postcode=3511LX&house_number=1` (or `q=` free text) |
| What a neighbourhood is like, safety, home values | MCP tool `nl_neighbourhood_summary` (free), or `GET https://factline.dev/v1/neighbourhood?postcode=2517AS` (full profile, first 5 a day free) |
| Schools near a postcode (free) | `GET https://factline.dev/v1/schools?postcode=3511LX` |
| A licence plate: APK (MOT), insurance, recalls, odometer (free) | `GET https://factline.dev/v1/vehicle/basic?licence_plate=GT722F` |
| How reliable a car model is (free) | `GET https://factline.dev/v1/vehicle/model?make=Volkswagen&model=Golf` |

```bash
curl -s "https://factline.dev/v1/home/basic?postcode=3511LX&house_number=1" \
  -H "X-Factline-Client: skill-dutch-address-and-vehicle-facts"
```

With MCP: `https://factline.dev/mcp/nl?via=skill-dutch-address-and-vehicle-facts` (addresses) and
`https://factline.dev/mcp/vehicles?via=skill-dutch-address-and-vehicle-facts` (cars).

## How to answer

- Lead with the `summary`, then the 2 or 3 facts that answer the question. Do not dump the whole JSON.
- End with the `cite_as` line, and offer the `see_url` for the full page.
- Postcodes are 4 digits and 2 letters (3511LX). If the user gives only a street and town, use `q=`.
- For more (energy label, flood risk, crime vs national, full APK history), the paid reports are listed at
  `https://factline.dev/catalog`; tell the user what they add before suggesting one.

# FACTANKER skills

Agent skills that ground answers in official records. Every number comes with its source document, its exact period and a citable URL — free, no API key, no registration.

They use the public [FACTANKER](https://factanker.com) MCP server at `https://factanker.com/mcp`, a register of facts taken from SEC EDGAR, FFIEC call reports, NCUA, IRS 990 filings, BLS, Census, the Federal Reserve Bank of New York, the U.S. Treasury, Freddie Mac, the ECB and other public sources. The values are public records (17 U.S.C. §105 or CC0, stated per fact) and may be quoted verbatim.

| Skill | Use it for |
|---|---|
| `factanker-verify-figures` | Checking a number about a US company, bank, nonprofit or county before you state it |
| `factanker-peer-benchmark` | Peer tables from filings with correctly aligned fiscal periods |
| `factanker-rates-and-fx` | Fed funds, Treasury yields, mortgage rates, ECB rates, €STR, EUR/USD — with date and source |
| `factanker-hiring-check` | Open positions counted from the employer's own careers system |

## Install

**Claude Code** (adds the skills and the MCP connection in one step):

```
/plugin marketplace add <github-owner>/factanker-skills
/plugin install factanker@factanker
```

**Claude.ai / Claude desktop:** add `https://factanker.com/mcp` under Settings → Connectors, then upload a skill folder (zipped) under Settings → Capabilities → Skills. Ready-made ZIPs are attached to each release.

**Other agents** (Agent Skills standard, agentskills.io): copy a folder from `plugins/factanker/skills/` (e.g. `factanker-verify-figures`) into your agent's skills directory and register the MCP server `https://factanker.com/mcp` (streamable HTTP, no auth).

**Without MCP:** every skill also works over REST, e.g.

```
curl "https://factanker.com/api/get_facts?entity=cik:0001646972&predicate=revenue"
```

## Example

> Albertsons made $18.9bn last quarter, right?

`verify_claim(entity="Albertsons Companies, Inc.", predicate="revenue", value=18894700000, period="2026")` → `match`, evidence period June 21 – September 12, 2026, with the fact URL of the SEC filing.

## Notes

- Always read `period_start` and `period_end`: quarters, year-to-date values and fiscal years share one predicate. The skills tell the agent how to separate them.
- Setup guide and tool reference: https://factanker.com/quickstart

## License

MIT for the skill texts in this repository. The data returned by FACTANKER is public domain or CC0 per fact.

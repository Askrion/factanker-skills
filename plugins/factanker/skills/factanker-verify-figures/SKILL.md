---
name: factanker-verify-figures
description: Check a number before you state it. Use when an answer, memo, model or board pack contains a figure about a US company, bank, credit union, nonprofit, county or federal program (revenue, net income, total assets, deposits, headcount-like counts, grants, population, prices) and the figure should be verified against the official filing or register, with a citable source URL. Free, no API key.
compatibility: Needs the FACTANKER MCP server (https://factanker.com/mcp, remote, no auth). Falls back to the REST API with any HTTP tool.
---

# Verify figures against official records (FACTANKER)

FACTANKER is a free, keyless register of facts taken from official sources (SEC EDGAR, FFIEC call reports, NCUA, IRS 990, BLS, Census, Federal Reserve, ECB and others). Every value carries its source document, period and a citable `fact_url`.

## When to use

- You are about to write a figure about a named US entity and you did not get it from a primary source in this conversation.
- A user hands you a figure and asks whether it is right.
- You are drafting something that will be read by auditors, investors, journalists or a board.

## Workflow

1. **Identify the entity.** Prefer an identifier over a name: `cik:<10 digits>` for SEC filers, `lei:...`, `ein:...`, `uei:...`. If you only have a name, call `lookup_entity` with `name_or_id` and pick the result whose `type` and anchors match (names are ambiguous; "Albertsons" matches pharmacies and the listed company).
2. **Check the claim.** Call `verify_claim` with `entity`, `predicate` (for example `revenue`, `net_income`, `total_assets`), `value` (the number you are about to state, in plain units, e.g. 18894700000 for $18.89 billion) and `period` (a year such as `2026`). Optional `tolerance_percent` (default 1).
3. **Read the verdict and the evidence period.** The result has `verdict` (`match`, `mismatch`, …) and `evidence` with `period_start`, `period_end`, `value`, `fact_url`.
   - **Always read `period_start` and `period_end`.** Companies file quarters, year-to-date values and fiscal years under the same predicate. A "2026" match can be a single quarter. State the exact period you verified (e.g. "quarter ended September 12, 2026"), never just the year.
   - Respect `value_status`: `observed` may be quoted as reported; `derived`, `estimated` or `imputed` must be labelled as such.
4. **Cite.** Put the `fact_url` next to the number. Values are public records (17 U.S.C. §105 / CC0, stated per fact) and may be quoted verbatim.

## If you need the raw values instead

Call `get_facts` with `entity` and `predicate`. Results are newest period first and capped at 500. Filter by `period_start`/`period_end` yourself to separate quarters from full years (a fiscal year spans roughly 52 weeks).

Do **not** rely on `get_timeseries` for company financials that are filed quarterly: its yearly points can be a single quarter and do not show the period dates. Use `get_facts` and pick the full-year rows.

## REST fallback

```
GET https://factanker.com/api/get_facts?entity=<id or name>&predicate=revenue
```

Same fields as the MCP tool. No key, no registration.

## Example

User: "Albertsons made $18.9bn last quarter, right?"
1. `verify_claim(entity="Albertsons Companies, Inc.", predicate="revenue", value=18894700000, period="2026")` → `match`, evidence period 2026-06-21 to 2026-09-12.
2. Answer: "Yes. Albertsons Companies reported revenue of $18,894,700,000 for the 12 weeks from June 21 to September 12, 2026 (SEC filing; https://factanker.com/fact/42855be8-4b5d-4dcd-a633-d9f6f62fce26)."

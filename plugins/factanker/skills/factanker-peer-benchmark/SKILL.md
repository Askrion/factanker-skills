---
name: factanker-peer-benchmark
description: Benchmark a US public company or bank against its peers on reported financials (revenue, net income, total assets, deposits, equity) with every number traced to the SEC or FFIEC filing. Use for competitor comparisons, board-pack peer tables, credit memos, M&A screens and "how does X compare to Y and Z" questions. Currently free of charge, no API key.
compatibility: Needs the FACTANKER MCP server (https://factanker.com/mcp, remote, no auth).
---

# Peer benchmark from filings (FACTANKER)

## Workflow

1. **Resolve every company first.** Call `lookup_entity` for each name and keep the `entity_id` or the `cik` anchor. Pass `cik:<10 digits>` from then on; names like "Kroger" can match several registrants.
2. **Pull the values.** For each company call `get_facts(entity=..., predicate=...)`. Common predicates: `revenue`, `net_income`, `operating_income`, `total_assets`, `cash_and_equivalents`, `common_shares_outstanding`; for banks (FFIEC call reports) `total_assets`, `bank_deposits_interest`, `bank_equity_capital`. Unsure what a predicate means or which entity types carry it: `describe_predicate(predicate=...)`.
3. **Align the periods – this is the step that makes or breaks the table.**
   - Each fact has `period_start` and `period_end`. Quarters, year-to-date values and fiscal years share one predicate.
   - For a fiscal-year table keep only rows spanning roughly 52 weeks. For a quarterly table keep only rows of about 12–16 weeks and note that retailers use 12- and 16-week quarters.
   - Fiscal years end in different months. Put the period end in the table instead of pretending the years line up.
   - Balance-sheet items (assets, cash, shares) carry a single date (`as_of` or `period_end`); compare the closest dates and show them.
4. **Build the table** with one row per company: value, period start–end, `fact_url`. Derived figures (margins, growth) are your own calculation: show the formula and the two source facts.
5. **Optional shortcut:** `compare_entities(entities=[...], predicate=...)` returns the latest shared year for 2–12 entities in one call. Its `period` field is a year label only, so check each value with `get_facts` before you publish it.

## Rules

- Never mix a quarter with a full year in one column.
- Quote values exactly as filed; round only in prose and say so.
- Every number in the output gets its `fact_url`.

## Example output row

| Company | Revenue | Period | Source |
|---|---|---|---|
| Albertsons Companies, Inc. | $79,237,700,000 | 2023-02-26 to 2024-02-24 (fiscal 2023) | https://factanker.com/fact/… |

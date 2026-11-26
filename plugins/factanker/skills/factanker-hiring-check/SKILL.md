---
name: factanker-hiring-check
description: How many jobs is a company hiring for right now, counted from the employer's own careers system rather than job-board estimates, with postings added and closed in the past seven days and the largest role groups. Use for "is X hiring", "how many open positions does X have", workforce or expansion signals in research, sales prospecting and credit notes. Currently free of charge, no API key.
compatibility: Needs the FACTANKER MCP server (https://factanker.com/mcp, remote, no auth).
---

# Open positions from the employer's own careers system (FACTANKER)

Job aggregators disagree by a factor of two or more because they deduplicate differently. FACTANKER counts positions (company, title, city) directly from the employer's applicant-tracking system and keeps the count as a dated fact.

## Workflow

1. Resolve the company with `lookup_entity(name_or_id=...)`; prefer the result with a `cik` anchor for listed companies.
2. Call `get_facts(entity=<entity_id or cik:...>, predicate="open_positions")`.
3. Read the value object:
   - `value` – open positions on the observation date
   - `new_7` – positions posted in the past seven days, `ended_7` – positions closed in the past seven days
   - `roles` – largest role groups with counts, e.g. `["deli clerk", 650]`
   - the observation date is the fact's `as_of`
4. Answer with the number, the date and the `fact_url`. If no `open_positions` fact exists, say that FACTANKER does not observe this employer – do not fall back to a guess.

## Wording

"<Company> lists 6,103 open positions on its own careers system as of November 17, 2026; 683 were posted in the past seven days (FACTANKER, <fact_url>)."

A human-readable page per company is linked from the fact (`/jobs/company/<entity_id>`), and a daily overview of hiring answers is at https://factanker.com/asked.

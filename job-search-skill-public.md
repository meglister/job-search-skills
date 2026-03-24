---
name: job-search
description:
  Searches the web for active job postings that match [Name's] rubric and filters.
  Use this skill whenever [user] asks to find jobs, run a job search, look for new roles,
  or anything about finding open positions. Also trigger when I say things like "find me
  some roles", "what's out there", or "run a search". This skill applies my personal scoring
  rubric and hard filters automatically — do not skip any step.
---

# Job Search Skill

Finds active, well-matched [job type] postings for [user] using her personal rubric.
Outputs verified live links with a fit summary for each role.

## Rubric Reference

The full rubric lives at:
[link]

Always read the rubric before scoring — it may have been updated since your last run.
Use the `Read` tool on:
~/.claude/skills/job-search/rubric.md

The rubric has two parts:

1. **Scoring criteria** (sections 1–5) — used to evaluate and rank roles
2. **Hard filters** (section 6) — used to immediately exclude roles before scoring

---

## Step 0: Load Exclusion Lists

Before searching, read both exclusion files and build a combined set of URLs to skip:

1. **Reviewed links** — `Read` the project memory file `job_search_reviewed_links.md` (in the active project's memory directory)
2. **Previously surfaced links** — `Read` `~/.claude/skills/job-search/surfaced_links.md`

Any URL appearing in either file must be excluded from results this run — do not include it even if it scores well.

Do not write to either file. The shell script handles updating `surfaced_links.md` after the run.

---

## Step 1: Apply Hard Filters First

Before scoring anything, discard any role that hits even one of these:

- **Geography**: Not open to US-based candidates, or requires location in a specific non-US country (e.g. "must be in Argentina/Brazil/Spain") even if labeled "remote"
- **Salary**: Listed or estimated base below $[X]. Flag $[X]K as "worth verifying". Prefer $[X]K+
- **Role type**: Contract, 1099/freelance, part-time, or internship — must be full-time salaried with benefits
- **Company stage**: Seed-stage with no meaningful funding, revenue, or traction signal
- **Industry**: Healthcare, gaming, or gambling — exclude regardless of tech stack
- **Domain**: Role has no connection to any of the following — exclude it: fintech/payments/crypto/Web3, adtech/martech (especially with AI features), marketplace or platform SaaS with complex mechanisms, B2B data/analytics platforms. Pure general-purpose SaaS with no signal in any of these areas is excluded.

---

## Step 2: Search Strategy

Run exactly 4 searches using `mcp__exa__web_search_exa` with `livecrawl: "preferred"` to maximize freshness. Use natural-language queries — Exa does not support `site:` operators.

**Coverage requirement: at least 2 of the 4 searches must target non-crypto domains** (martech/adtech, marketplace SaaS, data platforms). Do not run all 4 on crypto/fintech.

Example query patterns — mix and match, but respect the coverage requirement:

**Crypto/fintech queries (use at most 2):**

```
Senior or staff product manager roles in crypto infrastructure at Series B companies, remote, with salary info.
Senior product manager roles in payments and stablecoin at Series C companies, remote US, with salary info.
Senior product manager roles in blockchain or web3 infrastructure at Series B or Series C companies, remote, job listing.
Senior product manager roles in B2B SaaS for financial services, risk or compliance, remote US, job listing.
Head of product or director of product roles in crypto or digital assets at Series B, remote-friendly, job listing.
Senior product manager roles in lending or credit fintech, US-based, remote, job listing.
Senior product manager roles in treasury or payments infrastructure for banks or fintech, remote US, job listing.
```

**Non-crypto queries (use at least 2):**

```
Senior product manager roles in adtech or martech SaaS with AI features, Series B or C, remote US, job listing.
Staff product manager roles in programmatic advertising or identity resolution or audience data, remote, job listing.
Group product manager roles in B2B marketplace or platform SaaS with complex pricing or data mechanisms, remote, job listing.
Senior product manager roles in B2B data platform or analytics infrastructure, remote-first, Series B or C, job listing.
Director of product roles in performance marketing or measurement or attribution SaaS, remote US, job listing.
Staff or senior product manager roles in AI-powered martech or revenue intelligence SaaS, remote, job listing.
Senior product manager roles in two-sided marketplace or commerce platform, remote US, Series B or C, job listing.
```

After getting results, **filter client-side**: keep only URLs from the allowed ATS domains (see Step 3). Discard all aggregator URLs before proceeding.

Adjust angles based on what the user asks for (e.g. if they specify a domain, focus searches there).

**Domain priorities** (from Meg's rubric):

- Web3/crypto infra — highest fit
- Payments, stablecoins, DeFi, on-chain data — strong fit
- AI agents in financial contexts — strong fit
- SaaS with complex mechanisms or marketplaces — strong fit
- Martech or adtech with AI agents or features - strong fit
- Healthcare, gaming, gambling — always excluded

---

## Step 3: Source Only From Direct ATS Pages

**Only use results from these domains:**

- `jobs.lever.co`
- `job-boards.greenhouse.io`
- `boards.greenhouse.io`
- `jobs.ashbyhq.com`
- `jobs.eu.lever.co`

**Never surface results from aggregators** — they keep pages alive after postings expire:

- ❌ builtin.com
- ❌ cryptocurrencyjobs.co
- ❌ remote3.co
- ❌ web3.career
- ❌ cryptojobslist.com
- ❌ wellfound.com
- ❌ ziprecruiter.com
- ❌ indeed.com
- ❌ glassdoor.com
- ❌ Any other job board aggregator

If a search only returns aggregators, try fetching the company's careers page directly (e.g. `jobs.lever.co/companyname` or `job-boards.greenhouse.io/companyname`).

---

## Step 4: Verify Each Link is Live

For every candidate URL, run a targeted `mcp__exa__web_search_exa` query using the exact URL as the search query, with `livecrawl: "preferred"`. Use the returned content to verify liveness.

**Greenhouse** (`job-boards.greenhouse.io` or `boards.greenhouse.io`):

- ✅ Live: result content includes the job title, company description, and an Apply button
- ❌ Dead: content redirects to a "no current openings" or "job board" page, or returns 404

**Lever** (`jobs.lever.co`):

- Lever may return 403 on direct fetch — this is expected and does NOT mean the role is dead
- Instead, check whether the Exa result snippet contains full job description text
- ✅ Live: snippet contains full description text (responsibilities, requirements, etc.)
- ❌ Dead: snippet is minimal (just company boilerplate) or says "this position is no longer available"

**Ashby** (`jobs.ashbyhq.com`):

- Ashby pages often require JavaScript — content may be minimal
- Verify by checking the Exa snippet for full job content

Discard any URL that fails verification. Do not include it even with a caveat.

---

## Step 5: Score and Rank

Apply the rubric scoring (sections 1–5) to each verified live role. Max score is 10.

Score each dimension honestly. Note any flags (e.g. salary unconfirmed, hybrid rather than remote).

---

## Step 6: Output Format

For each role that passes filters and is verified live, output:

```
**[Job Title] @ [Company]**
🔗 [Direct ATS URL — verified live]
**Why this is a fit:** [2–4 sentences scoring against the rubric: role level, lifestyle fit, company stage, domain, culture signals. Call out any flags.]
**Fallback search:** `[search string the user can paste into Google if the link dies]`
```

Aim for 3–5 results per run. If fewer than 3 pass all filters and verification, say so and suggest expanding the search scope.

---

## Notes

- Re-read the rubric doc at the start of every run — Meg updates it periodically
- The hard filters in section 6 of the rubric are the source of truth; the filter list in Step 1 above is a convenience summary
- dLocal roles almost always fail the geography filter (require Argentina/Brazil/Spain/Uruguay) — verify before including
- Salary is rarely listed explicitly; use the role level + company size to estimate, and flag when uncertain
- When in doubt about a hard filter, exclude the role

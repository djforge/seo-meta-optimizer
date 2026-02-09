---
name: optimize-meta-tags
description: Optimize title tags and meta descriptions for SEO from CSV data. Use when the user wants to fix title lengths, meta description lengths, grammar issues, or duplicates across their website pages.
user-invocable: true
argument-hint: "[path to CSV file(s)]"
---

# SEO Meta Tag Optimizer

You are an SEO meta tag optimization expert. Your job is to take the user's existing page data (titles, meta descriptions, URLs) and produce optimized versions that meet character limits, are grammatically correct, and are unique across the site.

## Input

The user will provide one or more CSV files. Ask for the following if not provided:

1. **Page data** — CSV with columns like: URL, Title, Title Length, Description, Description Length (or similar)
2. **Audit data** (nice-to-have) — Ahrefs, Screaming Frog, or similar exports flagging pages with issues and providing organic traffic data
3. **Brand name** — The brand suffix to append to titles (e.g., " | Acme Corp")
4. **Brand context** — A one-line description of what the company does (used for generating meta descriptions)
5. **Website URL** (optional) — The brand's homepage URL. If provided, fetch it to understand brand voice, products, positioning, and target audience for more specific and on-brand meta descriptions

## Target Constraints

| Element | Min | Max | Notes |
|---------|-----|-----|-------|
| Title tag | — | 60 chars | Primary keyword near front |
| Meta description | 120 chars | 160 chars | Compelling, action-oriented |

## Process

Write a Python script that:

### 1. Parse all input data sources

- Auto-detect encoding (UTF-8 vs UTF-16) and delimiter (comma vs tab)
- Decode HTML entities with `html.unescape()`
- Merge data from multiple files by URL
- Identify page types from URL patterns (e.g., `/blog/`, `/docs/`, `/learn/`, etc.)
- Track which issues each page has (title_long, meta_short, meta_long, etc.)

### 2. Optimize titles using a strategy chain

Try each strategy in order until one fits within the character limit:

1. **Check manual overrides first** — always apply URL-specific overrides before any other logic
2. **Full content + brand suffix** — if it fits, use it
3. **Apply shortenings + brand** — remove filler words ("Understanding", "Introducing", "A Guide to", etc.)
4. **Smart truncate + brand** — grammar-aware truncation at clean break points
5. **Full content WITHOUT brand** — for titles 48-60 chars that are fine standalone
6. **Shortened WITHOUT brand**
7. **Unpack parentheticals** — turn `(content)` into just `content`
8. **Truncate WITHOUT brand**
9. **Remove parentheticals entirely + brand**
10. **Aggressive truncation** — cut at first colon/dash + brand

### 3. Optimize meta descriptions

- **Too long (>160):** Truncate at sentence boundary, then phrase boundary, then comma, then word boundary
- **Too short (<120):** Expand with page-type-specific additions (graduated from short to long)
- **Empty:** Generate from title + page context
- **Template pages** (tag pages, keyword indexes, legal pages): Generate from URL path context

### 4. Grammar-aware truncation (CRITICAL)

Never blindly truncate. Every truncation point must pass grammar validation.

**Title grammar checks (`is_title_grammar_ok`):**
- No trailing commas (truncated lists)
- No unclosed parentheses or brackets
- No unclosed quotes (excluding contractions like n't, 's, 're)
- Must not end with a preposition, conjunction, or article: `with, for, on, in, and, or, to, at, by, from, of, the, a, an, is, are, that, how, using, into, about, than, it, but`
- Must not end with a pronoun or clause starter: `you, they, we, who, which, what, where, when, why, will, can, could, should, would, while, before, after, until, through, between, other, because, since, without, within, during, across, over`
- Must not end with a dangling adjective (one that needs a following noun): `large, recent, various, multiple, specific, particular, distributed, compressed, advanced` etc.
- No incomplete clauses: patterns like "how [Noun]", "what [Noun]", "why [Noun]" at the end
- No encoding artifacts: `&` without surrounding spaces (except legitimate abbreviations like Q&A)
- No empty pipe patterns (`|` or `| ` at end)
- No truncated code identifiers (trailing underscores)

**Meta grammar checks (`is_meta_grammar_ok`):**
- No trailing commas
- No unclosed parentheses
- Must not end with a preposition/article
- No `?.` concatenation (question mark followed by period)
- No encoding artifacts

**Smart truncate strategy for titles:**
1. Cut at colon, semicolon, em-dash, period (if >15 chars before)
2. Cut at word boundary, checking grammar at each position (try progressively shorter)
3. Unpack parenthetical content
4. Remove parenthetical entirely
5. Fallback

**Smart truncate strategy for metas:**
1. Cut at sentence boundary (`. `) if result >=120 chars
2. Cut at phrase boundary (`; `, ` — `, ` – `)
3. Cut at comma
4. Cut at word boundary with grammar check
5. Fallback: truncate and add period

### 5. Detect and fix duplicates

After generating all optimized tags, check for:

- **Duplicate titles** — group by exact match, fix by adding context differentiators
- **Duplicate metas** — same approach
- **URL case variants** — e.g., `/keywords/aws` vs `/keywords/AWS` resolving to same title after normalization

Common duplicate sources:
- Keyword/tag index pages sharing a generic site title
- API doc variants (old/new versions, different categories)
- Legal pages sharing generic site meta
- Blog vs learn pages with same content
- Pages with query parameter variants (`?ref=`)

Fix strategies:
- Extract unique context from URL path segments
- Add differentiating suffixes (Legacy, API, Guide, etc.)
- Generate page-specific descriptions from URL structure

### 6. Tech term capitalization

Maintain a mapping for proper casing of technology terms. Common patterns:
- All-caps acronyms: SQL, API, AWS, CLI, IoT, MCP, SSO, VPC, GCP
- Product names: PostgreSQL, TimescaleDB, Docker, Kubernetes, Terraform, Grafana
- Lowercase by convention: pgvector, psql, pg_dump, npm
- Mixed case: macOS, PostGIS, InfluxDB, GitHub, JavaScript

When extracting keywords from URLs, always apply this mapping before using in titles/metas.

## Output

Generate a CSV file with these columns:

```
URL, Page_Type, Organic_Traffic, Issues, Current_Title, Current_Title_Length, Optimized_Title, Optimized_Title_Length, Title_Changed, Current_Meta, Current_Meta_Length, Optimized_Meta, Optimized_Meta_Length, Meta_Changed
```

Sort by organic traffic descending. Mark `Title_Changed` and `Meta_Changed` as YES/NO.

Also offer to split into separate CSVs by page type (website, blog, learn, docs, etc.) in a `by_section/` subfolder.

## Validation

After generating the output, run a validation pass and print:

- Total pages optimized
- Titles changed / Metas changed
- Title length violations (>60 chars)
- Meta length violations (<120 or >160 chars)
- Title grammar issues (using all checks above)
- Meta grammar issues
- Duplicate titles (groups + URLs)
- Duplicate metas (groups + URLs)

**Target: 0 issues across all checks.**

If any issues remain, fix them iteratively until all checks pass.

## Key Principles

1. **Overrides before algorithms** — always check URL-specific overrides before any automated logic, and before "already in range" early returns
2. **Grammar over character count** — a grammatically broken title at 59 chars is worse than dropping the brand suffix
3. **Brand suffix is optional** — prefer dropping " | Brand" over creating an incomplete sentence
4. **Uniqueness matters** — every page should have a unique title and meta description
5. **Context from URLs** — when pages have no meta or generic content, extract meaningful context from URL path segments
6. **Iterative quality** — run validation, fix issues, repeat until 0 issues remain

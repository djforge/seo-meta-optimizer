# seo-meta-optimizer

A Claude Code plugin that optimizes title tags and meta descriptions for SEO at scale.

## What it does

Give it your site's meta tag data (CSV exports from Ahrefs, Screaming Frog, or your CMS) and it will:

- **Fix title tags** — shorten to ≤60 characters using grammar-aware truncation
- **Fix meta descriptions** — expand short ones and trim long ones to the 120-160 character sweet spot
- **Detect grammar issues** — trailing commas, dangling prepositions, unclosed parentheses, incomplete clauses
- **Eliminate duplicates** — find and differentiate pages sharing identical titles or metas
- **Proper-case tech terms** — PostgreSQL, AWS, Kubernetes, etc.
- **Generate from context** — create titles/metas for pages that have none, using URL path analysis

## Install

```bash
claude /plugin install jordanchavis/seo-meta-optimizer
```

Or test locally:

```bash
claude --plugin-dir /path/to/seo-meta-optimizer
```

## Usage

```
/optimize-meta-tags path/to/your-meta-tags.csv
```

The skill will ask for:
1. Your **brand name** (e.g., "Acme Corp") for the title suffix
2. A **one-line brand description** for generating meta descriptions
3. Any **audit CSVs** (optional — Ahrefs, Screaming Frog, etc.)

## Output

A CSV file with columns:

| Column | Description |
|--------|-------------|
| URL | Page URL |
| Page_Type | website, blog, docs, learn, etc. |
| Organic_Traffic | From audit data (0 if unavailable) |
| Current_Title | Original title tag |
| Optimized_Title | New title tag |
| Title_Changed | YES or NO |
| Current_Meta | Original meta description |
| Optimized_Meta | New meta description |
| Meta_Changed | YES or NO |

Plus optional split by section (website, blog, docs, learn) in a `by_section/` subfolder.

## Validation

Every run ends with a validation report targeting **0 issues** across:

- Title length violations (>60 chars)
- Meta length violations (<120 or >160 chars)
- Grammar issues (30+ checks)
- Duplicate titles
- Duplicate meta descriptions

## How it works

The optimizer uses a **strategy chain** for titles — trying progressively more aggressive approaches until one fits:

1. Full title + brand suffix
2. Apply shortenings (remove "Understanding", "Introducing", etc.) + brand
3. Grammar-aware truncation + brand
4. Drop brand suffix entirely
5. Unpack/remove parentheticals
6. Aggressive truncation at first colon or dash

Every truncation point is validated against grammar rules — it will never create a title ending with "and", "for", "the", a trailing comma, or an unclosed parenthesis.

## License

MIT

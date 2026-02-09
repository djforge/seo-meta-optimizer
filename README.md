# seo-meta-optimizer

A Claude Code skill that optimizes title tags and meta descriptions for SEO at scale.

## What it does

Give it a website URL or CSV export and it will:

- **Crawl your site** — automatically extract all current titles and meta descriptions via sitemap or link crawling
- **Fix title tags** — shorten to ≤60 characters using grammar-aware truncation
- **Fix meta descriptions** — expand short ones and trim long ones to the 120-160 character sweet spot
- **Detect grammar issues** — trailing commas, dangling prepositions, unclosed parentheses, incomplete clauses
- **Eliminate duplicates** — find and differentiate pages sharing identical titles or metas
- **Proper-case tech terms** — PostgreSQL, AWS, Kubernetes, etc.
- **Generate from context** — create titles/metas for pages that have none, using URL path analysis

## Install

Clone the repo and copy the skill files into your Claude Code commands directory:

**Global install** (available in all projects):

```bash
git clone https://github.com/djforge/seo-meta-optimizer.git
mkdir -p ~/.claude/commands
cp seo-meta-optimizer/skills/optimize-meta-tags/SKILL.md ~/.claude/commands/optimize-meta-tags.md
cat seo-meta-optimizer/skills/optimize-meta-tags/reference.md >> ~/.claude/commands/optimize-meta-tags.md
```

**Per-project install** (available only in a specific project):

```bash
git clone https://github.com/djforge/seo-meta-optimizer.git
mkdir -p .claude/commands
cp seo-meta-optimizer/skills/optimize-meta-tags/SKILL.md .claude/commands/optimize-meta-tags.md
cat seo-meta-optimizer/skills/optimize-meta-tags/reference.md >> .claude/commands/optimize-meta-tags.md
```

After installing, restart Claude Code. The `/optimize-meta-tags` command will be available.

## Usage

From a website URL (crawls the site automatically):

```
/optimize-meta-tags https://example.com
```

Or from a CSV export:

```
/optimize-meta-tags path/to/your-meta-tags.csv
```

The skill will ask for:
1. Your **brand name** (e.g., "Acme Corp") for the title suffix
2. A **one-line brand description** for generating meta descriptions
3. Your **website URL** (if not already provided — fetched for brand voice and positioning context)
4. Any **audit CSVs** (nice-to-have — Ahrefs, Screaming Frog, etc. for organic traffic data and pre-flagged issues)

## Output

A CSV file with columns:

| Column           | Description                        |
| ---------------- | ---------------------------------- |
| URL              | Page URL                           |
| Page\_Type       | website, blog, docs, learn, etc.   |
| Organic\_Traffic | From audit data (0 if unavailable) |
| Current\_Title   | Original title tag                 |
| Optimized\_Title | New title tag                      |
| Title\_Changed   | YES or NO                          |
| Current\_Meta    | Original meta description          |
| Optimized\_Meta  | New meta description               |
| Meta\_Changed    | YES or NO                          |

Plus optional split by section (website, blog, docs, learn) in a `by_section/` subfolder.

## Validation

Every run ends with a validation report targeting **0 issues** across:

- Title length violations (\>60 chars)
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

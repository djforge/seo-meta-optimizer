# Reference: Word Lists & Patterns

## Bad Ending Words for Titles

Words that should never be the last word in a title (before brand suffix). These leave sentences grammatically incomplete.

### Prepositions & Conjunctions
```
with, for, on, in, and, or, to, at, by, from, of, into, about, than, but
```

### Articles & Determiners
```
the, a, an, its, your, our, their, my, this, these, those
```

### Verbs That Need Objects
```
is, are, using, open, prevents, enables, provides, offers, builds
```

### Pronouns & Clause Starters
```
you, they, we, it, who, which, what, where, when, why, how, that
```

### Modals & Auxiliaries
```
can, could, should, would, will, do, does, did, have, has, had
```

### Subordinating Conjunctions
```
while, before, after, until, because, since, whether, although, though
```

### Prepositions (extended)
```
through, between, without, within, during, across, over, under, among, along, toward, towards, against
```

### Other
```
other, another, each, every, all, both, either, neither, such, no, any, some, more, most, also, even, just, only, then, very, many, much, few, several
```

## Incomplete Phrase Endings

Adjectives/modifiers that need a following noun when they appear as the last word:

```
time-series, real-time, open-source, semi-structured, high-performance, cloud-native
sample, distributed, compressed, advanced, large, recent, various, multiple, specific, particular
```

## Title Shortening Substitutions

Try these replacements to shorten titles (in order):

| Original | Replacement | Savings |
|----------|------------|---------|
| "Understanding " | "" | ~14 chars |
| "Introducing " | "" | ~13 chars |
| "A Guide to " | "" | ~11 chars |
| "How to " | "" | ~7 chars |
| " – " | ": " | ~1 char |
| " — " | ": " | ~1 char |
| " - " | ": " | ~1 char |

After shortening, capitalize the first letter if it became lowercase.

## Contraction Patterns (Not Unclosed Quotes)

When checking for unclosed single quotes, exclude these contractions:
```
n't, 's, 're, 'll, 've, 'd, 'm
```

## Meta Description Expansion Templates

### Blog posts
```
" Read the full article on the {Brand} blog."
" A deep dive from the {Brand} engineering team."
" Explore the details in this {Brand} blog post."
" Learn more in this technical deep dive from {Brand}."
```

### Learn/Tutorial pages
```
" A practical guide for developers."
" Explore examples and best practices."
" Step-by-step guide with code examples."
```

### Docs pages (graduated by length needed)
Short additions (~40-50 chars):
```
" See syntax and examples in the docs."
" View syntax, parameters, and examples."
```

Medium additions (~55-70 chars):
```
" Explore examples and best practices in the docs."
" See syntax, parameters, and code examples in the documentation."
```

Long additions (~80-95 chars):
```
" Includes syntax, parameters, return values, and code examples."
" Complete reference with syntax, parameters, and practical examples for developers."
```

### Generic/Website pages
```
" Learn more about {Brand}'s platform."
" Discover how {Brand} works at scale."
```

## Common Data Source Formats

### Ahrefs audit exports
- Encoding: UTF-16
- Delimiter: Tab
- Headers: Quoted with `"`
- Key columns: `URL`, `Is indexable page`, `Organic traffic`, `Title`, `Title length`, `Meta description`, `Meta description length`

### Screaming Frog exports
- Encoding: UTF-8
- Delimiter: Comma
- Key columns: `Address`, `Title 1`, `Title 1 Length`, `Meta Description 1`, `Meta Description 1 Length`

### Generic meta tag CSVs
- Encoding: UTF-8
- Delimiter: Comma
- May contain HTML entities (use `html.unescape()`)

## URL Pattern → Page Type Mapping

```python
def get_page_type(url):
    if '/blog/tag/' in url: return 'blog_tag'
    if '/blog/author/' in url: return 'blog_author'
    if '/blog/' in url or url.endswith('/blog'): return 'blog'
    if '/learn/' in url or url.endswith('/learn'): return 'learn'
    if '/docs/keywords/' in url: return 'docs_keyword'
    if '/docs/api/' in url: return 'docs_api'
    if '/docs/' in url or url.endswith('/docs'): return 'docs'
    if '/legal/' in url: return 'legal'
    return 'website'
```

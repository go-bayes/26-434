# PSYC 434: Conducting Research Across Cultures (2026)

Course website for PSYC 434, Trimester 1, 2026, Victoria University of Wellington.

Built with [mdBook](https://rust-lang.github.io/mdBook/) using [mdbook-katex](https://github.com/lzanini/mdbook-katex) for maths and [mdbook-admonish](https://github.com/tommilligan/mdbook-admonish) for callout blocks.

## Building locally

```
cargo install mdbook mdbook-katex mdbook-admonish
mdbook serve
```

Renders at <http://localhost:3000>.

## Structure

```
src/
  index.md          # homepage
  schedule.md       # weekly schedule
  assessments.md    # assessment descriptions
  extensions.md     # extensions and penalties
  week-01.md …      # weekly lecture content
  figures/          # SVG diagrams and static assets
```

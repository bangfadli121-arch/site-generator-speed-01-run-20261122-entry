# site-generator-speed-01-run-20261122

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/bangfadli121-arch/site-generator-speed-01-run-20261122-entry/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/bangfadli121-arch/site-generator-speed-01-run-20261122-entry/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for makefun-site 1.0.0, make.fun's own static site generator. It
returns byte-identical output for every case (each page's `index.html`, the index
and tag pages, `build-report.txt`, every static file) while running about 2.4x
faster than the house baseline on the practice set (node22, CPU time).

How it gets faster, without changing any output:

- **Fast HTML escaping** — a single-regex scan that returns the input untouched
  when nothing needs escaping, instead of a regex replace per character.
- **Fused Markdown inline scanner** — plain-text runs are sliced in bulk with
  `indexOf` and escaped once, instead of one `escapeHtml` call per character.
- **Fused lex + render** — blocks are rendered in a single pass with first-character
  dispatch (no per-line regex battery, no token objects).
- **Template codegen** — each parsed template compiles to a JS function
  (`new Function`) that pushes into a string array, instead of walking a node
  tree through a scope-chain interpreter.
- **Split-free front matter** — the body is never split and re-joined; only the
  front-matter region is split.
- **Slug memoisation** — `slugify` results are cached per case (titles and tag
  names repeat across pages).
- **Allocation-free word count** — `reading_minutes` counts words with a
  lookup-table scan instead of `String.match(/\S+/g)`.

## How to run

```sh
node main.ts
```

`main.ts` reads every case from `/input/cases.json` (or `$MAKEFUN_INPUT_DIR`) and
writes `/output/answers.json` (or `$MAKEFUN_OUTPUT_DIR`), one answer per case.
`solve.js` is the entry: `run(input)` returns makefun-site 1.0.0's answer for
one case's input. Node 22, no dependencies, no install step.

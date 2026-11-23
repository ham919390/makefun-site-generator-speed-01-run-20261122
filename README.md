# Fast site-generator — a faster drop-in for makefun-site 1.0.0

[![site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/ham919390/makefun-site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/ham919390/makefun-site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

A faster drop-in for make.fun's own makefun-site 1.0.0 — an entry for the [site-generator-speed-01-run-20261122](https://make.fun/bounty/site-generator-speed-01-run-20261122) bounty on [make.fun](https://make.fun/r/ej1tk366). It answers exactly as makefun-site does on every case, byte for byte, and builds the practice set about 2.9× faster than the reference on solving alone (about 1.5× end to end in a fresh process).

`solve.js` is a dependency-free ES module whose `run(input)` returns makefun-site's answer for a site `{ site, templates, files }`: the full output tree (each page's `index.html`, the index and tag pages, `build-report.txt`, each static file). `main.ts` is the house driver: it reads every case from `/input/cases.json`, answers each with `run`, and writes `/output/answers.json`.

## How it is faster

The reference parses and renders everything through small per-character and per-token steps. This one keeps the exact same semantics but removes the repeated work:

- front matter is parsed once into `{ data, lines, bodyStart }` and the body is rendered from the lines directly, never re-split or re-joined;
- Markdown lexing and rendering happen in a single pass with no token objects, dispatching on the first character of each line;
- inline formatting escapes plain runs in one regex pass instead of escaping character by character;
- templates are parsed once and compiled to plain JavaScript (`new Function`), with a scope object and loop object reused across iterations, and a scratch output array reused across renders;
- word counts and line trimming are manual scans instead of regexes, and each source's slug and card are computed once and reused;
- nothing is cached from one case to the next — every build starts clean, as the rules require.

## Check it

The kit's practice set (`practice/input/cases.json`) answers identically to the reference on all three cases, and the entry was additionally checked against 120 freshly drawn sites from the kit's own generator and 99 hand-written edge cases (front-matter errors, slug collisions, Unicode titles, unclosed fences, deep template includes, loop-variable shadowing, broken links, paging) — all identical to makefun-site 1.0.0.

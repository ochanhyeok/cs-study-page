# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A single-page static landing site for a 12-week backend CS study group (Korean, 7 members). The whole app lives in `index.html` — there is no build step, no `package.json`, no bundler. React 18 UMD, Babel standalone, and Tailwind are loaded from CDNs and JSX is transpiled in the browser at runtime.

`templates/CLAUDE.md` is **not** a Claude Code instruction file — it is study-group content (rules for *learners* using AI assistants while studying) that is also rendered inside the page via the "Claude.md 보기" modal. Do not treat it as guidance for Claude Code itself.

## Run locally

```bash
python3 -m http.server 8000 -d /Users/ochanhyeog/Desktop/cs-study-page
# → open http://localhost:8000
```

This matches `.claude/launch.json`. There are no tests, no linter, no build command.

## Architecture (one-file React)

`index.html` contains a single root component `App` (line 57). Everything is inlined:

- **Icon set**: `I` object (~line 43) — inline SVG components (`I.Flame`, `I.Check`, etc.). When you need a new icon, add a new key to `I` rather than importing one.
- **Data lives in component scope**, not external JSON:
  - `week1Members` (~line 62) — Week 1 per-member scenario matrix (s1–s4: race / visibility / measurement / Spring). Used by the scenario modal.
  - `phases` (~line 114) — 4 study phases × 3 weeks each. Each topic has `{ w, t, goal, cs, q }`. Drives the curriculum section.
- **Page sections** (top-down in JSX, ~line 193 onward): sticky header → Hero → `#cycle` → `#curriculum` → `#weekly` → `#output` → footer.
- **Three modals**, each toggled by its own `useState` flag at the top of `App`:
  - `scenarioOpen` — week-1 per-member race/visibility scenario detail.
  - `topicFlowOpen` — "한 주제, 4단계" learning flow.
  - `claudeMdOpen` — renders the contents of `templates/CLAUDE.md` for learners. **The modal text is hardcoded inline in `index.html`**; if you change `templates/CLAUDE.md`, also update the modal body (and vice versa) to keep them in sync.
- **Tailwind config** is inlined in a `<script>` before the CDN script (~line 12). Custom tokens: `ink`, `ink-soft`, `ink-mute`, `line`, `bg-alt`, `brand`, `brand-faint`, `brand-hover`. Prefer these over raw hex.
- **Korean UI copy** with Pretendard Variable font (preconnected from jsdelivr). Keep `lang="ko"` and tabular-nums (`.num`) for any numeric stats.

## Editing conventions specific to this file

- The `<script type="text/babel" data-presets="env,react">` block is parsed by Babel standalone in the browser — no TypeScript, no JSX imports. Stick to plain JSX + `React.useState` / `React.useEffect` accessed off the global `React`.
- Anchor links (`#cycle`, `#curriculum`, `#weekly`, `#output`) are referenced from the nav at line ~202. If you rename a section `id`, update the nav.
- Member names and per-member scenarios are real personal references — do not invent or replace names; ask before restructuring `week1Members`.

## Git

Default branch has short Korean conventional-style commit messages (`feat:`, `fix:`, `update:`, `init:`). Match that style.

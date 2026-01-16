# Supermarket Sweep Question Rounds

A SvelteKit app for running Supermarket Sweep-style question rounds with separate host and contestant views that stay in sync in real time.

## What it does (planned)
- Host control panel to reveal clues, advance questions, and mark correct contestants
- Contestant display with large, readable clues/answers and timers
- Multiple question types: hangman-style reveal, progressive clues, fill-in-the-blank, guess-the-price
- Competitive scoring with time bonuses for correct answers
- Real-time updates between host and contestants
- Questions authored ahead of time and stored in PostgreSQL

## Tech stack
- SvelteKit 2 with Svelte 5 runes
- TypeScript (strict)
- Vite 7
- WebSocket for real-time sync
- PostgreSQL for persistence (planned)

## Getting started (Deno)
1) Run the dev server
```sh
deno task dev
```
2) Type check
```sh
deno task check
```
3) Build
```sh
deno task build
```

## Project layout
- src/routes/ — pages (host and contestant screens will live here)
- src/lib/ — shared components/utilities
- static/ — public assets

See the full requirements in [REQUIREMENTS.md](REQUIREMENTS.md).

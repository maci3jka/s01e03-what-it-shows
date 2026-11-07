# s01e03-what-it-shows

MEGA.dev S01E03. The support desk merged 412 date reports into ticket #4471 and marked it known. Here they are split by cause.

## Tickets

| issue | problem | reports |
|---|---|---|
| [#1](../../issues/1) | parser: dates read month first, day-first tenants get day and month swapped | 374 |
| [#2](../../issues/2) | hint: booking-v3 form says `MM/DD/YYYY` to day-first tenants | 33 |
| [#3](../../issues/3) | one-day move: dates shift back one day (time zone) | 5 |

The counts are over unique report ids. The export holds 427 entries, and 15 of them are exact copies.

## Page

- Source: [`kb/src/routes/kb/incidents/4471-what-it-shows/+page.svx`](kb/src/routes/kb/incidents/4471-what-it-shows/+page.svx). The counts come from [`kb/src/lib/kb/ProblemSplit.svelte`](kb/src/lib/kb/ProblemSplit.svelte), not typed text.
- As the desk sees it: [`rendered/4471-what-it-shows.html`](rendered/4471-what-it-shows.html) (self-contained) and [`rendered/4471-what-it-shows.png`](rendered/4471-what-it-shows.png).

## Build

`kb/` is the desk's export (dated 2026-10-04) plus the new page and component. The export ships `src/` only, so `package.json`, `svelte.config.js`, `vite.config.js`, `src/app.html` and `src/routes/+layout.js` are added: the smallest SvelteKit + mdsvex config that renders it.

```
cd kb && npm install && npx vite build   # pages land in kb/build/kb/
```

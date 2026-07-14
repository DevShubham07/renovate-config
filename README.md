# renovate-config

Org-wide Renovate preset (design doc §4.2/§4.5, D-#/§5 "Deps" row, §9). Sites don't hand-roll
Renovate rules; they extend this preset.

## How a site uses this

Every `site-<slug>` (via `site-template`, §4.4) ships a `renovate.json` at its repo root:

```json
{
  "extends": ["github>ORG/renovate-config"]
}
```

That's it — Renovate (installed once as the org GitHub App, §4.1/P0) picks up `default.json`
from this repo for every site, so a fleet-wide policy change (schedule, grouping, automerge
rules) is a single edit here rather than N edits across sites.

## What the preset does (`default.json`)

- Extends `config:recommended` (Renovate's maintained recommended baseline).
- Runs on the `Asia/Kolkata` timezone, weekly.
- Groups the Astro-ecosystem (`astro`, `@astrojs/*`, `astro-favicons`, `tailwindcss`,
  `@tailwindcss/*`) minor/patch updates into a single PR per site.
- Automerges `devDependencies` patch-level PRs once the site's CI (the reusable `astro-ci.yml`
  gate, §4.5) is green — production dependency and minor/major updates always wait for
  manual review.
- Uses semantic-commit-formatted PR titles/commits.
- Throttles to 2 PRs/hour and 5 concurrent PRs per repo, respecting the fleet's 20-concurrent-Actions-job
  ceiling (§4.5) when Renovate opens PRs across many site repos at once.

---
name: Edit a Ploy site locally with a coding agent and sync it back
description: Initialize Code Sync, install Ploy's own agent skills, read the site's design system, push reviewed changes to GitHub main, import them back into Ploy, and publish.
api: Ploy CLI (no OpenAPI published)
generated: '2026-08-12'
method: generated
source: https://docs.ploy.ai/cli/local-development
operations:
  - ploy site use
  - ploy site code-sync init
  - ploy skills init
  - ploy skills sync
  - ploy design-system list-components
  - ploy design-system list-pages
  - ploy design-system get-page-components
  - ploy design-system get-theme-colors
  - ploy design-system query
  - ploy design-system plan-page-build
  - ploy site code-sync sync
  - ploy site publish
---

# Work on a Ploy site from a local or remote coding agent

Code Sync gives a Ploy site a GitHub repository (Astro + Tailwind, hosted under
the `github.com/ployspace` org and managed by Ploy). Ploy's own agent commits to
the same repo, so the human, the coding agent and Ploy's agent all work on one
branch: `main`. Code Sync requires a paid plan.

## 1. Select the site and initialize Code Sync

```sh
ploy workspace use
ploy site use
ploy site code-sync init
```

`code-sync init` prints connection status, setup links, and HTTPS and SSH clone
commands. Clone it and keep all work on `main`.

## 2. Install Ploy's published agent skills

From the repository root:

```sh
ploy skills init
ploy skills sync
ploy skills sync --check
```

- `skills init` installs Ploy's published catalog without overwriting existing
  files, at `.agents/skills/<id>/SKILL.md` and `.claude/skills/<id>/SKILL.md`.
- `skills sync` replaces edited Ploy-managed files and removes managed skills no
  longer in the catalog.
- `skills sync --check` changes nothing and **exits 1 when managed content has
  drifted** — use it as a CI gate.

Ownership is recorded in `.ploy/skills.json`; `.ploy/.skills.lock` serializes
runs. Symlinks and unmanaged same-ID collisions fail closed. Skills commands do
not support `--dry-run`. The checkout must be a Ploy site with a regular,
non-symlink `package.json` whose `name` is exactly `ploy-web`.

## 3. Read the design system before writing any markup

These commands read the checkout and require no authentication or selected
workspace. The checkout must contain `src/pages`, and the local inspection
runtime needs `bash` plus either `node` or `bun` on PATH.

```sh
ploy design-system list-components
ploy design-system list-components --path "src/components/sections/**/*.tsx" --limit 50
ploy design-system list-pages
ploy design-system get-page-components --page-path /pricing
ploy design-system get-theme-colors
ploy design-system query --query 'pricing card'
ploy design-system plan-page-build \
  --page-kind landing \
  --target-audience "growth teams" \
  --requested-sections "hero, proof, pricing, FAQ" \
  --requested-path /growth-teams
```

List commands support `--limit 1..200`, `--cursor`, `--path` and
`--include-deprecated`. `--requested-path` must be a site route beginning with
`/`. `--cwd` changes the checkout.

**Reuse registered components and preserve the active theme tokens.** Inventing
new one-off components is the failure mode this whole surface exists to prevent.

## 4. Sync back and publish

```sh
ploy site code-sync sync
ploy site publish --wait
```

`code-sync sync` imports the latest GitHub `main` commit into the selected site.
On a reported conflict, resolve it in the Ploy editor and retry. Review the
synced site in Ploy before publishing.

## Running this from a remote agent or CI

```sh
export PLOY_API_TOKEN=...        # workspace-scoped, from Settings → Developer
export PLOY_NO_UPDATE_CHECK=1
ploy whoami
```

- Use a separate token per agent/CI environment so rotation and revocation are
  predictable.
- Never paste the token into an agent prompt, command argument, repo file or
  build log; mask it and never echo the environment.
- Allowlist outbound `https://ploy.ai` — restricted remote environments block
  Ploy CLI auth until it is allowed.
- `ploy site publish`, `ploy variable set` and `ploy site code-sync sync` use the
  **saved** site selection; an ephemeral runner has none, and the token's
  workspace pin does not select a site. Establish and verify selection first.
- Stay under 60 requests per minute per token.

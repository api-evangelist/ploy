---
name: Publish a Ploy site from the CLI
description: Select a workspace and site, check readiness, publish to production, and confirm the deploy reached ready — non-interactively where possible.
api: Ploy CLI (no OpenAPI published)
generated: '2026-08-12'
method: generated
source: https://docs.ploy.ai/cli/reference
operations:
  - ploy whoami
  - ploy workspace use
  - ploy site use
  - ploy site status
  - ploy site publish
  - ploy site publish-status
---

# Publish a Ploy site

Ploy publishes no REST API reference and no OpenAPI. The CLI is the documented
programmatic surface, so every step below is a real `ploy` command taken from the
published command index at https://docs.ploy.ai/cli/reference. Do not invent
flags — run `ploy help <command>` to confirm syntax against the installed
version.

## Before you start

- Install: `curl -fsSL https://ploy.ai/install.sh | sh`
- Authenticate. Interactive: `ploy login`. Headless/CI: set `PLOY_API_TOKEN`
  (workspace-scoped, prefix `sk_ploy_pat_`, created in workspace Settings →
  Developer). The token is read on every invocation and never written to disk;
  there is no `--token` flag.
- Set `PLOY_NO_UPDATE_CHECK=1` in automation.
- Allow outbound access to `https://ploy.ai`.

## Steps

1. Confirm the credential and its workspace pin.

   ```sh
   ploy whoami
   ```

   Exit code 3 means the credential is missing or expired — create a replacement
   token, do not retry.

2. Select context. Interactive selectors need a TTY; in CI pass explicit IDs.

   ```sh
   ploy workspace use            # or: ploy workspace use --id <workspace-id>
   ploy site use                 # or: ploy site use --id <site-id>
   ```

   A fresh CI runner has NO saved selection, and the token's workspace pin does
   not select a site. A successful `ploy whoami` is not proof a site is selected.

3. Check readiness before mutating anything.

   ```sh
   ploy site status --json
   ```

4. Publish and wait for production.

   ```sh
   ploy site publish --wait
   ```

   `--wait` exits 0 only when production reaches `ready`.

5. If `--wait` did not settle, the publish is still running — poll, never
   re-publish.

   ```sh
   ploy site publish-status <operation-id>
   ```

## Rules an agent must follow

- **No idempotency key exists.** Ploy documents none. Re-issuing `ploy site
  publish` after a timeout starts duplicate work; use the returned operation ID
  with `ploy site publish-status` instead.
- **Exit codes are the contract:** 0 success · 1 runtime/API failure · 2 usage
  error · 3 auth required/expired · 4 plan upgrade required (API 402) · 5
  `--wait` timed out, publish still running · 6 `--wait` lost API contact,
  publish still running.
- **Rate limit:** stay below 60 requests per minute per API token. A rate-limited
  command exits 1 and includes the retry delay in the message. Retry rate limits
  and transient failures; never retry authentication failures.
- **Preview mutations** with `--dry-run` where the command supports it.
- Prefer `--json` where supported and keep stdout clean for parsing.

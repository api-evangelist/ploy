# Ploy

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Ploy is an AI-powered web marketing automation platform — AI agents that build, optimize and publish
Astro/Tailwind marketing sites, run SEO and answer-engine optimization, identify visiting companies,
and execute reusable multi-step workflows called Ploybooks.

Ploy's programmable surface is **CLI-first, not REST-first**. As of the 2026-08-12 enrichment pass it
publishes:

- a standalone **`ploy` CLI** (v0.7.0, released 2026-08-11) covering workspaces, organizations, sites,
  publishing, variables/secrets, documents, Ploybooks, databases, Code Sync and design-system
  inspection — authenticated by workspace-scoped `PLOY_API_TOKEN` for headless CI and remote agents;
- one public HTTP endpoint — an authenticated **inbound webhook ingest** at
  `POST https://ploy.ai/api/v1/webhook/{endpointSlug}` that stores arbitrary JSON and triggers a
  Ploybook;
- an installable **Agent Skills catalog** (`ploy skills init`) written into a checked-out Ploy site;
- `llms.txt` on both `ploy.ai` and `docs.ploy.ai`, and a served `/.well-known/security.txt`.

It publishes **no OpenAPI, no REST API reference, no client SDKs, no MCP server, no A2A agent card and
no deprecation policy** — each probed and recorded as an honest absence in this repo.

Backed by: y-combinator, first-round-capital

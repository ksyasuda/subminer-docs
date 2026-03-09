# Plausible Tracking Design

## Goal

Keep docs analytics active for `docs.subminer.moe` while reporting into the existing Plausible site `subminer.moe` through the shared Cloudflare worker at `worker.subminer.moe`.

## Decisions

- Plausible site remains `subminer.moe`.
- Docs tracker explicitly sends `domain: 'subminer.moe'`.
- Docs tracker posts to `https://worker.subminer.moe/api/event`.
- Proxy worker continues owning first-party event forwarding to `https://plausible.sudacode.com/api/event`.
- Add regression coverage in both repos so future theme or worker changes do not silently remove tracking.

## Rationale

Plausible supports rolling subdomains into one root-domain site and filtering by hostname in the dashboard. That avoids splitting reporting while still letting docs traffic be isolated when needed.

Using the npm tracker inside VitePress keeps tracking client-side and SPA-aware without adding an inline analytics script to the docs theme. The Cloudflare worker remains the stable proxy boundary for event delivery.

## Scope

- Docs repo: make Plausible config explicit and keep tests aligned with the approved domain/endpoint pairing.
- Worker repo: harden proxy behavior with tests for script passthrough, event forwarding, cookie stripping, and `/api/capture` compatibility.

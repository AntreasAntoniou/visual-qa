---
name: visual-qa
description: Review a rendered interface with an isolated committee of orthogonal visual experts. Use for UI audits, responsive and theme checks, edge-state coverage, screenshot-based accessibility review, adversarial finding validation, and optional fix-and-recapture loops.
---

# Visual QA

Visual QA is a review protocol. The project adapter owns capture; the skill supplies the state matrix, independent expert lenses, refutation, synthesis, and optional verified fixes.

## Inputs

Infer these when safe; otherwise ask.

- **adapter**: project-specific capture and test instructions in `adapters/<project>.md`
- **tier**: `quick`, `full`, or `deep`; default `full`
- **mode**: `review` or `fix`; default `review`
- **scope**: flows and screens; default to the adapter's declared flows
- **focus**: optional lens or risk to emphasize

## Capability discovery

Before capture:

1. Inspect the adapter and project scripts for an existing screenshot command.
2. Inventory actually available browser, device, native-UI, and image-reading tools.
3. Prefer the project's deterministic capture harness. Do not assume Playwright, a browser MCP, or a particular agent runtime exists.
4. If capture or image inspection is unavailable, stop and name the missing capability. Never fabricate screenshots or visual findings.

This repository does not ship a universal browser driver. Start from [`adapters/_template.md`](adapters/_template.md) when a project has no adapter.

## Pipeline

1. **Capture** — render a deterministic matrix of flows, states, breakpoints, and themes; add a clearly marked exploratory pass.
2. **Validate** — run `python3 scripts/validate_manifest.py path/to/manifest.json --check-files`.
3. **Review** — give each selected expert only its lens, negative constraint, manifest, and images. Experts do not see one another's findings.
4. **Refute** — assign a skeptic to every critical/high/medium finding. The default verdict is “not proven” unless pixels or interaction evidence support it.
5. **Synthesize** — deduplicate, preserve dissent, and rank by severity, confidence, independent votes, and user impact.
6. **Fix** — only in `mode: fix`, with explicit authority. Give disjoint file scopes to isolated implementers and apply reviewed diffs once.
7. **Re-verify** — recapture affected states and compare before/after for improvement and regression.

## Capture contract

The matrix should cover:

- flows: the core user journeys;
- states: default, empty, long-content, loading, error/offline, disabled, and first-run;
- breakpoints: project-specific, with narrow/mobile, medium, and wide coverage;
- themes: every supported theme;
- transitions: pending, focus, hover, pressed, open/close, and success/failure where relevant.

Pin time, timezone, locale, randomness, animation policy, and fixtures where the project permits. Deterministic fixtures should be purpose-built and non-sensitive. Exploratory screenshots are useful evidence but are not goldens.

`manifest.json` is an array of:

```json
{
  "label": "checkout-error-mobile-dark",
  "path": "qa-shots/checkout-error-mobile-dark.png",
  "flow": "checkout",
  "state": "error",
  "breakpoint": 320,
  "theme": "dark"
}
```

Paths must be repository-relative and use forward slashes.

## Finding contract

Each finding must contain:

- expert slug and lens;
- manifest label and exact visible region;
- issue and governing principle;
- pixel or interaction evidence;
- severity: `critical`, `high`, `medium`, `low`, or `nit`;
- concrete proposed fix;
- confidence from 0 to 1;
- any additional capture required.

Silence beats weak findings. Aesthetic preference is not an objective defect. Preserve minority opinions when they are high-confidence and clearly labeled as taste.

## Severity

- **critical**: data loss, unintended irreversible action, unreadable core content, or sensitive information exposed in-frame
- **high**: blocks or badly degrades a core task, fails a required accessibility criterion, or breaks a required state
- **medium**: material friction, inconsistency, or ambiguous affordance
- **low/nit**: polish with limited user impact

## Guardrails

- Isolation is mandatory during review.
- Validate capture coverage before judging design.
- Real-account or sensitive screenshots are ephemeral: keep them outside version control and remove them according to project policy.
- Never commit raw captures by default. Only synthetic goldens may be committed after human ratification.
- The human owns final taste, brand direction, and golden-image approval.
- A screenshot cannot prove DOM semantics, keyboard behavior, screen-reader output, performance, or network correctness. Route those claims to appropriate tests.

Select experts from [`roster.md`](roster.md). Keep the committee as small as the risk permits.

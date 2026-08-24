# Visual QA

Visual QA turns screenshot review into an evidence-driven committee process:

```text
capture matrix → validate manifest → isolated reviews → adversarial refutation
               → prioritized synthesis → optional fix → recapture
```

The central idea is orthogonality. One broad reviewer tends to produce generic advice; several reviewers with mutually exclusive lenses expose different failure classes. A skeptic then separates visible defects from taste presented as certainty.

## What is included

- an Agent Skill operating contract in [SKILL.md](SKILL.md);
- a compact, public-safe [expert roster](roster.md);
- a generic [project adapter template](adapters/_template.md);
- a dependency-free [manifest validator](scripts/validate_manifest.py) with tests.

Visual QA intentionally does not bundle a browser driver or agent framework. It discovers and uses the project's existing capture command and the host's available image-inspection tools. If those capabilities are absent, it reports that limitation instead of pretending a review occurred.

## Quick start

1. Install the skill: `npx skills add AntreasAntoniou/visual-qa`.
2. Copy `adapters/_template.md` to an adapter owned by your project.
3. Fill in the capture command, deterministic controls, flows, states, breakpoints, themes, and test commands.
4. Run capture.
5. Validate the gallery:

```bash
python3 scripts/validate_manifest.py path/to/qa-shots/manifest.json --check-files
```

6. Invoke the skill in `review` mode with the adapter and desired tier.

## Privacy

Screenshots can contain names, messages, access tokens, account identifiers, and other personal data. Capture synthetic fixtures for repeatable audits. Keep real-account captures outside version control and follow the project's deletion and retention policy.

The validator reads local JSON and optionally verifies local image paths. It performs no network requests.

## Development

```bash
python3 -m unittest discover -s tests -v
python3 -m compileall -q scripts tests
```

The automated suite creates temporary manifests and commits no screenshots.

## License

MIT. See [LICENSE](LICENSE).

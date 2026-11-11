# AGENTS.md (skaegis)

Documentation and spec home for skaegis, a sovereign cross-OS CVE-detection and closed-loop remediation service built on unmodified upstream Wazuh. The deploy role lives in SKStacks `optional/skaegis`, not here. Read [README.md](./README.md) for the overview and [SOP.md](./SOP.md) for operations. Standard: [AGENT_INSTRUCTIONS_STANDARD](https://github.com/smilinTux/sk-standards/blob/main/standards/AGENT_INSTRUCTIONS_STANDARD.md).

## Source of truth and invariants

- This repo is docs and spec only. The SKStacks `optional/skaegis` role is the source of truth for what is actually deployed; if the two disagree, the role wins and this repo is fixed.
- `docs/design.md` is the product design. `docs/closed-loop.md` is the hand-off contract. The README and SOP link to them and never copy them.
- skaegis runs **unmodified upstream Wazuh**, pinned by `@sha256:`. Never document or imply a fork or patched image.
- The tier is **T0 - N/A** (no key material of its own). Never claim a crypto property the repo does not own.
- The agent drafts remediation, it never auto-merges. Keep that guardrail in every doc that describes the loop.
- The license is GPLv3 to match Wazuh. Do not relicense without owner sign-off.

## Commands

```bash
# install: none, docs only
# test (docs gate)
python3 <path-to-sk-standards>/scripts/docs_check.py --repo .
# dash check (must print nothing)
grep -rnP '\x{2014}|\x{2013}' --include='*.md' --exclude=LICENSE .
```

## Start here

1. `README.md`: suite framing (Defend sksec, Detect skaegis, Remediate skpatch) and the two diagrams.
2. `SOP.md`: architecture, deploy via the SKStacks role, troubleshooting, `docs-evidence` block.
3. `docs/design.md`: product design, tiers, build plan, open decisions.
4. `docs/closed-loop.md`: detect, triage, remediate, verify wiring.

## Rules

- **Writing style:** no em dashes (U+2014) and no en dashes (U+2013) anywhere: code,
  comments, docs, commits, PR bodies. Use commas, parentheses, colons or a new sentence.
  Hyphens are fine.
- **Branch and commit:** never commit to `main`. Branch, commit as soon as a fast check
  passes and before any long verification, then open a PR.
- **Attribution:** a `Co-Authored-By` trailer names the agent that actually did the work,
  or is left out.
- **Honest claims:** never write "quantum-proof", "unhackable", "complete coverage" or
  similar. skaegis detects known, published CVEs. It does not find zero-days.
- **Diagrams:** mermaid, kept in sync with the text in the same PR. A data-path change
  updates the data-flow diagram.
- **Generated files:** none. Nothing in this repo is generated.
- **Docs-evidence:** keep the `docs-evidence` block in `SOP.md` true. Update it when a
  fact it checks changes.

## Privacy

Never put any of these in code, docs, tests, fixtures, commits or the site: host names,
LAN or tailnet IPs, estate names, personal names or data, credentials, real CVE findings
from a live fleet, agent enrolment keys, OIDC client secrets, indexer passwords. Use
placeholders such as `<domain>` and `<env>`. Enforced by: review only.

## Hazards

- **Role vs repo drift:** the SOP describes the SKStacks role. Re-check the role before
  editing a port, path or service name here; do not guess from memory.
- **Unpinned images:** a doc snippet that shows a tag instead of an `@sha256:` digest
  teaches the wrong thing. The play refuses unpinned images.
- **Overstating remediation:** skaegis hands findings off. Do not write that it patches
  hosts itself.
- **Exposing agent ports:** never document a public port for agent enrolment. The tunnel
  or internal path is the only ingress.
- **Concurrent sessions:** other sessions may share this checkout. Branch first, commit
  often, and `git status` before assuming a clean tree.

## See also

- [SKStacks](https://github.com/smilinTux/SKStacks): hosts the `optional/skaegis` deploy role.
- [skpatch](https://github.com/smilinTux/skpatch): the Remediate half of the suite.
- [sksec](https://github.com/smilinTux/sksec): the Defend half and the closed-loop policy.
- [SK_REPO_DOC_STANDARD](https://github.com/smilinTux/sk-standards/blob/main/standards/SK_REPO_DOC_STANDARD.md)

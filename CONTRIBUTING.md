# Contributing to skaegis

Thanks for helping. This repo is the documentation and spec home for skaegis. The deploy role itself lives in the SKStacks repo as `optional/skaegis`; changes to Ansible, compose or defaults belong there.

By participating you agree to the [Code of Conduct](./CODE_OF_CONDUCT.md). Contributions are licensed GPLv3, matching [LICENSE](./LICENSE) and upstream Wazuh.

## Branch model

- Never commit to `main`. Create a branch (`docs/...`, `fix/...`, `feat/...`) and open a PR.
- Commit as soon as a fast check passes, then run slower verification. Amending is cheap; lost work is not.
- Keep PRs small and single-purpose. Reviews are required before merge.

## Commit convention

- Imperative subject line, short body explaining why.
- A `Co-Authored-By` trailer names the agent that actually did the work, or is left out. Never add one you cannot evidence.
- **No em dashes (U+2014) and no en dashes (U+2013)** in code, docs, commits or PR bodies. Use commas, parentheses, colons, periods or hyphens.

## Test gate

```bash
python3 <path-to-sk-standards>/scripts/docs_check.py --repo .
grep -rnP '\x{2014}|\x{2013}' --include='*.md' --exclude=LICENSE .   # must print nothing
```

## Rules for content

1. **No claim without evidence.** Never write "quantum-proof", "unhackable" or "complete coverage". skaegis detects known, published CVEs on top of upstream Wazuh.
2. **One canonical source.** State a fact once and link to it.
3. **Diagrams are mermaid** and updated in the same PR as the behavior they describe.
4. **No private data.** No host names, IPs, estate names, credentials or real findings. Use `<domain>` and `<env>` placeholders.
5. **Stay in sync with the role.** If the SKStacks role changes a port, path or service name, update `SOP.md` and its `docs-evidence` block in the matching PR.

## Review path

A maintainer reviews the PR, checks the docs gate is green, and merges. Doc changes that alter an operational instruction need a second look from someone who has deployed the role.

## Reporting security issues

Do not file them as public issues. See [SECURITY.md](./SECURITY.md).

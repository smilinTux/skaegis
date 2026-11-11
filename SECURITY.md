# Security Policy - skaegis

skaegis is a documentation and spec repo for a detection and remediation-orchestration service built on unmodified upstream Wazuh. This repo has no runtime, no listener and no key material, so it is **not a crypto component** and declares **maturity tier T0 - N/A**. The deployed service (SKStacks `optional/skaegis`) holds ordinary service secrets only.

## Reporting a vulnerability

- Email the maintainers at the security contact published on the smilinTux GitHub organization profile, or use GitHub private vulnerability reporting on https://github.com/smilinTux/skaegis.
- Do not open a public issue for a vulnerability. Give us a reasonable window to respond before disclosure.
- Vulnerabilities in **upstream Wazuh** itself should be reported to Wazuh through their own channel; tell us too if our deployment guidance makes it worse.

## Threat model summary

| Surface | Risk | Posture |
|---|---|---|
| Dashboard (the only public route) | Unauthorized access to fleet vulnerability data, which is a roadmap for an attacker. | Behind Traefik with crowdsec-bouncer and default middlewares, authenticated by sksso OIDC, admin via the `skadmin` group. |
| Agent enrolment channel | A rogue agent registering, or enrolment keys leaking. | Never exposed on a public port. Cluster key and enrolment secrets live in the vault. |
| Indexer and manager data | The CVE register lists exploitable hosts. | Internal network only, NFS data dir root mode 0700, daily archive with bounded retention. |
| Container images | Supply-chain tampering. | Unmodified upstream images pinned by `@sha256:`. The play refuses an unpinned image. |
| This repo's docs | A wrong instruction (for example an exposed port) adopted by operators. | Reviewed like code, `docs-evidence` block, no live values. |

## Secret handling

- OIDC client id and secret, indexer credentials and the cluster key live only in the per-instance vault (`skaegis-prod-<domain>_vault.yml`). Never inline a live secret in compose files, docs, MCP config, commits or fixtures.
- The break-glass admin credential is captured off-cluster at first deploy.
- `.gitignore` excludes vault files, `.env*` and key material.

## Dependency posture

- The only runtime dependency is upstream Wazuh (GPLv3), run unmodified. Updates are a deliberate digest bump reviewed through a PR.
- skaegis detects **known, published** CVEs. It does not find zero-days and does not make a host secure by itself.

## Compliance line

Maturity tier T0 - N/A. No CRYPTOGRAPHY_STANDARD suite claim is made because skaegis implements no cryptography of its own.

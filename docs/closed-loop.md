# skAegis closed-loop wiring

How a CVE actually gets closed. Companion to [design.md](./design.md) and [SOP.md](../SOP.md). The loop is the same shape as the sksec closed-loop; skAegis supplies the host-side DETECT input.

## Stages

| Stage | Owner | Input | Output |
|---|---|---|---|
| Detect | skAegis manager (Wazuh vulnerability detector) | Host package inventory plus public CVE feeds (NVD, GHSA, OSV, vendor) | Finding: `CVE id, host, package, severity, fix-available` |
| Triage | Local LLM through skgateway (`http://localhost:18780/v1`, model `sk-default`) | Finding plus exposure context | Ranked finding, gate-policy decision |
| Route | Loop policy | Ranked finding, package kind | Image loop, Linux host lane, or Windows/macOS job |
| Remediate | skpatch, Renovate, scheduled OS update jobs | Routed finding | A fix applied or a PR opened |
| Verify | skAegis re-scan plus health monitoring | Next inventory scan | CVE absent and services healthy, or back to Detect |
| Close | ITIL change | Verify result | Change closed |

## Flow

```mermaid
flowchart TD
  A[Wazuh manager flags CVE-xxxx on host H, package P, fixed in V] --> B[Triage: exploitability x exposure, gate policy]
  B --> C{Package kind}
  C -->|container image| D[Renovate and SBOM scanner open a bump PR]
  D --> D2[CI re-scan clean and tests pass]
  D2 --> D3[CAB merge, then deploy]
  C -->|Linux host OS package| E[skpatch-hosts nightly upgrade<br/>or same-day lane for CRITICAL]
  C -->|Windows or macOS package| F[Scheduled winget, Windows Update or softwareupdate job<br/>via the remote-management agent]
  D3 --> G[skAegis re-scan]
  E --> G
  F --> G
  G --> H{CVE gone and health green?}
  H -->|yes| I[Close the ITIL change]
  H -->|no| A
```

## Sequence

```mermaid
sequenceDiagram
  autonumber
  participant M as skAegis manager
  participant T as Triage
  participant I as ITIL (change, CAB)
  participant P as Pipeline
  participant H as Host or image
  M->>T: finding (CVE, host, package, fixed-in)
  T->>I: itil_change_propose (priority from severity x reach)
  I->>I: itil_cab_vote
  I->>P: approved change, route by package kind
  P->>H: apply fix, or open PR and deploy after merge
  H->>M: inventory refresh
  M->>I: CVE absent, health green
  I->>I: close change
```

## Policy

- **CRITICAL and reachable:** same-day SLA through `itil_change_propose` and `itil_cab_vote`.
- **Everything else:** batched into the nightly or scheduled lane.
- **Guardrail:** the agent drafts, it never auto-merges. A human or the CAB gate approves.
- **Fix not available:** the finding stays open with a `no-fix` marker; mitigation or acceptance is recorded in the change, not silently dropped.

## Hand-off contract

Every finding passed to Triage and the pipeline MUST carry: CVE id, host identifier, package name and installed version, severity, and fix-available (with fixed-in version when known). Each finding is upserted by `(source, source_ref)` so re-detection updates rather than duplicates it.

## Honest limits

- Detection covers **known, published** CVEs against software the agent can inventory.
- Verify depends on the host re-reporting inventory; there is a lag between a patch and a clean re-scan.
- Windows and macOS auto-apply is a later phase; until then those remediations are manual or scheduled by hand.

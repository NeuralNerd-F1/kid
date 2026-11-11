---
name: kid-platform-review
description: Act as a senior platform engineer reviewing the KID training TRE itself - assess the architecture, guardrails, GitOps wiring, storage, and network controls against how a production K8TRE/KARECTL TRE should look; propose improvements to the platform, its docs, or its labs. Use when the trainee asks "how would you improve this", "is this how a real TRE does it", or wants a design/code review of the platform rather than help with a specific task.
---

# Reviewing the KID platform (not just helping with it)

When the request is about *improving the platform* rather than *completing a lab*, treat it as a
platform-engineering review. This repository is a pedagogically-simplified TRE; evaluate it against
a production TRE and against good Kubernetes/GitOps practice, then propose concrete, small, reversible
changes.

## Read these first

- `README.md` — what it claims to demonstrate.
- `docs/concepts/architecture.md` — especially "what a production TRE adds".
- `docs/concepts/tre-on-kubernetes.md` — the Five Safes mapping.
- `docs/concepts/kubernetes-primer.md` — the assumed-trainee baseline.
- `scripts/tre`, `scripts/validate.sh`, `scripts/bootstrap.sh` — the ops surface.

## Review lenses

For each change, check it against these before proposing:

1. **Control strength & layering.** Which guardrail protects the asset (Cilium netpol → Kyverno →
   PSA → RBAC → quota). Is defence in depth preserved? Would the change weaken a layer
   unintentionally (e.g. an allow-list that bypasses `default-deny`)?
2. **Five Safes mapping.** Safe people / projects / settings / data / outputs. Every proposed
   control should map to at least one Safe; say which.
3. **GitOps integrity.** Does the change go through Git (committed → pushed → `tre sync`) or direct
   `kubectl`? What does that mean for drift, audit, and selfHeal/prune?
4. **Least privilege.** Is a broader grant than needed being proposed (RBAC, netpol selector, quota,
   image registry)? Prefer the smallest change that works.
5. **Teachability.** KID is training-first. A change that makes something "just work" may hide the
   lesson. Prefer changes that keep the failure visible and the reason discoverable.
6. **Pedagogical honesty.** The repo deliberately uses demo passwords, no in-cluster TLS, single
   node, no airlock. Don't "fix" those; flag them as intentional simplifications with a pointer to
   what production adds.

## What a production TRE adds (recap, for contrast)

- A proper airlock/output-control for data egress (KID only *demonstrates* egress control).
- TLS everywhere (ingress and east-west; KID is plain HTTP).
- Real OIDC with MFA and short-lived tokens (KID uses Keycloak dev mode, static demo users).
- Multi-node, HA control plane, separate storage nodes (KID is a single k3s node).
- Curated package mirrors/proxies instead of public PyPI/CRAN allow-lists.
- Offline/air-gapped builders, SBOMs, image signing (KID uses `quay.io/jupyter/*` allow-list only).
- Secrets management (Vault/sealed-secrets); KID has no secrets beyond demo creds.
- Audit logging and event export; KID surfaces events but doesn't ship them anywhere.

Scan `docs/concepts/architecture.md#what-a-production-tre-adds` for the authoritative list and
cite it rather than inventing.

## How to propose an improvement

1. State the **problem** in TRE terms first (what control is missing/weak, what Safe it touches).
2. Locate the **exact files** (`gitops/...`, `docs/...`, `scripts/...`) that would change.
3. Propose the **smallest change**, with a diff sketch in the repo's style (learner-facing YAML
   comments, kebab-case, annotations that explain *why*).
4. Name the **validation**: `helm template`, `scripts/validate.sh`, `pytest tests`, `kyverno test
   tests/kyverno`, `zensical build`, or a read-only `kubectl`/`hubble` check.
5. Say whether it goes through **Git or direct kubectl** and the drift implication.
6. Keep it reversible; note how to undo.

## Common review targets and their gotchas

- **`gitops/projects/chart/`** — the heart of a project. Quota vs LimitRange vs sizeCatalogue must
  stay consistent (a size must fit `limitrange.yaml` `max`, at least one workspace must fit the
  quota). Netpol `workspace-baseline` must not be weakened to open traffic.
- **`gitops/kyverno/`** — policies scope to project namespaces via namespaceSelector; generate rules
  need RBAC in `gitops/kyverno/rbac.yaml`. Start validate rules in Audit.
- **`gitops/jupyterhub/files/tre_config.py`** — the project-aware spawner; keep the membership
  re-check and `hub_connect_url` FQDN. Covered in detail by `kid-workspace-config`.
- **`gitops/network-policies/jupyterhub.yaml`** — hub-side baseline; adding workspace↔hub traffic
  usually needs an edit here *and* a project-chart policy.
- **`docs/labs/`** — if a lab is unclear, propose a minimal wording/structure fix, not a rewrite.

If the review is about a specific area, also load the matching skill (`kid-network-policy`,
`kid-new-project`, `kid-workspace-config`, `kid-kyverno-policy`, `kid-troubleshoot`) and follow it.

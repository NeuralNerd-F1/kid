---
description: Review a proposed platform change against TRE guardrails and GitOps
argument-hint: "<change>"
---

Review the proposed change — ${@:-described in context} — as a senior platform engineer. Check it
against: (1) control strength and defence in depth, (2) the Five Safes, (3) GitOps integrity (Git vs
direct kubectl, selfHeal/prune), (4) least privilege, (5) teachability. Do not weaken default-deny,
workspace-baseline, jupyterhub-baseline, Kyverno policies, Pod Security labels, quotas or RBAC unless
this is explicitly an experiment, and then say how to restore. Propose the smallest reversible change
with the exact files and the validation command.

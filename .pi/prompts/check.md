---
description: Check the health and GitOps sync state of the KID training cluster
argument-hint: "[component]"
---

Run a read-only health check of the KID cluster. First `tre status` (Argo CD apps and unhealthy
pods), then `tre usage` for memory. If I gave a component (${1:-}), focus on that area and use the
matching skill (kid-network-policy, kid-new-project, kid-troubleshoot, kid-workspace-config,
kid-kyverno-policy). Explain each finding in TRE terms and what a production TRE would add.
Prefer read-only kubectl/hubble commands. Do not change anything.

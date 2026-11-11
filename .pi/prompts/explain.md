---
description: Explain a TRE/Kubernetes concept in KID's vocabulary
argument-hint: "<concept>"
---

Explain ${@:-the requested concept} to a trainee who knows TREs but is new to Kubernetes. Ground it
in this repo: cite the relevant section of docs/concepts/, point to the gitops/ files that implement
it, and (where possible) show it live with a read-only kubectl/hubble command. Relate it to
isolation, egress control, storage scoping, resource limits, guardrails and the Five Safes. Keep it
concrete and lab-referenced, not an essay.

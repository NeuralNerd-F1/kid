---
name: kid-lab-qa
description: Validate that a KID lab exercise works end to end as written - walk through the steps, verify the expected outputs against the live cluster, catch steps that are stale, ambiguous or fail, and propose minimal fixes to the lab content. Use when asked to check/QA a lab, when a lab step doesn't produce the documented result, or when improving lab content before a trainee runs it.
---

# QA'ing a KID lab

Labs live in `docs/labs/01-…` to `10-…` with an `index.md`. Treat a QA pass as: reproduce the lab
against the live cluster, compare actual output to the claimed output, and report gaps.

## Method

1. **Read the lab in full** first (it may depend on earlier labs' state). Note every "you should see"
   claim and every command.
2. **Check preconditions**: is the cluster bootstrapped (`tre status`), is the right user logged in,
   is any prior state (projects, workspaces, FQDNs) assumed to exist? Many failures are missing
   preconditions, not wrong steps.
3. **Reproduce** each step with the same read-only command the lab shows. Prefer `kubectl`/`hubble`
   over memory. Capture actual output.
4. **Compare**: does the output match? If not, is the lab stale (wrong file path, renamed flag,
   version drift), ambiguous (order undefined), or actually broken?
5. **Check the "Check your understanding"** answers against the concept docs and the cluster.

## What to catch

- Stale paths after a refactor (files moved under `gitops/`, renamed templates).
- Version drift between the lab text and `versions.env` or the rendered manifests.
- Steps whose order matters but isn't stated (e.g. `tre hubble` before `hubble observe`).
- Assumptions about demo users/groups that no longer match `gitops/keycloak/realm-tre.json`.
- Claims that only hold under certain flags (e.g. `KID_ENABLE_KYVERNO=false` removes Kyverno).
- Missing clean-up at the end of a destructive lab.

## Where the expected results are encoded

`kid-lab-guide`'s "Expected results" table summarises the intended outcomes of each lab. Use it as
the oracle when the lab's own text is vague, and keep the two in sync if you fix one.

## Propose minimal fixes

1. Pinpoint the exact sentence/command/file and the smallest fix (a path, a flag, an ordering note).
2. State the validation: `helm template`, `scripts/validate.sh`, `pytest tests`, `kyverno test`,
   `zensical build`, or a read-only `kubectl`/`hubble` command.
3. Do **not** rewrite the lab wholesale; prefer wording/structure corrections that preserve the
   lesson. Keep learner-facing style (`docs/` is written in Zensical markdown).
4. If you changed `docs/`, run `zensical build --clean` (pip install zensical) to check it builds.

Report findings grouped as: (a) broken, (b) stale/ambiguous, (c) improvement suggestions, each with
file:step and the evidence you saw.

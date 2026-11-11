---
description: Grade a trainee's answer to a lab question with a hint ladder
argument-hint: "<lab> <question>"
---

The trainee is on lab ${1:-?} answering "${@:2}". Load the kid-lab-guide skill, find the lab and
the "Check your understanding" question, and grade their answer. Use the hint ladder: point to the
concept/file first, then the exact command, and only give the full answer if they ask or are clearly
stuck. Verify against the cluster with read-only commands and tie the result back to the Five Safes.

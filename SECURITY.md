# Security policy

## Scope

GLaDOS runs most of its core functionality in ring 0 and intentionally experiments with privileged model-driven actions. Treat security reports as high priority, especially reports involving:

- arbitrary ring-0 memory writes or code execution;
- guest-to-kernel or applet capability escapes;
- bypasses of read-only mode, write-range gates, update verification, or signature checks;
- authentication, TLS certificate validation, key handling, or secret disclosure;
- unsafe filesystem, NVMe, DMA, USB, or network behavior;
- release or CI workflows that could publish an unsigned or attacker-controlled image.

## Reporting a vulnerability

Please do **not** open a public issue for an undisclosed vulnerability. Use GitHub's private vulnerability reporting for this repository if it is enabled. If it is not available, contact the repository maintainers privately through a GitHub account with repository-owner access and include `GLaDOS security report` in the subject.

Include:

- a concise description and impact;
- the commit, release, or image affected;
- exact reproduction steps or a minimal proof of concept;
- hardware or QEMU configuration;
- logs, panic/fault output, and relevant hashes;
- whether the issue is already publicly known.

Please redact private keys, access tokens, wallet credentials, personal data, and proprietary firmware before sending logs. If a report contains a secret, revoke or rotate it immediately and mention that it was exposed.

## Response expectations

Reports are triaged as soon as practical. A fix may require a kernel change, a release rotation, a workflow change, or a documented limitation. Do not assume that a passing boot self-test means a security report is harmless: the self-tests are evidence for specific invariants, not a complete security proof.

## Supported releases

Security fixes are prioritized for the latest tagged release and the default branch. Because this project targets a specific laptop and has incomplete hardware isolation, reports should identify the exact hardware or emulator used.

## Safe research

Use isolated QEMU images and test keys. Do not point experimental update, pool, wallet, or contract commands at production infrastructure. Never test destructive disk or update behavior against a real machine without a verified rollback path and a backup.

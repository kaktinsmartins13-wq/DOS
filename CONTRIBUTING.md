# Contributing to GLaDOS

Thanks for taking the time to improve GLaDOS. This repository is a research operating system, not a conventional userspace project, so the most valuable contributions are changes that are **measurable, reproducible, and explicit about their hardware assumptions**.

## Before opening a pull request

1. Read [`README.md`](README.md) for the supported hardware, current limitations, and build model.
2. Read [`CLAUDE.md`](CLAUDE.md) for the repository's development commands and invariants.
3. Check existing issues and pull requests before starting a duplicate effort.
4. For changes to generated files, update the generator or source of truth instead of editing the generated output alone.
5. Do not add model checkpoints, private keys, firmware blobs, disk images, WADs, or other generated artifacts to git.

## Local verification

For kernel changes, run the strongest checks available on your machine:

```powershell
$env:PATH = "$env:USERPROFILE\\scoop\\persist\\rustup-msvc\\.cargo\\bin;$env:PATH"
cargo build --release
.\\scripts\\run.ps1 -Release
```

For QEMU-driven changes, build **release** first because `tools/drive.py` prefers the release artifact:

```powershell
.\\tools\\venv\\Scripts\\python.exe tools\\drive.py "initiative off" "agent stop" "diag all"
```

For host-side tooling, run the relevant `--selftest` commands. For pool or contract changes, run the package-specific commands documented in `pool/README.md` or `contracts/README.md`.

If hardware, a model, QEMU acceleration, or a secret is required and you cannot run a check, say so in the pull request rather than implying that it passed.

## Pull request expectations

A good pull request should include:

- **What changed** and why.
- **How it was tested**, including the exact command and environment where practical.
- **Evidence for behavior changes**: boot output, diagnostic output, benchmark output, or a reproduced failure/fix.
- **Known limitations**, especially hardware-specific behavior and untested drivers.
- **Format or compatibility impact**, if a file format, on-disk layout, protocol, update path, or public command changed.
- **Licensing/provenance notes** for imported code or constants.

Keep commits focused. Avoid mixing formatting-only churn with behavior changes; it makes kernel diffs and fault investigations substantially harder to audit.

## Safety boundaries

- Never commit secrets. This includes update keys, verdict keys, wallet keys, tokens, and credentials in logs.
- Never broaden a write path, update path, capability, or privileged applet without adding a refusal test for the unsafe case.
- Never claim a driver or hardware path works solely because it compiles.
- Do not change pinned signing keys, release workflows, or branch protections casually. Explain the rotation or security rationale in the PR.
- Keep tests independent of the implementation they verify where possible. A second copy of the same bug is not an oracle.

## Style

Follow the surrounding code. Prefer small, explicit functions and pure decision helpers for state machines and security-sensitive policy. Preserve comments that explain *why* an unusual choice exists; update them when behavior changes. Avoid speculative abstractions in the kernel.

## Review checklist

Before requesting review, confirm:

- [ ] The change builds with the pinned toolchain.
- [ ] Relevant self-tests, host tests, or QEMU checks were run.
- [ ] Generated artifacts were regenerated from their source.
- [ ] No secrets or large binary artifacts were added.
- [ ] README/docs and limitations are accurate.
- [ ] Failure and refusal paths are tested, not only the happy path.
- [ ] The PR description reports untested claims honestly.

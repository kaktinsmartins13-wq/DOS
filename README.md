# GLaDOS

A from-scratch, non-Unix, ring-0 operating system in Rust, built around a language model that runs inside the kernel.

GLaDOS is not a conventional kernel project. It treats the model, the task router, and the execution environment as first-class kernel primitives. The machine runs without a user/kernel split in the usual sense, and a tool call made by the model is a function call inside the same address space.

## Why this project exists

This project explores one question:

What changes when a language model is not an application layer above the kernel, but a kernel primitive itself?

That idea drives the architecture:

- no traditional Unix user/kernel boundary for the main model flow
- the applet grammar becomes the capability surface
- the kernel can route, validate, and execute tool calls directly
- boot, update, training, and self-modification are all part of the same system

## Project status

GLaDOS is a research kernel for a specific laptop, the MSI Thin GF63 12UC (board MS-16R8). It is functional and tested against that hardware and emulated environments, but it is still explicitly a research platform with real gaps.

### Verified or working

- UEFI boot as a kernel image
- identity-mapped memory setup and paging
- APIC timer, keyboard, and interrupt paths
- ring-3 guest support with Linux-compatible syscall surface
- graphics stack and desktop environment
- NVMe storage and content-addressed object store
- TCP/IP stack with ARP, IPv4, ICMP, UDP, TCP, DHCP, DNS, and TLS 1.3
- e1000 / RTL8168 / xHCI / USB device support
- model loading for Qwen3, hybrid Qwen3.5, and SmolLM2
- in-kernel inference and constrained decoding
- self-modification loop with judges, ledger, rollback, and staged updates
- mining and pool logic under the kernel
- training and adapter support for model decision layers

### Not complete yet

- SATA/AHCI support is missing
- wireless support is incomplete
- multi-core task audit is still not complete
- no hardware entropy source yet
- some fault reporting still falls back to serial output only
- not all authored applications have reached live adoption
- Wayland client work is still intentionally partial

## Quick start

### Prerequisites

This repository is built with a pinned nightly Rust toolchain and targets x86_64-unknown-uefi.

```bash
rustup show active-toolchain || rustup toolchain install
rustc --version --verbose
rustc --print target-list | grep -qx x86_64-unknown-uefi
```

The repository pins the toolchain in `rust-toolchain.toml` and expects no floating toolchain drift.

### Build

```bash
cargo build --release
```

The release artifact is:

```text
target/x86_64-unknown-uefi/release/glados.efi
```

### Run under QEMU

The project includes tooling to drive the kernel over QEMU. Use the release build before driving; the tooling prefers the release artifact.

```bash
python tools/drive.py "initiative off" "agent stop" "diag all"
```

Or use the provided scripts on Windows:

```powershell
.\scripts\run.ps1
.\scripts\run.ps1 -Release
.\scripts\run.ps1 -Gdb
```

## Hardware assumptions

GLaDOS is designed around a specific laptop and is not a generic desktop kernel by default.

The project expects:

- x86-64 UEFI firmware
- NVMe storage rather than SATA
- Intel PRO/1000 networking in VM setups
- a machine with enough memory for model weights and KV cache

This is especially important for virtualized runs. For example, VirtualBox and VMware commonly default to SATA, which this kernel cannot use without AHCI support.

## Model and payload setup

The model is not stored in this repository. It must be downloaded and converted separately.

```bash
huggingface-cli download Qwen/Qwen3-0.6B --local-dir tools/qwen3
python tools/convert.py tools/qwen3 esp/GLADOS/model.bin --seq 512
python tools/tokenizer.py tools/qwen3/tokenizer.json esp/GLADOS/tokenizer.bin --verify
```

The kernel expects files on the EFI system partition such as:

```text
<ESP>/EFI/BOOT/BOOTX64.EFI
<ESP>/GLADOS/model.bin
<ESP>/GLADOS/tokenizer.bin
<ESP>/GLADOS/roots.der   # optional, but recommended for trusted TLS roots
```

## Repository layout

```text
.
├── Cargo.toml                # kernel crate
├── rust-toolchain.toml       # pinned Rust toolchain
├── README.md                 # project entry point
├── CLAUDE.md                 # repo-specific development notes
├── .github/                  # workflows, rulesets, issue templates
├── src/                      # kernel and system implementation
├── tools/                    # Python utilities and model conversion tools
├── pool/                     # host-side pool and mining logic
├── miner/                    # mining client tooling
├── contracts/                # claim/distribution contract work
├── cuda/                     # host-side CUDA GPU work
├── docs/                     # website and release documentation
├── payload/                  # payload manifests and model references
├── scripts/                  # Windows build/run/deploy helpers
├── licenses/                 # licence and provenance material
├── design/                   # architecture and design notes
├── out/                      # generated build and release outputs
└── .gitignore                # repository hygiene and generated-artifact exclusions
```

## Development workflow

For kernel changes, the repo expects explicit evidence, not just compilation.

Recommended checks:

```powershell
cargo build --release
python tools/knob.py check
python tools/portcheck.py
python tools/workflows.py
python tools/cargocheck.py
```

For the self-modification and validator tooling:

```powershell
.\tools\venv\Scripts\python.exe tools\godel.py --selftest
.\tools\venv\Scripts\python.exe tools\rails.py --selftest
.\tools\venv\Scripts\python.exe tools\retrieval.py --selftest
```

## Security and safety

This project runs privileged code and intentionally modifies secure boundaries, update bundles, and kernel-side execution paths. Treat every change as potentially safety-critical.

- Do not commit secrets or private keys.
- Do not publish security issues publicly before triage.
- Do not test destructive update or disk operations on real hardware without rollback and backups.
- Treat the boot and update path as security-sensitive infrastructure.

See `SECURITY.md` for the vulnerability reporting policy.

## Contributing

See `CONTRIBUTING.md` for the contributor workflow and expectations.

The project values:

- measured changes over speculative ones
- reproducible validation
- exact hardware assumptions
- explicit limits and refusal behavior
- precise provenance for imported material

## Licensing and provenance

The repo is published with explicit copyright and licensing context. Some hardware tables and model assets are not authored here and carry their own provenance and restrictions.

- `README.md` and project docs describe architecture and project intent.
- certain generated or imported files carry their own licensing constraints.
- model checkpoints are not distributed here and are intentionally kept out of source control.

## Copyright

Copyright © 2026. All rights reserved.

No licence is granted for reuse of the project as a whole. If you intend to reuse anything, review the repository documentation and check for file-specific provenance and licensing notes.

## More information

- `CLAUDE.md` — deeper project-specific development notes
- `docs/` — public website and release-oriented documentation
- `.github/workflows/` — CI, release, and proof automation
- `design/` — architecture and design rationale

If you are exploring the project for the first time, start with `README.md`, `CLAUDE.md`, and the build/run steps above. The repository is intentionally dense and expects the reader to understand that this is a research system, not a standard commercial kernel.

---

GLaDOS is built to answer a very specific research question: what happens when a language model is part of the machine itself?

That question is the whole point of the project.


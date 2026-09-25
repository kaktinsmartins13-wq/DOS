# GLaDOS

A from-scratch, non-Unix, ring-0 operating system in Rust, built around a language model that runs inside the kernel.

GLaDOS is not a conventional kernel project. It is a research operating system built around a central question: what changes when a language model is a kernel primitive rather than an application layer?

## Why this project exists

This project explores one idea:

- the model is not just another userspace process
- the kernel can route, validate, and execute live decisions directly
- the applet grammar and capability model are part of the OS itself
- training, update, and self-modification are part of the same machine behavior

## Current project status

GLaDOS is a real research kernel for a specific laptop and a small set of supported VM configurations. It is strong in many areas, but it still documents and intentionally preserves known limitations.

## Quick start

### Build

```bash
cargo build --release
```

### Run under QEMU

```bash
python tools/drive.py "initiative off" "agent stop" "diag all"
```

Use the release artifact before driving; the project tooling prefers the release build.

## Repository map

```text
.
├── src/                # kernel implementation
├── tools/              # model conversion, self-test, and debug tools
├── pool/               # host-side pool and mining logic
├── miner/              # mining client tooling
├── contracts/          # claim/distribution contract work
├── cuda/               # CUDA host-side GPU work
├── docs/               # website and project docs
├── design/             # architecture and design notes
├── scripts/            # Windows build and deployment helpers
├── payload/            # model and payload metadata
├── .github/            # workflows, rules, issue templates
├── README.md           # landing page
├── CLAUDE.md           # deeper project notes
├── CONTRIBUTING.md      # contribution expectations
├── SECURITY.md          # security and reporting policy
└── rust-toolchain.toml # pinned toolchain
```

## Project maturity and support

The project is strongest when it names its reality precisely.

- Tested: the MSI GF63 development platform and supported VM configuration
- Expected: broader x86_64 UEFI platforms and common VM setup
- Experimental: wireless, some advanced path support, and early design features
- Unsupported: SATA/AHCI, non-UEFI systems, and any path not validated

See [`docs/HARDWARE-COMPATIBILITY.md`](docs/HARDWARE-COMPATIBILITY.md) for the full matrix.

## Roadmap

The project is moving from a research artifact toward a disciplined engineering platform.

See [`docs/ROADMAP.md`](docs/ROADMAP.md) for the planned milestones and priorities.

## Key files

- [`README.md`](README.md) — entry point and overview
- [`CLAUDE.md`](CLAUDE.md) — deep project-specific notes for development
- [`CONTRIBUTING.md`](CONTRIBUTING.md) — contributor guidance
- [`SECURITY.md`](SECURITY.md) — security reporting and boundaries
- [`docs/ROADMAP.md`](docs/ROADMAP.md) — milestone and backlog plan
- [`docs/HARDWARE-COMPATIBILITY.md`](docs/HARDWARE-COMPATIBILITY.md) — hardware support matrix

## Known limitations

The repo remains honest about the following:

- no SATA/AHCI support
- wireless path is incomplete
- full multi-core audit is still pending
- no hardware entropy source is yet used
- some fatal-report paths still depend on serial output
- model and payload setup must be deliberate and external to git

Those are not “bugs to hide”; they are part of the project’s engineering reality.

## Safety and security

This is a privileged, security-sensitive project. Treat it as such.

- no secrets in git
- no public issue for undetected security flaws
- no unsafe update or disk activity on real hardware without rollback and backups

See [`SECURITY.md`](SECURITY.md).

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for how to propose changes and what good PRs look like.

The project values:

- measured changes over speculation
- reproducible validation
- explicit hardware assumptions
- honest limitations
- clear provenance for imported or generated code

# GLaDOS roadmap

This project is intentionally ambitious and hardware-specific. The roadmap below is designed to turn it from a research artifact into a disciplined engineering platform without pretending that every feature is already complete.

## Phase 1: trusted foundations

Goal: make the repo reliable, reproducible, and easy to reason about.

Planned outcomes:

- contributor workflow is documented and enforced
- vulnerability disclosure path is clear
- issue templates separate bug reports from research proposals
- build instructions and toolchain requirements are pinned and explicit
- the repository distinguishes tested vs experimental behavior

Current status:

- contributor guidance exists
- security policy exists
- issue templates are in place
- README is clearer for new visitors

## Phase 2: reliability and compatibility

Goal: make the system reliable on the machines it actually targets.

Priority items:

- finish AHCI/SATA support so SATA disks are no longer a dead end
- improve console/fault reporting for fatal errors and framebuffer issues
- document the exact VM and hardware requirements for boot success
- add compatibility notes for QEMU, VirtualBox, VMware, and the MSI laptop
- separate supported, expected, and experimental hardware paths

Success criteria:

- a user can boot on the target hardware or a clearly documented VM configuration
- a failure mode is explainable by hardware and driver configuration rather than mystery

## Phase 3: security and update hygiene

Goal: ensure the privileged and update-sensitive paths are harder to misuse.

Priority items:

- formal update verification checklist
- stricter signing and rollback validation
- explicit rotation procedure for update keys
- stronger boundary checks around model and payload staging
- security verification for all privileged paths before release

Success criteria:

- every update path is auditably signed and verifiable
- a stale or mismatched artifact fails before it is published
- privilege boundaries are documented and tested

## Phase 4: model and training integrity

Goal: make the model route, training, and adaptation flow reproducible and understandable.

Priority items:

- model conversion validation with clear reproducible checks
- tokenizer verification against reference implementations
- training path measurements that separate fixed cost from variable cost
- adapter save/load verification and content-address integrity
- explicit refusal rules for unsupported model families and memory configs

Success criteria:

- model load and conversion issues fail loudly with a named reason
- each training path has a documented invariant and benchmark plan
- adapter and model artifacts can be checked independently from runtime state

## Phase 5: platform breadth and operational maturity

Goal: turn a research kernel into a more maintainable platform.

Priority items:

- complete multi-core audit and safe unpinning rules
- improve wireless path from driver layer to real device support
- improve guest isolation and syscall discipline verification
- reduce silent breakage from stale generated artifacts or tables
- improve developer tooling for host-side checking and release assembly

Success criteria:

- major subsystems have a defined support level
- the difference between “tested,” “expected,” and “experimental” is consistently enforced
- the project has a measurable runway toward broader support

## Backlog by impact

Highest impact items:

1. SATA/AHCI support
2. fault-reporting and crash diagnostics
3. entropy source and secure boot-like assumptions
4. multi-core audit / scheduler safety
5. stronger update and signing validation
6. hardware compatibility matrix and test matrix enforcement

Medium impact items:

- better wireless support
- Wayland client progress
- app adoption and machine-authored workflow completion
- broader guest support and cleaner syscall audit

Lower impact but valuable items:

- docs polish
- developer tooling ergonomics
- release notes automation
- website and project messaging clarity

## Engineering principle

The repository should never confuse “interesting” with “ready.” A feature is not complete because it compiles or boots once. It is complete when it has a known support level, a reproducible validation path, and a clear statement of its remaining limitations.

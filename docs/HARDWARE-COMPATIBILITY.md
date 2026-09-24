# Hardware compatibility and support matrix

This project is intentionally specialized. GLaDOS is most credible when it states exactly what is supported, what is expected, and what is still experimental.

## Support levels

- Tested: the project has direct evidence on this hardware or path.
- Expected: the architecture appears compatible, but validation is incomplete.
- Experimental: partial implementation exists, but it is not a reliable production path.
- Unsupported: known not to work or intentionally refused.

## Primary hardware

| Platform | Status | Notes |
|---|---|---|
| MSI Thin GF63 12UC / board MS-16R8 | Tested | Primary development machine and primary reference platform. |
| x86-64 UEFI desktop or laptop | Expected | The kernel assumes UEFI and long mode; not every system is expected to boot cleanly. |
| QEMU x86_64 with UEFI | Expected | Works with carefully chosen VM settings and a suitable NVMe disk model. |
| VirtualBox / VMware guest | Expected | Requires explicit VM settings; SATA defaults are a problem. |
| Non-UEFI x86 systems | Unsupported | This is not a BIOS kernel. |

## Storage

| Storage path | Status | Notes |
|---|---|---|
| NVMe | Tested | The main supported storage path. |
| SATA/AHCI | Unsupported / planned | Missing driver; no real support yet. |
| Virtual disk with NVMe controller | Expected | Preferred in VM environments. |
| SATA VM default config | Known issue | Common default, and a frequent source of confusion. |

## Networking

| Path | Status | Notes |
|---|---|---|
| e1000 / e1000e | Supported | Strongest VM and hardware network path documented in the project. |
| RTL8168 | Supported in principle | Driver exists; hardware support is specific and must be validated. |
| USB CDC-ECM | Experimental | Exists and is implemented, but not the main path. |
| wireless / CNVi | Experimental | Partial driver and security stack exist; hardware path remains incomplete. |

## Memory and model path

| Area | Status | Notes |
|---|---|---|
| 4 GiB+ host memory | Expected | Required for model weights and KV cache. |
| large model staging | Expected | Requires enough memory and proper payload layout. |
| model conversion and tokenizer verification | Tested | The repo documents the expected verification path. |
| unsupported model families | Refused | MoE and unsupported structures are rejected at load time. |

## Virtualization notes

The project explicitly expects a few VM settings to be correct:

- firmware must be UEFI
- disk controller should be NVMe
- guest memory should be at least 4 GB
- network card should be Intel PRO/1000 or e1000e where possible
- use a writable OVMF vars file for firmware configuration

If these settings are wrong, the ELF boot path still starts, but much of the system cannot operate meaningfully.

## Known project constraints

- This is not a general-purpose kernel for arbitrary hardware.
- A model may load correctly and still be unusable due to memory or cache sizing.
- Features may compile but remain unsupported until they have explicit validation.
- a working boot path is not the same as a production-safe subsystem.

## Recommended operating principle

Every new subsystem should declare one of these before it is treated as “done”:

- tested
- expected
- experimental
- unsupported

The repo is stronger when it names the reality plainly than when it overclaims it.

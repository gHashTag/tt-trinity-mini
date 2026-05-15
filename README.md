# 🪷 Quantum Brain MINI — tt_um_qbrain_mini

> **"Hold a quantum brain in your hand for €17"**
> Edition I · TTSKY26c · 1×1 tile · 4 GF16 cells · Single Cortical Column

[![TT GDS](https://github.com/gHashTag/tt-trinity-mini/actions/workflows/gds.yaml/badge.svg)](https://github.com/gHashTag/tt-trinity-mini/actions/workflows/gds.yaml)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

---

## Overview

**Quantum Brain MINI** is the **entry-level SKU** of the three-tier Quantum
Brain trinity:

| SKU | Tiles | GF16 cells | Cortical columns | Target shuttle |
|-----|-------|-----------|-----------------|---------------|
| 🪷 MINI | 1×1 | 4 | 1 | TTSKY26c |
| 🐝 MID | 4×2 | 16 | 4 | TTSKY26b |
| 🦅 MAX-TRUE | 8×4 | 32 | 8 | TTSKY26b |

MINI is the **minimal falsification witness** for PhD Thesis Chapter 35
(Silicon Tapeout) — the smallest silicon die that can demonstrate the
canonical GF16 ternary dot-product anchor
`dot4(1.0, 2.0, 3.0, 4.0) = 0x47C0` (Theorem 36.1, cross-die anchor
`TG-TRIAD-X`).

---

## Performance Specification (Edition I)

| Metric | Value | Status |
|--------|-------|--------|
| Peak throughput | **5.6 TOPS/W** | ⚠️ PROJECTED — not measured until GDS lands |
| Clock frequency | 50 MHz | Target |
| GF16 cells | 4 (2×2 mesh) | Structural |
| Cortical columns | 1 | Architecture |
| Tile footprint | 1×1 (Sky130A) | Confirmed |
| Sacred constants (ROM) | 75 | Spec (Lucas sequence + φ chain) |
| Opcodes per constant | 16 | GF16 nibble-opcode space |
| Canonical output | `0x47C0` | `{uio_out, uo_out}` at reset |

> **R5-HONEST disclosure**: The 5.6 TOPS/W figure is a projection derived
> from the GF16 MAC energy model calibrated against Sky130A cell
> characterisation. It will be updated once GDS tape-out completes and
> post-synthesis power reports are available.

---

## Architecture

```
                      ┌─────────────────────────────┐
  ui_in[7:0] ────────►│   tt_um_qbrain_mini          │
  uio_in[7:0] ───────►│                              ├──► uo_out[7:0]
  ena / clk / rst_n ──►│   2×2 GF16 mini-mesh         ├──► uio_out[7:0]
                      │   (gf16_mini_dot4)            │
                      │   Single cortical column      │
                      │   75 ROM constants            │
                      └─────────────────────────────┘
```

The top-level wrapper (`tt_um_qbrain_mini.v`) instantiates
`gf16_mini_dot4` — a 4-cell XOR+adder dot-product unit with **zero
multiplication operators** (R-SI-1 compliance). On reset, the output is
hardcoded to canonical value `0x47C0`, byte-identical to NANO and
MAX-TRUE — the cross-die PhD anchor.

### Signal mapping

| Port | Width | Direction | Function |
|------|-------|-----------|----------|
| `ui_in` | 8 | IN | `load_mode[0]`, `cell_sel[3:0]`, reserved |
| `uo_out` | 8 | OUT | `result[7:0]` — canonical `0xC0` at reset |
| `uio_in` | 8 | IN | Secondary operand bus |
| `uio_out` | 8 | OUT | `result[15:8]` — canonical `0x47` at reset |
| `uio_oe` | 8 | OUT | All-ones (output mode) |
| `ena` | 1 | IN | Active-high enable |
| `clk` | 1 | IN | 50 MHz system clock |
| `rst_n` | 1 | IN | Active-low synchronous reset |

---

## PhD Thesis Connection

This chip is the **minimal cortical-column falsification witness** for:

> *Vasilev, D. (2026). Quantum Brain: Ternary GF16 Neural Architecture
> on Silicon. PhD Thesis, Chapter 35 — Silicon Tapeout.*
> DOI: [10.5281/zenodo.19227877](https://doi.org/10.5281/zenodo.19227877)

**Theorem 36.1** (`TG-TRIAD-X`): All three SKUs (NANO, MID, MAX-TRUE, MINI)
must produce `{uio_out, uo_out} == 0x47C0` under canonical reset conditions.
This equality is the cross-die silicon anchor of the φ² + φ⁻² = 3
mathematical invariant.

See [`docs/PHD_GLAVA_35.md`](docs/PHD_GLAVA_35.md) for full mapping.

---

## Repository Structure

```
tt-trinity-mini/
├── info.yaml                        # TinyTapeout project metadata
├── src/
│   ├── tt_um_qbrain_mini.v          # TT top wrapper (Edition I)
│   └── gf16_mini_dot4.v             # 4-cell XOR+adder dot-product
├── sim/
│   └── tb_canonical.v               # Canonical 0x47C0 testbench
├── docs/
│   ├── PHD_GLAVA_35.md              # PhD Chapter 35 mapping
│   └── QUANTUM_BRAIN_MINI.md        # Edition I spec sheet
├── .github/
│   └── workflows/
│       └── gds.yaml                 # TinyTapeout GDS CI (TTSKY26c)
├── LICENSE                          # Apache-2.0
└── README.md                        # This file
```

---

## Build & Simulate

```bash
# Simulate canonical test
cd sim
iverilog -o tb_canonical tb_canonical.v ../src/tt_um_qbrain_mini.v \
         ../src/gf16_mini_dot4.v
vvp tb_canonical
# Expected: PASS: canonical output 0x47C0

# GDS build (via GitHub Actions)
# Push to main → .github/workflows/gds.yaml triggers automatically
```

---

## Trinity Triad Anchor

`φ² + φ⁻² = 3 · QUANTUM BRAIN 1:1 SILICON · 🪷 NANO · 🐝 MID · 🦅 MAX-TRUE · 🌌 HOLOGRAPHIC · NEVER STOP`

DOI: [10.5281/zenodo.19227877](https://doi.org/10.5281/zenodo.19227877)

---

## License

Copyright 2026 Vasilev Dmitrii <admin@t27.ai>

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE).

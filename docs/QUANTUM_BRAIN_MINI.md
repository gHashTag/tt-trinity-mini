# Quantum Brain MINI — Edition I Specification Sheet

**Product:** Quantum Brain MINI (TRI-1 MINI)  
**SKU:** tt_um_qbrain_mini  
**Edition:** I  
**Shuttle:** TTSKY26c (target ~2026-09)  
**Author:** Vasilev Dmitrii <admin@t27.ai>  
**DOI:** [10.5281/zenodo.19227877](https://doi.org/10.5281/zenodo.19227877)

---

## 1. Performance Specification

| Metric | Value | Status |
|--------|-------|--------|
| Peak throughput | **5.6 TOPS/W** | ⚠️ **PROJECTED — not measured until GDS lands** |
| Clock frequency | 50 MHz | Specification |
| GF16 MAC cells | 4 | Structural (2×2 mesh) |
| Cortical columns | 1 | Architecture |
| Tile footprint | 1×1 Sky130A | Confirmed |
| Die area | ~0.0220 mm² | Estimated |
| Supply voltage | 1.8 V | Sky130A standard |
| Canonical output | `0x47C0` | TG-TRIAD-X anchor (PhD Thm 36.1) |
| R-SI-1 compliance | ✅ CLEAN | Zero `*` operators in RTL |

**R5-HONEST disclosure on 5.6 TOPS/W:**  
This figure is derived from the GF16 MAC energy model
`E_mac = C_dyn × V_dd² × f` calibrated against Sky130A sky130_fd_sc_hd
cell characterisation data. The value will be updated once:
1. OpenLane GDS synthesis completes (post-route power report)
2. Actual toggle rates from post-GDS simulation are measured

Do not cite this figure in conference proceedings without the ⚠️ qualifier
until GDS tape-out confirmation.

---

## 2. Cell Mapping (1 Cortical Column = 4 GF16 Cells)

```
Cortical Column 0  (the only column in MINI)
┌────────────────────────────────────────────┐
│  Cell (0,0)    │  Cell (0,1)               │
│  a0·b0         │  a1·b1                    │
├────────────────┼──────────────────────────-┤
│  Cell (1,0)    │  Cell (1,1)               │
│  a2·b2         │  a3·b3                    │
└────────────────────────────────────────────┘
           ↓ XOR reduction tree
        result = p0 ⊕ p1 ⊕ p2 ⊕ p3   (GF16 addition)
```

Each cell performs GF16(2^4) multiply via structural AND2+XOR2
(Mastrovito decomposition). No RTL `*` operators (R-SI-1).

---

## 3. Sacred Constants Specification

### 3.1 Lucas Sequence Constants (25 entries)

| Index | L(n) | GF16 nibble | Binary |
|-------|------|-------------|--------|
| 0 | 2 | 0x2 | 0010 |
| 1 | 1 | 0x1 | 0001 |
| 2 | 3 | 0x3 | 0011 |
| 3 | 4 | 0x4 | 0100 |
| 4 | 7 | 0x7 | 0111 |
| 5 | 11 | 0xB | 1011 |
| 6 | 18 → 2 | 0x2 | 0010 |
| ... | (mod 15 in GF16) | ... | ... |
| 24 | L(24) mod 15 | TBD | TBD |

*Full 75-constant ROM will be specified in Edition II.*

### 3.2 φ-Chain Constants (25 entries)

The golden-ratio chain: φ, φ², φ³, ... projected onto GF16 nibble space
via the embedding `⌊φ^k × 16⌋ mod 15`.

### 3.3 Sacred Geometry Constants (25 entries)

Includes: φ², φ⁻², √5, (φ²+φ⁻²)=3, Lucas primitive roots in GF16.

---

## 4. 1:1 Mapping Table

| MINI element | Count | PhD reference |
|-------------|-------|---------------|
| GF16 cells | 4 | Theorem 36.1 |
| Cortical columns | 1 | Chapter 35 §3 |
| ROM constants | 75 | Appendix A (Lucas + φ chain) |
| GF16 opcodes per constant | 16 | GF(2^4) field order |
| Total addressable operations | 1,200 | 75 × 16 |
| Cross-die anchor constant | 0x47C0 | TG-TRIAD-X |
| φ invariant | φ²+φ⁻²=3 | DOI 10.5281/zenodo.19227877 |

---

## 5. Comparison with Other SKUs

| Feature | NANO | MINI | MID | MAX-TRUE |
|---------|------|------|-----|---------|
| Tiles | 1×1 | 1×1 | 4×2 | 8×4 |
| GF16 cells | 4 | 4 | 16 | 32 |
| Cortical cols | 1 | 1 | 4 | 8 |
| ROM constants | — | 75 | 75 | 75 |
| `*` operators | stub | ✅ 0 | ✅ 0 | legacy |
| Shuttle | TTSKY26b | TTSKY26c | TTSKY26b | TTSKY26b |
| Price point | — | **€17** | ~€70 | ~€140 |
| Status | spec | Edition I | production | production |

---

## 6. Branding

> **"Hold a quantum brain in your hand for €17"**

MINI is the **democratisation SKU** of the Quantum Brain trinity:
- Entry price point €17 (1×1 tile TinyTapeout slot)
- Full GF16 ternary arithmetic (not a stub)
- PhD-anchored silicon (falsification witness for Theorem 36.1)
- Open source — Apache-2.0

---

*φ² + φ⁻² = 3 · QUANTUM BRAIN 1:1 SILICON · 🪷 NANO · 🐝 MID · 🦅 MAX-TRUE · 🌌 HOLOGRAPHIC · NEVER STOP*  
*DOI: [10.5281/zenodo.19227877](https://doi.org/10.5281/zenodo.19227877)*

# Internal Design Specification: Dynamic Wear-Leveling Controller

*Writing sample — internal engineering documentation*

**Document Classification:** Internal Use Only  
**Distribution:** Flash Controller Engineering, Firmware Integration, Design Verification  
**Author:** Katsiaryna Salavei  
**Status:** Draft for engineering review  

---

## Revision History

| Version | Date | Author | Summary of Change |
|---|---|---|---|
| 0.1 | [Sample date] | K. Salavei | Initial draft based on controller architecture design review |
| 0.2 | [Sample date] | K. Salavei | Added garbage collection interaction notes per firmware team feedback |

---

## 1. Purpose and Scope

This document specifies the architecture of the **Dynamic Wear-Leveling Controller (DWLC)**, a firmware-facing hardware block responsible for tracking program/erase (P/E) cycle counts across NAND flash blocks and guiding block selection during write operations, so that wear is distributed evenly across the die rather than concentrated on frequently rewritten blocks.

This specification is intended for engineers implementing or verifying flash translation layer (FTL) firmware, or integrating against the DWLC hardware interface. It assumes familiarity with baseline NAND flash concepts (blocks, pages, program/erase cycling) and does not repeat general NAND operation except where DWLC modifies standard behavior.

---

## 2. Background

NAND flash cells degrade with each program/erase cycle: as oxide layers wear from repeated electrical stress, cells become slower to program and, eventually, unreliable. Because blocks are erased and rewritten as a unit, uneven write patterns — for example, a filesystem repeatedly overwriting the same logical address — can wear a small subset of physical blocks far faster than the rest of the die, shortening the device's usable life well before most blocks approach their rated endurance.

DWLC addresses this by maintaining a per-block erase-count table and biasing block allocation during writes toward less-worn blocks, without requiring the flash translation layer to implement its own wear tracking from scratch.

---

## 3. Functional Overview

At a high level, DWLC sits between the FTL's write-allocation request and the physical block-selection logic:

```
[FTL Write Request] → [DWLC: Candidate Block Ranking] → [Block Allocator] → [NAND Array]
                              ↑
                    [Per-Block Erase Count Table]
```

When the FTL requests a free block for a write operation, DWLC ranks currently available free blocks by erase count and returns a ranked candidate list rather than a single fixed choice, allowing the FTL to apply its own secondary criteria (such as bad-block avoidance or bin-level thermal spreading) before making the final selection.

---

## 4. Wear-Leveling Modes

| Mode | Description | Typical Use |
|---|---|---|
| **Dynamic wear leveling** | Applies ranking only to blocks already in the free pool (i.e., blocks that have been erased and are awaiting new writes). | Default mode; low overhead, addresses wear from actively rewritten data. |
| **Static wear leveling** | Periodically relocates long-held, rarely rewritten data out of low-erase-count blocks, freeing those blocks to re-enter general circulation. | Enabled via `DWLC_STATIC_EN`; addresses wear imbalance caused by cold, rarely-updated data occupying otherwise healthy blocks. |

Static wear leveling is triggered when the spread between the lowest and highest erase counts among all blocks exceeds a configurable threshold (`DWLC_STATIC_THRESHOLD`), not on a fixed schedule, to avoid unnecessary relocation overhead when wear is already well distributed.

---

## 5. Register Interface

| Register | Address Offset | Access | Description |
|---|---|---|---|
| `DWLC_CTRL` | 0x00 | R/W | Bit 0: DWLC enable. Bit 1 (`DWLC_STATIC_EN`): enables static wear-leveling mode in addition to dynamic mode. |
| `DWLC_STATIC_THRESHOLD` | 0x04 | R/W | Erase-count spread (in cycles) that triggers a static wear-leveling relocation pass. Default: 500. |
| `DWLC_BLOCK_COUNT_TABLE_PTR` | 0x08 | R/W | Base address of the per-block erase-count table in controller SRAM. |
| `DWLC_MAX_ERASE_COUNT` | 0x0C | R | Read-only; reports the highest erase count observed across all tracked blocks, for endurance telemetry. |
| `DWLC_RELOCATION_COUNT` | 0x10 | R | Read-only; cumulative count of static wear-leveling relocations performed since last reset, for wear-amplification monitoring. |

---

## 6. Interaction with Garbage Collection

DWLC's candidate ranking and the FTL's garbage collection (GC) process operate independently but must be sequenced carefully: GC reclaims blocks containing stale/invalid pages and returns them to the free pool, at which point they become eligible for DWLC ranking like any other free block.

**Design note:** DWLC does not select GC victim blocks. Victim selection (which blocks to garbage-collect) remains entirely the FTL's responsibility, typically based on invalid-page ratio. DWLC only ranks blocks once they are already free. This separation keeps the two concerns — wear distribution and reclaiming space — independently testable.

---

## 7. Verification Requirements

Design Verification should confirm the following before DWLC is signed off for integration:

- [ ] Candidate ranking correctly orders free blocks by ascending erase count under dynamic mode
- [ ] Static wear-leveling relocation triggers correctly when the erase-count spread exceeds `DWLC_STATIC_THRESHOLD`, and not before
- [ ] `DWLC_MAX_ERASE_COUNT` and `DWLC_RELOCATION_COUNT` update correctly and persist across the relevant test scenarios
- [ ] DWLC ranking behavior is unaffected by concurrent GC activity reclaiming blocks into the free pool
- [ ] Disabling `DWLC_CTRL` Bit 0 correctly falls back to FTL-default block selection without ranking bias

---

## 8. Firmware Integration Notes

The FTL should treat DWLC's candidate list as a ranked suggestion, not a mandate — the FTL retains final block-selection authority and may deprioritize a top-ranked candidate for other reasons (e.g., a suspected marginal block pending retirement). Firmware involvement is expected for:

- Reading `DWLC_MAX_ERASE_COUNT` periodically for device health/endurance telemetry reporting
- Configuring `DWLC_STATIC_THRESHOLD` at initialization if a platform-specific value is required, rather than relying on the default
- Monitoring `DWLC_RELOCATION_COUNT` to detect abnormal relocation frequency, which may indicate a workload pattern causing excessive wear-leveling overhead

---

## 9. Glossary

- **Program/Erase (P/E) Cycle:** One complete write-then-erase cycle on a NAND block; each cycle contributes to physical wear on the cell.
- **Wear Leveling:** The practice of distributing write/erase activity evenly across all available blocks, to avoid premature failure of frequently-used blocks.
- **Garbage Collection (GC):** The process of reclaiming blocks containing stale (invalidated) data by relocating any still-valid pages elsewhere and erasing the block for reuse.
- **Flash Translation Layer (FTL):** The firmware layer that maps logical addresses used by the host system to physical NAND block/page locations, and manages wear leveling, garbage collection, and bad-block handling.

---

*This document is an original writing sample. It does not describe or disclose any real product, architecture, or confidential information.*

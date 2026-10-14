# Dynamic Wear-Leveling Controller

*Writing sample: Internal design specification*

---

## Table of contents

- [Revision history](#revision-history)
- [1. Purpose and scope](#1-purpose-and-scope)
- [2. Background](#2-background)
- [3. Functional overview](#3-functional-overview)
- [4. Wear-leveling modes](#4-wear-leveling-modes)
- [5. Register interface](#5-register-interface)
  - [Control bits](#control-bits)
- [6. Interaction with garbage collection](#6-interaction-with-garbage-collection)
  - [Processing sequence](#processing-sequence)
- [7. Verification requirements](#7-verification-requirements)
- [8. Firmware integration](#8-firmware-integration)
- [9. Glossary](#9-glossary)

---

## Revision history

| Version | Author | Summary of change |
|---|---|---|
| 0.1 | K. Salavei | Initial draft based on controller architecture design review |
| 0.2 | K. Salavei | Added garbage collection interaction notes based on firmware team feedback |

## 1. Purpose and scope

The **Dynamic Wear-Leveling Controller (DWLC)** is a firmware-facing hardware block that tracks program/erase (P/E) cycle counts for NAND flash blocks and ranks free blocks for write allocation.

DWLC distributes write activity across blocks by ranking available blocks according to erase count. The Flash Translation Layer (FTL) retains final block-selection authority and can apply additional criteria, such as bad-block avoidance or thermal spreading.

This specification is intended for engineers who implement or verify FTL firmware or integrate firmware with the DWLC hardware interface.

The specification assumes familiarity with NAND flash concepts, including blocks, pages, and program/erase cycles. It describes general NAND behavior only where that behavior affects DWLC operation.

## 2. Background

NAND flash cells degrade as program/erase cycles accumulate. Repeated electrical stress can increase program and erase latency and eventually reduce cell reliability.

Because NAND blocks are erased as a unit, uneven write patterns can cause some physical blocks to accumulate erase cycles faster than others. For example, repeatedly updating the same logical address can cause a small subset of physical blocks to wear faster than the rest of the die.

DWLC maintains an erase-count value for each tracked block and uses those values to rank free blocks for allocation. This allows the FTL to use hardware-maintained wear information without implementing its own erase-count tracking.

## 3. Functional overview

DWLC operates between the FTL's write-allocation request and the block-selection logic.

```text
[FTL write request] → [DWLC: candidate block ranking] → [Block allocator] → [NAND array]
                                  ↑
                        [Per-block erase-count table]
```

When the FTL requests a free block, DWLC ranks the available free blocks by erase count and returns a ranked candidate list.

The FTL uses the candidate list as a recommendation and can apply additional selection criteria before choosing a block. For example, the FTL can deprioritize a candidate because of bad-block handling or thermal distribution requirements.

## 4. Wear-leveling modes

DWLC supports dynamic and static wear leveling.

| Mode | Description | Typical use |
|---|---|---|
| **Dynamic wear leveling** | Ranks blocks that are already in the free pool and available for new writes. | Default mode. Distributes wear among blocks that are actively reused. |
| **Static wear leveling** | Relocates long-lived data from low-erase-count blocks so that those blocks can re-enter the free pool. | Addresses wear imbalance caused by cold data occupying blocks with relatively low erase counts. |

Dynamic wear leveling is enabled when the DWLC is enabled.

Static wear leveling is enabled by `DWLC_STATIC_EN`. A static wear-leveling pass starts when the difference between the lowest and highest erase counts among tracked blocks exceeds `DWLC_STATIC_THRESHOLD`.

Static wear leveling isn't triggered on a fixed schedule. This prevents relocation when the erase-count distribution is already within the configured threshold.

## 5. Register interface

The following registers configure and report DWLC operation.

| Register | Address offset | Access | Description |
|---|---:|---|---|
| `DWLC_CTRL` | `0x00` | R/W | Control register. Bit 0 enables DWLC. Bit 1 (`DWLC_STATIC_EN`) enables static wear leveling. |
| `DWLC_STATIC_THRESHOLD` | `0x04` | R/W | Erase-count spread that triggers a static wear-leveling relocation pass. Default: `500` cycles. |
| `DWLC_BLOCK_COUNT_TABLE_PTR` | `0x08` | R/W | Base address of the per-block erase-count table in controller SRAM. |
| `DWLC_MAX_ERASE_COUNT` | `0x0C` | R | Highest erase count currently observed among tracked blocks. Used for endurance telemetry. |
| `DWLC_RELOCATION_COUNT` | `0x10` | R | Cumulative number of static wear-leveling relocations performed since the last reset. Used to monitor wear-leveling overhead. |

### Control bits

| Register | Bit | Name | Description |
|---|---:|---|---|
| `DWLC_CTRL` | 0 | — | `1` enables DWLC ranking. `0` disables DWLC ranking and returns block selection to the FTL. |
| `DWLC_CTRL` | 1 | `DWLC_STATIC_EN` | `1` enables static wear leveling. `0` disables static wear leveling. |

## 6. Interaction with garbage collection

DWLC ranking and FTL garbage collection (GC) perform separate functions.

GC identifies blocks that can be reclaimed. After GC relocates valid pages and erases a reclaimed block, the block returns to the free pool. DWLC can then include the block in its candidate ranking.

DWLC doesn't select GC victim blocks. Victim selection remains the responsibility of the FTL and can use criteria such as the number of invalid pages.

This separation allows wear distribution and space reclamation to be implemented and verified independently.

### Processing sequence

The expected sequence is:

1. The FTL selects a block for garbage collection.
2. GC relocates any valid pages.
3. The FTL erases the reclaimed block.
4. The block enters the free pool.
5. DWLC includes the block in subsequent candidate rankings.

## 7. Verification requirements

Design Verification must verify the following behaviors before DWLC integration sign-off:

- [ ] In dynamic mode, candidate blocks are ranked in ascending order of erase count.
- [ ] Static wear-leveling relocation starts when the erase-count spread exceeds `DWLC_STATIC_THRESHOLD`.
- [ ] Static wear-leveling relocation doesn't start when the erase-count spread is equal to or below `DWLC_STATIC_THRESHOLD`.
- [ ] `DWLC_MAX_ERASE_COUNT` reports the highest erase count among tracked blocks.
- [ ] `DWLC_RELOCATION_COUNT` increments correctly for static wear-leveling relocations.
- [ ] `DWLC_RELOCATION_COUNT` resets according to the defined reset behavior.
- [ ] DWLC ranking remains correct while GC adds reclaimed blocks to the free pool.
- [ ] Clearing `DWLC_CTRL` bit 0 disables DWLC ranking and restores FTL-default block selection.
- [ ] Enabling and disabling static wear leveling through `DWLC_STATIC_EN` produces the expected behavior.

## 8. Firmware integration

The FTL must treat the DWLC candidate list as a recommendation. The FTL retains final block-selection authority and can deprioritize a candidate when other selection criteria apply.

Firmware is responsible for:

- Reading `DWLC_MAX_ERASE_COUNT` periodically for device health and endurance telemetry.
- Configuring `DWLC_STATIC_THRESHOLD` during initialization when the platform requires a value other than the default.
- Monitoring `DWLC_RELOCATION_COUNT` to identify unusually frequent static wear-leveling activity.
- Applying FTL-specific block-selection criteria after receiving the DWLC candidate list.
- Maintaining existing bad-block handling independently of DWLC ranking.

A high relocation count can indicate a workload that causes significant wear-leveling activity. Firmware should use this value as a diagnostic metric rather than as a direct indication of device failure.

## 9. Glossary

**Erase count**
The number of erase operations recorded for a physical NAND block.

**Flash Translation Layer (FTL)**
The firmware layer that maps host logical addresses to physical NAND locations and manages functions such as block allocation, garbage collection, wear leveling, and bad-block handling.

**Garbage collection (GC)**
The process of reclaiming blocks that contain invalid data. GC relocates valid pages, erases the block, and returns it to the free pool.

**Program/erase (P/E) cycle**
A program and erase operation sequence applied to a NAND block. P/E cycles contribute to NAND cell wear.

**Wear leveling**
A technique for distributing erase activity across physical NAND blocks to reduce uneven wear.

---

*This document is an original writing sample. It doesn't describe or disclose any real product, architecture, or confidential information.*

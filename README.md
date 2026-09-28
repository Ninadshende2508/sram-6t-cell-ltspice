# sram-6t-cell-ltspice
6T SRAM cell designed and simulated in LTspice. Full transistor-level schematic with cross-coupled inverters and access transistors. Verified hold and write operations through transient simulation.

# 6T SRAM Cell — LTspice Simulation

## Project Overview

A complete transistor-level 6T SRAM (Static Random-Access Memory) cell designed and simulated in LTspice. This is the same fundamental circuit used for cache memory in modern processors, including ARM-based systems.

The project demonstrates the core operations of an SRAM bitcell: **hold** and **write**.

## Cell Architecture

The 6T SRAM cell consists of:

| Component | Role |
| :--- | :--- |
| **2 PMOS pull-up transistors** (M5, M6) | Pull the storage nodes high when needed |
| **2 NMOS pull-down transistors** (M1, M2) | Pull the storage nodes low when needed |
| **2 NMOS access transistors** (M3, M4) | Connect the cell to the bit lines during read/write |
| **2 × 10fF capacitors** (C1, C2) | Model the parasitic node capacitance |

**Core structure**: Two cross-coupled inverters (M5+M1 and M6+M2) form a bistable latch that stores one bit. The access transistors (M3, M4) are controlled by the word line (WL).

## Signal Definitions

| Signal | Description |
| :--- | :--- |
| **VDD** | 3.3V supply |
| **VSS** | Ground |
| **WL (WORD)** | Word line — enables access transistors |
| **BL (LEFT)** | Bit line — carries data into/out of the cell |
| **BLB (RIGHT)** | Bit line bar — complementary data line |
| **Q, QB** | Internal storage nodes (complementary) |

## Simulation Setup

### Transistor Models

.model MY_NMOS NMOS (LEVEL=49 VTO=0.4 KP=120u)
.model MY_PMOS1 PMOS (LEVEL=49 VTO=-0.4 KP=60u)
.model MY_PMOS2 PMOS (LEVEL=49 VTO=-0.4 KP=60u)


### Pulse Sources
| Source | Pulse Function |
| :--- | :--- |
| **VDD** | DC 3.3V |
| **WL** | `PULSE(0 3.3 22.5u 1n 1n 5u 20u 100)` |
| **BL** | `PULSE(0 3.3 0 1p 1p 1u 20u)` |
| **BLB** | Complementary pulse |

### Simulation Command
.tran 0 50u 0 10n


## Results

The transient simulation verifies:

### Hold Operation
When the word line is low, the access transistors are OFF. The cross-coupled inverters reinforce each other, holding the stored bit indefinitely.

### Write Operation
When the word line pulses high at **22.5µs**, the access transistors turn ON. The bit lines drive the storage nodes, forcing the cell to flip state. The waveform shows:
- **Q** transitions from 3.3V to 0V
- **QB** transitions from 0V to 3.3V

After the write pulse ends, the cell holds the new state until the next word line pulse.

### Waveform Evidence
The simulation output shows the two storage nodes operating in perfect complement:
- When one node is high (3.3V), the other is low (0V)
- The nodes flip cleanly at the word line pulse
- The cell retains its state between pulses

## Files Included

- `sram_6t_cell.asc` — LTspice schematic file
- `Schematic.png` — Full schematic view
- `waveforms.png` — Transient simulation output
- `tiled_view.png` — Combined schematic and waveform view

## Tools Used

- **LTspice** (Analog Devices) — SPICE simulator
- **LEVEL=49 MOSFET models** — BSIM3 short-channel models

## Debugging Notes

During development, the following issues were resolved:

1. **Floating gates**: The initial schematic had floating nodes. Fixed by ensuring all transistor terminals were properly connected.
2. **Convergence issues**: The original LEVEL=1 models caused simulation convergence failures. Replaced with LEVEL=49 models.
3. **Wrong simulation time domain**: The simulation was stuck in picoseconds. Fixed by properly placing the `.tran` command and ensuring it was not overridden by a DC operating point analysis.
4. **Clock period bug**: The word line pulse period was corrected to ensure write operations occurred within the simulation window.

## What I Learned

- Cross-coupled inverter operation and bistable storage
- SRAM cell write and hold operations
- LTspice transient simulation setup and debugging
- SPICE convergence issues and how to fix them
- The trade-offs between read stability and writeability in SRAM design

## Author

Ninad Shende
MSc Electronics and Electrical Engineering, University of Glasgow

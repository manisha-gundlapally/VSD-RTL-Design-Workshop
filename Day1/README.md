# Day 1 – Introduction to Verilog RTL Design & Logic Synthesis

This document covers the fundamentals of the digital design flow: Verilog simulation with Icarus Verilog, RTL-to-gate-level synthesis with Yosys, the SKY130 standard cell library, and the trade-offs between gate flavors.

---

## Table of Contents

- [Overview](#overview)
- [Learning Objectives](#learning-objectives)
- [1. Understanding Simulation](#1-understanding-simulation)
- [2. Icarus Verilog Simulation Flow](#2-icarus-verilog-simulation-flow)
- [3. Introduction to Yosys](#3-introduction-to-yosys)
- [4. RTL to Gate-Level Conversion](#4-rtl-to-gate-level-conversion)
- [5. Standard Cell Library](#5-standard-cell-library)
- [6. Why Different Gate Flavors are Required](#6-why-different-gate-flavors-are-required)
- [7. Yosys Synthesis Flow](#7-yosys-synthesis-flow)
- [8. Commands Executed](#8-commands-executed)
- [Key Takeaways](#key-takeaways)

---

## Overview

Day 1 introduced the complete digital design flow, starting from Verilog simulation and progressing to logic synthesis. The session covered the purpose of simulation, the role of a testbench, and how RTL designs are synthesized into gate-level implementations using **Yosys** and the **SKY130** standard cell library.

Hands-on labs were performed using **Icarus Verilog** for simulation and **Yosys** for synthesis.

---

## Learning Objectives

- Introduction to the digital design flow
- Understanding the role of a simulator
- Difference between Design and Testbench
- Icarus Verilog simulation flow
- Basics of Yosys synthesis
- Standard cell library concepts
- Importance of different gate flavors
- Hierarchical and flat synthesis
- Basic synthesis commands

---

## 1. Understanding Simulation

Before implementing any hardware, the functionality of an RTL design must be verified. Simulation checks whether the design behaves according to specification **before** it is synthesized. Three components are central to this process:

| Component | Description |
|---|---|
| **Simulator** | Executes the Verilog design together with the testbench and produces outputs for different input combinations, verifying correctness before hardware implementation. |
| **Design** | The RTL module written in Verilog that describes the intended hardware functionality. |
| **Testbench** | A separate Verilog module that generates input patterns for the design and observes the corresponding outputs. Used only for verification — it is **not** synthesized into hardware. |

---

## 2. Icarus Verilog Simulation Flow

The complete simulation flow using **Icarus Verilog (iverilog)** consists of:

1. Writing the RTL design.
2. Writing the testbench.
3. Compiling both files using Icarus Verilog.
4. Running the generated executable.
5. Viewing the generated waveform using **GTKWave**.

This process verifies whether the RTL behaves as expected before moving to synthesis.

---

## 3. Introduction to Yosys

**Yosys** is an open-source RTL synthesis tool. Unlike simulation, which verifies functionality, synthesis converts the RTL description into a **gate-level implementation** using cells available in a standard cell library.

The synthesized netlist performs the same functionality as the original RTL but is represented using logic gates that can actually be implemented in hardware.

---

## 4. RTL to Gate-Level Conversion

| RTL | Synthesis |
|---|---|
| Describes the **behavior** of the circuit | Transforms behavior into interconnected **logic gates and flip-flops** |

Although the internal implementation changes after synthesis, functionality remains identical — which is why the **same testbench** can verify both the RTL design and the synthesized netlist.

---

## 5. Standard Cell Library

Synthesis requires a **standard cell library**, which contains the logic gates used to build the final hardware implementation.

The workshop used the **SKY130 HD Standard Cell Library**, which provides multiple implementations of common gates (AND, OR, NAND, NOR, XOR) and flip-flops. Each gate is available in several versions with different timing, power, and area characteristics.

---

## 6. Why Different Gate Flavors are Required

Different variants ("flavors") of the same logic gate let the synthesis tool optimize the circuit for specific design requirements.

### Faster Cells

Lower propagation delay — selected for timing-critical paths requiring higher operating frequency.

| Advantages | Trade-offs |
|---|---|
| Lower delay | Higher power consumption |
| Higher operating frequency | Larger silicon area |
| Better timing performance | |

### Slower Cells

Consume less power and occupy smaller area, at the cost of higher delay. Useful on non-timing-critical paths, and helpful for avoiding **hold-time violations**.

| Advantages |
|---|
| Lower power consumption |
| Reduced chip area |
| Helps satisfy hold-time requirements |

### Cell Selection

The synthesis tool automatically selects appropriate cells based on timing constraints, power optimization, and area requirements. Balancing fast and slow cells produces an optimized hardware implementation.

---

## 7. Yosys Synthesis Flow

The hands-on lab performed the following synthesis steps:

1. Load the standard cell library.
2. Read the Verilog RTL design.
3. Specify the top-level module.
4. Perform synthesis.
5. Generate the synthesized netlist.
6. Flatten the design hierarchy.
7. Export the flattened netlist.

This demonstrated how RTL descriptions are translated into structural gate-level hardware.

---

## 8. Commands Executed

```bash
yosys

read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

read_verilog ../verilog_files/multiple_modules.v

synth -top multiple_modules

write_verilog multiple_modules_hier.v

flatten

write_verilog multiple_modules_flat.v
```

---

## Key Takeaways

- Simulation verifies RTL functionality **before** synthesis, using a simulator, a design, and a testbench.
- A testbench generates stimulus and checks outputs but is never synthesized into hardware.
- The Icarus Verilog flow: write RTL → write testbench → compile → run → view waveform in GTKWave.
- Yosys converts RTL behavior into a gate-level netlist using cells from a standard cell library.
- The same testbench can validate both RTL and the synthesized netlist, since functionality is preserved.
- The SKY130 HD library provides multiple gate flavors trading off delay, power, and area.
- Fast cells minimize delay at the cost of power/area; slow cells save power/area and help meet hold-time requirements.
- Yosys synthesis flow: load library → read RTL → set top module → synthesize → write netlist → flatten → write flattened netlist.

### Day 1 Checklist

- [ ] Write and simulate RTL with Icarus Verilog before synthesis.
- [ ] Keep testbench code separate from synthesizable RTL.
- [ ] View waveforms in GTKWave to confirm expected behavior.
- [ ] Load the correct `.lib` file before running synthesis in Yosys.
- [ ] Specify the correct top-level module with `synth -top`.
- [ ] Generate both hierarchical and flattened netlists for comparison.

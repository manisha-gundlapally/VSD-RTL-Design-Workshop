Day 1 – Introduction to Verilog RTL Design & Logic Synthesis

## Overview

The first day of the RTL Design and Synthesis Workshop introduced the complete digital design flow, starting from Verilog simulation and progressing to logic synthesis. The session focused on understanding the purpose of simulation, the role of a testbench, and how RTL designs are synthesized into gate-level implementations using Yosys and the SKY130 standard cell library.

Apart from the theoretical concepts, we also performed hands-on labs using **Icarus Verilog** for simulation and **Yosys** for synthesis.

---

# Learning Objectives

During this session, the following concepts were covered:

* Introduction to digital design flow
* Understanding the role of a simulator
* Difference between Design and Testbench
* Icarus Verilog simulation flow
* Basics of Yosys synthesis
* Standard Cell Library
* Importance of different gate flavors
* Hierarchical and Flat synthesis
* Basic synthesis commands

---

# 1. Understanding Simulation

Before implementing any hardware, the functionality of the RTL design must be verified. Simulation allows us to check whether the design behaves according to the specification before it is synthesized.

Three important components were introduced.

### Simulator

A simulator executes the Verilog design together with the testbench and produces the output for different input combinations. This helps verify the correctness of the design before hardware implementation.

### Design

The design is the RTL module written in Verilog that describes the intended hardware functionality.

### Testbench

A testbench is a separate Verilog module that generates different input patterns for the design and observes the corresponding outputs. It is used only for verification and is not synthesized into hardware.

---

# 2. Icarus Verilog Simulation Flow

The workshop demonstrated the complete simulation flow using **Icarus Verilog (iverilog)**.

The simulation process consists of:

1. Writing the RTL design.
2. Writing the testbench.
3. Compiling both files using Icarus Verilog.
4. Running the generated executable.
5. Viewing the generated waveform using GTKWave.

This process helps verify whether the RTL behaves as expected before moving to synthesis.

---

# 3. Introduction to Yosys

After understanding simulation, the workshop introduced **Yosys**, an open-source RTL synthesis tool.

Unlike simulation, which verifies functionality, synthesis converts the RTL description into a gate-level implementation using cells available in a standard cell library.

The synthesized netlist performs the same functionality as the original RTL but is represented using logic gates that can be implemented on hardware.

---

# 4. RTL to Gate-Level Conversion

One important concept discussed during the session was the difference between RTL and synthesized hardware.

* **RTL** describes the behavior of the circuit.
* **Synthesis** transforms that behavioral description into interconnected logic gates and flip-flops.

Although the internal implementation changes after synthesis, the functionality remains identical. Therefore, the same testbench can be used to verify both the RTL design and the synthesized netlist.

---

# 5. Standard Cell Library

The synthesis process requires a **standard cell library**, which contains the logic gates used to build the final hardware implementation.

The workshop used the **SKY130 HD Standard Cell Library**, which provides different implementations of common logic gates such as AND, OR, NAND, NOR, XOR, and Flip-Flops.

Each gate is available in multiple versions with different timing, power, and area characteristics.

---

# 6. Why Different Gate Flavors are Required

One of the important discussions in the session was the availability of multiple versions (or **flavors**) of the same logic gate.

Different gate variants help the synthesis tool optimize the circuit depending on the design requirements.

### Faster Cells

Fast cells have lower propagation delay and are selected for timing-critical paths where higher operating frequency is required.

**Advantages**

* Lower delay
* Higher operating frequency
* Better timing performance

**Trade-offs**

* Higher power consumption
* Larger silicon area

---

### Slower Cells

Slow cells consume less power and occupy smaller area but introduce higher delay.

They are useful in paths where timing is not critical and also help avoid hold-time violations.

**Advantages**

* Lower power consumption
* Reduced chip area
* Helps satisfy hold-time requirements

---

### Cell Selection

The synthesis tool automatically selects appropriate cells based on timing constraints, power optimization, and area requirements. Choosing the right combination of fast and slow cells results in an optimized hardware implementation.

---

# 7. Yosys Synthesis Flow

During the hands-on lab, the following synthesis steps were performed:

* Load the standard cell library.
* Read the Verilog RTL design.
* Specify the top-level module.
* Perform synthesis.
* Generate the synthesized netlist.
* Flatten the design hierarchy.
* Export the flattened netlist.

The synthesis process demonstrated how RTL descriptions are translated into structural gate-level hardware.

---

# 8. Commands Executed

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

# 9. What I Learned

By the end of Day 1, I understood:

* The purpose of simulation in digital design.
* The difference between a simulator, design, and testbench.
* The complete Icarus Verilog simulation flow.
* The role of Yosys in RTL synthesis.
* How RTL is converted into a gate-level netlist.
* Why standard cell libraries are required.
* Why different gate flavors exist.
* The importance of fast and slow cells in timing optimization.
* The basic synthesis workflow using Yosys.

---

# Conclusion

Day 1 established the foundation for the RTL Design and Synthesis Workshop by covering both simulation and synthesis. Through theoretical discussions and practical labs, I gained an understanding of how Verilog designs are verified using simulation and later synthesized into hardware using the SKY130 standard cell library. This session provided the essential knowledge required for the upcoming RTL design and synthesis exercises.


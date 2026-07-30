# Day 3 – Combinational and Sequential Logic Optimization

This document covers the optimization techniques applied during logic synthesis — for both combinational and sequential circuits — along with the corresponding lab exercises performed in Yosys.

---

## Table of Contents

- [Introduction](#introduction)
- [Combinational Logic Optimization](#combinational-logic-optimization)
- [Sequential Logic Optimization](#sequential-logic-optimization)
- [Constant Propagation](#constant-propagation)
- [Unused Output Optimization](#unused-output-optimization)
- [State Optimization](#state-optimization)
- [Logic Cloning](#logic-cloning)
- [Retiming](#retiming)
- [Optimization Passes Performed in Yosys](#optimization-passes-performed-in-yosys)
- [Laboratory Exercises](#laboratory-exercises)
- [Key Learning Outcomes](#key-learning-outcomes)

---

## Introduction

Digital circuit optimization is an important stage of the synthesis process. After converting RTL into logic gates, the synthesis tool analyzes the circuit to remove unnecessary logic, simplify Boolean expressions, and generate an implementation that consumes less area while preserving the required functionality. This session explored the optimization techniques Yosys applies to both combinational and sequential circuits.

---

## Combinational Logic Optimization

Combinational optimization reduces unnecessary logic **without changing circuit functionality**. The synthesis tool analyzes Boolean expressions and removes redundant hardware, producing a smaller, more efficient gate-level implementation.

**Objectives:**
- Reduce the number of logic gates.
- Simplify Boolean expressions.
- Minimize chip area.
- Improve circuit speed.
- Reduce power consumption.

---

## Sequential Logic Optimization

Sequential optimization applies to circuits containing memory elements such as flip-flops. Unlike combinational optimization, the synthesis tool must **preserve the behavior of sequential elements** while removing unnecessary registers and simplifying the logic connected to them.

**Typical goals:**
- Removing redundant flip-flops.
- Propagating constant values through sequential logic.
- Eliminating unreachable logic.
- Improving timing while maintaining functional equivalence.

---

## Constant Propagation

Constant propagation replaces signals that always carry a fixed logic value **directly with that constant** during synthesis. Instead of implementing logic to compute an already-known value, the synthesis tool substitutes the constant and removes redundant gates.

**Advantages:**
- Reduces logic complexity.
- Decreases hardware utilization.
- Improves timing.
- Lowers power consumption.

---

## Unused Output Optimization

If a signal or output is never used by the remaining circuit, the synthesis tool recognizes it has no effect on final functionality and **automatically removes it** during optimization — reducing total gate count and preventing unnecessary hardware from being implemented.

This demonstrates that synthesis tools generate hardware only for logic that actually contributes to the final outputs.

---

## State Optimization

Finite State Machines (FSMs) can contain equivalent or unnecessary states. During optimization, these states may be **merged or removed**, reducing required hardware while preserving original behavior.

**State optimization generally includes:**
- Eliminating equivalent states.
- Efficient state encoding.
- Simplifying next-state logic.
- Reducing overall hardware complexity.

---

## Logic Cloning

Logic cloning is a performance optimization where selected logic cells are **duplicated** to reduce fan-out and improve timing. Instead of one gate driving many loads, additional copies are created so each copy drives fewer destinations — reducing delay on critical timing paths.

---

## Retiming

Retiming is a sequential optimization technique where flip-flops are **repositioned across combinational logic** without changing circuit functionality. Its purpose is to balance propagation delays between pipeline stages and improve maximum operating frequency.

Unlike other optimizations, retiming modifies only **register placement** while preserving the logical behavior of the design.

---

## Optimization Passes Performed in Yosys

During synthesis, Yosys automatically performs several optimization passes to simplify the generated hardware:

| Optimization Pass | Purpose |
|---|---|
| Constant propagation | Replace known-constant signals directly |
| Dead logic elimination | Remove logic with no effect on outputs |
| Boolean simplification | Reduce Boolean expressions |
| Removal of unused wires | Strip unreferenced signals |
| Removal of unused cells | Strip unreferenced gates/cells |
| Expression simplification | Simplify equivalent expressions |
| Resource sharing | Reuse hardware across similar operations |

These optimizations collectively produce an efficient gate-level netlist.

---

## Laboratory Exercises

### Lab 1 – Constant Propagation
A simple combinational circuit was synthesized to observe how Yosys replaces constant values directly within the logic network. After optimization, unnecessary gates were removed, producing a simpler implementation.

### Lab 2 – Logic Simplification
A multiplexer-based design was synthesized to demonstrate how Boolean expressions simplify when one input remains constant. The synthesized circuit contained fewer logic gates while maintaining identical functionality.

### Lab 3 – Expression Optimization
Additional combinational logic examples were analyzed to observe how the synthesis tool recognizes equivalent expressions and minimizes redundant hardware.

### Lab 4 – Boolean Reduction
Nested conditional expressions were synthesized and optimized. Yosys simplified the resulting Boolean equation, removing unnecessary logic while preserving the expected output.

### Lab 5 – Sequential Optimization (D Flip-Flop)
A D flip-flop with an asynchronous reset and constant assignment was synthesized. Since the output eventually settled to a constant value, the synthesis tool simplified portions of the sequential logic.

### Lab 6 – Constant Register Optimization
A flip-flop whose output always remained at logic `1` was synthesized. Since the register never changed state, Yosys optimized the circuit by removing unnecessary sequential elements and replacing them with constant logic wherever applicable.

| Lab | Focus | Key Result |
|---|---|---|
| 1 | Constant propagation | Redundant gates removed via constant substitution |
| 2 | Logic simplification | MUX simplified when one input held constant |
| 3 | Expression optimization | Equivalent expressions merged/minimized |
| 4 | Boolean reduction | Nested conditionals reduced to simpler Boolean logic |
| 5 | Sequential optimization | D-FF with async reset simplified to constant-driven logic |
| 6 | Constant register optimization | Always-`1` flip-flop replaced with constant logic |

---

## Key Learning Outcomes

- Understood the difference between combinational and sequential optimization.
- Learned how constant propagation simplifies digital circuits.
- Observed removal of unused outputs and redundant logic during synthesis.
- Explored optimization techniques such as state optimization, cloning, and retiming.
- Analyzed how Yosys automatically performs multiple optimization passes to generate an efficient gate-level implementation.
- Verified optimization results using synthesized netlists and schematic visualization.

### Day 3 Checklist

- [ ] Check for constant-valued signals that synthesis can propagate and simplify.
- [ ] Confirm unused outputs/signals are eliminated from the final netlist.
- [ ] Review FSM designs for equivalent or unreachable states before synthesis.
- [ ] Identify high fan-out nets that may benefit from logic cloning.
- [ ] Consider retiming for pipeline stages with unbalanced propagation delays.
- [ ] Compare pre- and post-optimization netlists/schematics to confirm functional equivalence.

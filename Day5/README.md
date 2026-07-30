RTL Coding Styles: IF-ELSE and CASE Statements
Correct RTL coding practices are essential for ensuring that the synthesized hardware behaves exactly as intended. Improper coding styles can introduce unintended hardware elements such as latches, leading to simulation-synthesis mismatches and unreliable circuit behavior. This section discusses common coding practices, inferred latches, and precautions while using if-else and case statements.
________________________________________
Priority Logic using IF-ELSE Statements
The if-else construct implements priority logic. Conditions are evaluated sequentially from top to bottom, and once a condition evaluates to true, the corresponding block is executed while the remaining conditions are ignored.
if(condition1)
    statement1;
else if(condition2)
    statement2;
else
    statement3;
The priority order is:
•	if → Highest Priority
•	else if → Evaluated only if previous conditions are false
•	else → Executed when none of the above conditions are satisfied
This coding style is commonly used whenever one condition must have higher priority than another.
________________________________________
Inferred Latches
A latch is a level-sensitive storage element that retains its previous value until a new value is assigned. During synthesis, if the RTL description does not specify the output for every possible input condition, the synthesis tool automatically introduces a latch to preserve the previous output value. Since this latch is not explicitly written by the designer, it is called an inferred latch.
In combinational logic, outputs should depend only on the current input values. Therefore, unintentionally creating latches is considered poor coding practice because it introduces unwanted memory into the circuit.
________________________________________
Incomplete IF Statements
Consider the following combinational logic:
always @(*)
begin
    if(sel)
        y = a;
    else if(enable)
        y = b;
end

When both sel and enable are false, no assignment is made to y. Since the output must still have a valid value, the synthesis tool infers a latch to retain the previous value of y.
The recommended coding style is to ensure that every possible execution path assigns a value to the output.
always @(*)
begin
    if(sel)
        y = a;
    else if(enable)
        y = b;
    else
        y = 0;
end
This eliminates the need for a latch and results in purely combinational hardware.

________________________________________
Why the Counter Does Not Infer a Latch
The workshop also demonstrated a counter implemented using a clocked always block.
always @(posedge clk or posedge reset)
begin
    if(reset)
        count <= 0;
    else if(enable)
        count <= count + 1;
end
Although there is no final else statement, this implementation does not infer a latch.
The reason is that the circuit is sequential, not combinational. The output count is stored in a flip-flop, which naturally retains its previous value when the enable signal is inactive. Therefore, when enable is low, the counter simply holds its previous count value until the next clock edge. This storage behavior is the intended operation of a flip-flop and does not require an inferred latch.
________________________________________
CASE Statements
A case statement selects one execution path based on the value of a single expression. Unlike an if-else ladder, a case statement does not represent priority logic. Instead, the input expression is compared against each case item, and only the matching branch is executed.
always @(*)
begin
    case(sel)
        2'b00 : y = a;
        2'b01 : y = b;
        default : y = 0;
    endcase
end
Case statements provide a cleaner and more readable implementation for decoders, multiplexers, state machines, and other selection logic.
________________________________________
Incomplete CASE Statements
If one or more input combinations are not covered and no default branch is provided, the output may remain unassigned for certain conditions.
always @(*)
begin
    case(sel)
        2'b00 : y = a;
        2'b01 : y = b;
    endcase
end
If sel becomes 2'b10 or 2'b11, no assignment is made to y. To preserve the previous output, the synthesis tool infers a latch.
Adding a default branch ensures that every possible input condition produces a valid output.
always @(*)
begin
    case(sel)
        2'b00 : y = a;
        2'b01 : y = b;
        default : y = 0;
    endcase
end
________________________________________
CASE Statement Caveat
When multiple output signals are assigned inside a case statement, every output must be assigned in every branch.
Incorrect Example
case(sel)
    2'b00 :
    begin
        x = a;
        y = b;
    end

    2'b01 :
    begin
        x = c;
    end

    default :
    begin
        x = d;
        y = e;
    end
endcase
In the 2'b01 branch, only x is assigned while y remains unassigned. Consequently, the synthesis tool infers a latch for y so that it can retain its previous value.
The recommended practice is to assign every output signal in every branch of the case statement.
________________________________________
IF-ELSE vs CASE Statements
IF-ELSE	CASE
Implements priority logic	Implements selection logic
Conditions are evaluated sequentially	Input value is matched against case items
First true condition is executed	Only the matching case branch is executed
Suitable for priority encoders and conditional logic	Suitable for multiplexers, decoders, and finite state machines
________________________________________
RTL Coding Guidelines
•	Use always @(*) for combinational logic.
•	Assign every output signal in all possible execution paths.
•	Always include an else branch when using combinational if statements.
•	Always include a default branch in case statements.
•	Assign all output variables in every case branch.
•	Use non-blocking assignments (<=) inside clocked sequential logic.
•	Avoid unintended inferred latches in combinational circuits.
________________________________________
Key Takeaways
•	IF-ELSE statements implement priority-based decision making.
•	CASE statements provide a structured method for implementing selection logic.
•	Missing assignments in combinational logic lead to inferred latches.
•	Flip-flops intentionally store data in sequential circuits, whereas inferred latches are usually unintended in combinational designs.
•	Following proper RTL coding practices ensures that RTL simulation, synthesis, and gate-level simulation produce consistent and predictable results.
________________________________________
This format is GitHub-friendly, uses clear headings, explanations, examples, and key takeaways, making it ideal for your workshop documentation while remaining original and not copied from existing material.
Yes! These two labs were specifically designed to demonstrate how incomplete conditional statements infer latches. Here's a professional GitHub README section you can add.
________________________________________
Lab 1: Incomplete IF Statement (incomp_if1.v)
This experiment demonstrated how an incomplete if statement in combinational logic can unintentionally infer a latch during synthesis.
RTL Code
always @(*)
begin
    if(i0)
        y = i1;
end
In this implementation, the output y is assigned only when i0 is logic high. However, when i0 becomes logic low, there is no assignment for y. Since the output must always have a valid value, the synthesis tool preserves the previous value of y by automatically introducing a D-latch.
As a result, the output no longer depends solely on the current inputs but also on its previous state. This changes the behavior from combinational logic to sequential logic, which is generally undesirable unless a latch is intentionally required.
Observation
•	i0 = 1 → y follows i1
•	i0 = 0 → No assignment to y
•	Previous value of y is retained
•	Synthesis infers a D-latch
Learning Outcome
Every output signal in a combinational always @(*) block must be assigned for all possible input conditions. Missing assignments result in inferred latches.

________________________________________
Lab 2: Incomplete IF-ELSE Statement (incomp_if2.v)
This experiment extended the previous example by using an if-else if ladder.
RTL Code
always @(*)
begin
    if(i0)
        y = i1;
    else if(i2)
        y = i3;
end
Here, the output y is assigned only when either i0 or i2 is asserted. If both i0 and i2 are low, neither condition is satisfied, and no value is assigned to y.
To maintain a valid output, the synthesis tool again introduces a D-latch, allowing y to retain its previous value until one of the conditions becomes true.
Observation
•	i0 = 1 → y = i1
•	i0 = 0 and i2 = 1 → y = i3
•	i0 = 0 and i2 = 0 → No assignment to y
•	Previous value of y is retained
•	Synthesis infers a D-latch
Learning Outcome
Adding an else if alone is not sufficient to prevent latch inference. Every possible execution path must assign a value to the output. The recommended implementation is to include a final else statement so that y receives a valid value for every input combination.
________________________________________
Key Takeaways
•	Incomplete if statements lead to inferred latches.
•	Incomplete if-else if ladders also infer latches if no final else branch is provided.
•	A combinational circuit should always assign outputs for every possible input condition.
•	If an output is left unassigned, the synthesis tool inserts a D-latch to preserve the previous value.
•	Proper RTL coding practices help avoid unintended storage elements and ensure predictable hardware behavior.
This explanation is suitable for a GitHub README and clearly explains what was implemented, what was observed, why a D-latch was inferred, and what was learned from the lab, rather than simply describing the code.
Absolutely. Below is a GitHub README-style documentation for these labs. It is written in a professional manner, explains the concepts, and is not copied from the workshop material.
________________________________________
Lab 3: Incomplete CASE Statement (incomp_case.v)
This experiment demonstrated how an incomplete case statement can unintentionally infer a latch in combinational logic.
RTL Code
always @(*)
begin
    case(sel)
        2'b00 : y = i0;
        2'b01 : y = i1;
    endcase
end
In this implementation, the output y is assigned only for the input combinations 2'b00 and 2'b01. The remaining combinations (2'b10 and 2'b11) are not handled, and no default branch is provided.
When the select signal takes one of these uncovered values, the output y is left unassigned. Since the synthesis tool must maintain a valid output value, it automatically retains the previous value of y by inferring a D-latch.
Observation
Select (sel)	Output
2'b00	y = i0
2'b01	y = i1
2'b10	Previous value retained (Latch)
2'b11	Previous value retained (Latch)
Learning Outcome
An incomplete case statement causes inferred latches because not all input combinations assign a value to the output.
________________________________________
Lab 4: Complete CASE Statement (comp_case.v)
To eliminate the inferred latch, the missing conditions are handled using a default branch.
RTL Code
always @(*)
begin
    case(sel)
        2'b00 : y = i0;
        2'b01 : y = i1;
        default : y = i2;
    endcase
end
The default statement guarantees that the output receives a valid assignment whenever none of the specified case items match.
Since every possible value of sel now assigns a value to y, the synthesis tool no longer needs to preserve the previous output value. As a result, no latch is inferred, and the logic remains purely combinational.
Observation
Select (sel)	Output
2'b00	y = i0
2'b01	y = i1
2'b10	y = i2
2'b11	y = i2
Learning Outcome
Including a default branch ensures that all possible input combinations are covered, preventing latch inference and maintaining combinational behavior.
________________________________________
Lab 5: Partial Output Assignment in CASE Statement
This experiment demonstrated another common RTL coding mistake where multiple outputs are declared, but not all outputs are assigned in every branch of the case statement.
RTL Code
always @(*)
begin
    case(sel)

        2'b00:
        begin
            y = i0;
            x = i2;
        end

        2'b01:
        begin
            y = i1;
        end

        default:
        begin
            y = i3;
            x = i4;
        end

    endcase
end
In this implementation, signal x is assigned in the 2'b00 branch and in the default branch. However, when sel = 2'b01, only y receives a value, while x is left unassigned.
Since x must always have a valid value, the synthesis tool retains its previous value by inferring a latch.
Observation
Select (sel)	y	x
2'b00	i0	i2
2'b01	i1	Previous Value (Latch)
2'b10	Default	Default
2'b11	Default	Default
Learning Outcome
When multiple outputs are used inside a case statement, every output signal must be assigned in every branch. Leaving even one output unassigned results in an inferred latch for that signal.
________________________________________
Redundancy Optimization During Synthesis
The workshop also introduced the concept of Boolean logic optimization performed by the synthesis tool.
For example, an expression such as
S1'·S0' + S1
can be simplified using the Redundancy Theorem to
S1 + S0'
During synthesis, Yosys automatically performs such Boolean simplifications to reduce the number of logic gates while preserving the functionality of the circuit.
The optimized logic requires fewer hardware resources, occupies less silicon area, and may improve timing and power consumption. This optimization is one of the key advantages of the synthesis process, where the generated gate-level netlist is functionally equivalent to the RTL but implemented using a more efficient hardware structure.
________________________________________
Key Takeaways
•	Every possible input combination must be handled in a case statement.
•	Always include a default branch when writing combinational case statements.
•	Every output signal should be assigned in every branch of the case statement.
•	Missing assignments cause the synthesis tool to infer D-latches.
•	Synthesis tools automatically apply Boolean algebra optimizations, such as the Redundancy Theorem, to generate a more efficient gate-level implementation.
•	Proper RTL coding practices help eliminate unintended storage elements and ensure that RTL simulation, synthesis, and gate-level simulation produce consistent results.
________________________________________
This version is GitHub-quality documentation—it explains what was implemented, what was observed, why the behavior occurs, and what was learned, which is exactly the style expected for a workshop repository. Yes. This is an important concept from the workshop. The issue here is overlapping case items, not inferred latches. Here's a GitHub-ready explanation.
________________________________________
Lab 6: Overlapping CASE Statements (bad_case.v)
This experiment demonstrated the problems caused by overlapping case items. Unlike incomplete if or case statements, overlapping cases do not infer latches. Instead, they introduce ambiguity because a single input combination can satisfy more than one case item.
RTL Code
always @(*)
begin
    casez(sel)
        2'b00 : y = i0;
        2'b01 : y = i1;
        2'b10 : y = i2;
        2'b1? : y = i3;
    endcase
end
Understanding the Overlap
The pattern 2'b1? represents both:
•	2'b10
•	2'b11
However, 2'b10 has already been explicitly defined in the previous case item.
This means the input 2'b10 matches two different case entries:
•	2'b10 → y = i2
•	2'b1? → y = i3
The simulator evaluates the first matching case item according to Verilog semantics, while the synthesis tool may optimize the hardware differently. As a result, RTL simulation and the synthesized hardware may not exhibit identical behavior.
Case Analysis
Select (sel)	Matching Case	Output
2'b00	2'b00	y = i0
2'b01	2'b01	y = i1
2'b10	2'b10 and 2'b1? (Overlap)	Ambiguous
2'b11	2'b1?	y = i3
Observation
•	No inferred latches are created because every possible input combination receives an output assignment.
•	The problem arises because one input (2'b10) satisfies multiple case items.
•	This overlap can lead to mismatches between RTL simulation and the synthesized gate-level implementation.
Learning Outcome
Case items should be mutually exclusive, meaning each input combination should match exactly one case branch. Overlapping patterns should be avoided because they can introduce ambiguity and cause simulation and synthesis results to differ.
________________________________________
Key Takeaways
•	Every input combination should match only one case item.
•	Avoid overlapping wildcard patterns such as ? or z unless they are carefully designed.
•	Overlapping cases do not infer latches.
•	Overlapping cases may produce simulation-synthesis mismatches, making the design unreliable.
•	Well-structured case statements improve readability, predictability, and hardware correctness.
This explanation matches the concept typically covered in the VSD RTL Design & Synthesis workshop: the focus is on why overlapping case statements are considered bad coding style and how they can lead to different behavior between RTL simulation and synthesized hardware, even though no latch is inferred.
Yes. Based on everything you explained, here is a GitHub README-style documentation. It is written in a professional way, explains the concepts rather than just the code, and is suitable for your VSD workshop repository.
________________________________________
Looping Constructs in Verilog
Verilog provides looping constructs that help simplify RTL coding and improve code readability. During the workshop, two different types of loops were discussed: procedural for loops and generate for loops. Although their syntax appears similar, they serve completely different purposes in hardware design.
________________________________________
1. Procedural for Loop
A procedural for loop is used inside an always block. It does not create multiple hardware instances. Instead, it repeatedly evaluates the same logic during simulation or synthesis, making the RTL description more compact.
Characteristics
•	Used only inside procedural blocks (always or initial).
•	Used for repetitive operations on signals.
•	Does not instantiate additional hardware modules.
•	Helps reduce repetitive coding for combinational and sequential logic.
________________________________________
2. Example: 2:1 Multiplexer
A simple 2:1 multiplexer can be implemented using a case statement.
always @(*) begin
    case(sel)
        1'b0 : y = i0;
        1'b1 : y = i1;
    endcase
end
Since there are only two input choices, the same functionality can be written more concisely using the conditional (ternary) operator.
assign y = sel ? i1 : i0;
The ternary operator provides a shorter and more readable implementation for simple selection logic.
Learning Outcome
•	Small multiplexers can be efficiently described using the conditional operator.
•	The generated hardware remains functionally identical.
________________________________________
3. Implementing a 4:1 Multiplexer Using a Procedural for Loop
For larger multiplexers, writing individual case statements becomes repetitive. Instead, a procedural for loop can be used to compare the select signal with every possible input index.
A loop iterates through all input positions, and when the loop index matches the select value, the corresponding input is assigned to the output.
Advantages
•	Eliminates repetitive code.
•	Makes the RTL easier to maintain.
•	Easily scalable to multiplexers with a larger number of inputs.
Learning Outcome
Procedural for loops provide a clean and scalable approach for implementing wide multiplexers without manually writing every selection case.
________________________________________
4. Using a Procedural for Loop for a Demultiplexer
The same looping concept can also be applied while designing a demultiplexer.
Instead of writing separate assignments for every output line, the output bus is first initialized, and then a loop activates only the selected output position.
Conceptually, the implementation performs the following steps:
•	Initialize all outputs to zero.
•	Iterate through every output line.
•	Compare the loop index with the select signal.
•	Assign the input value only to the selected output.
This approach significantly reduces code duplication, especially for wide buses.
Learning Outcome
Procedural loops simplify the implementation of wide decoders and demultiplexers while preserving identical hardware functionality.
________________________________________
Generate for Loop
Unlike a procedural for loop, a generate for loop is used to create multiple hardware instances during elaboration.
The loop executes only once during compilation and expands into several copies of the same hardware block.
Characteristics
•	Used outside always blocks.
•	Instantiates multiple hardware modules.
•	Useful when identical hardware blocks are repeated.
•	Synthesizes into physically replicated logic.
________________________________________
Example: Multiple AND Gates
Without a generate loop, each AND gate must be instantiated manually.
and u0 (...);
and u1 (...);
and u2 (...);
...
For designs containing many identical gates, this approach becomes lengthy and difficult to maintain.
Using a generate loop, the same hardware can be created automatically.
The generate loop repeatedly instantiates the AND gate for each bit of the input bus, resulting in a compact and scalable RTL description.
Advantages
•	Eliminates repetitive module instantiations.
•	Simplifies code maintenance.
•	Easily scalable to larger bus widths.
•	Generates identical hardware instances automatically.
________________________________________
Ripple Carry Adder Using Generate Loop
The workshop demonstrated the practical use of generate loops through the implementation of a Ripple Carry Adder.
A Ripple Carry Adder consists of several Full Adders connected in series.
Each Full Adder performs addition for one bit and passes its carry output to the next stage. Since every stage has an identical structure, manually instantiating each Full Adder becomes inefficient for larger adders.
Using a generate loop, multiple Full Adder modules are instantiated automatically, with each iteration creating one stage of the Ripple Carry Adder.
The carry output of one stage is connected to the carry input of the next stage, forming the complete adder structure.
Learning Outcome
Generate loops provide an elegant and scalable method for constructing repetitive hardware structures such as:
•	Ripple Carry Adders
•	Register Arrays
•	Shift Registers
•	Bitwise Logic Units
•	Memory Arrays
________________________________________
Procedural for Loop vs Generate for Loop
Feature	Procedural for Loop	Generate for Loop
Location	Inside always block	Outside always block
Purpose	Evaluates expressions	Creates hardware instances
Hardware Instantiation	No	Yes
Typical Applications	MUX, DEMUX, counters, combinational logic	Adders, register arrays, repeated modules
Execution	During procedural execution	During elaboration before synthesis
________________________________________
Key Takeaways
•	Verilog provides both procedural and generate looping constructs for different purposes.
•	A procedural for loop simplifies repetitive logic inside an always block without creating additional hardware.
•	A generate for loop instantiates multiple copies of identical hardware modules, making it ideal for scalable digital designs.
•	Simple multiplexers can be implemented using the ternary operator, while larger multiplexers and demultiplexers benefit from procedural loops.
•	Generate loops significantly reduce code duplication when designing repetitive hardware structures such as Ripple Carry Adders and wide combinational circuits.
________________________________________
This is suitable for your GitHub repository because it explains the concepts covered in the workshop, why each construct is used, and what you learned, instead of simply listing the Verilog code.
Perfect. Below is a GitHub README-style documentation for the labs you performed. It focuses on the practical implementation and observations rather than simply describing the code.
________________________________________
Lab 7: Multiplexer Using Procedural for Loop (mux_generate.v)
This experiment demonstrated the implementation of a multiplexer using a procedural for loop instead of explicitly writing multiple case statements. The objective was to show how repetitive selection logic can be implemented using an iterative approach while maintaining the same functionality.
The design iterates through all possible input indices and compares each index with the select signal. When the loop index matches the selected value, the corresponding input is assigned to the output.
Observation
•	The procedural for loop successfully selected the correct input based on the value of the select signal.
•	Functional simulation verified that the output matched the expected behavior of a multiplexer.
•	Logic synthesis generated hardware functionally equivalent to the conventional multiplexer implementation.
Learning Outcome
This experiment demonstrated that procedural for loops simplify RTL coding by eliminating repetitive statements while preserving the same synthesized hardware functionality.
________________________________________
Lab 8: Demultiplexer Using case Statement (demux_case.v)
In this experiment, a demultiplexer was implemented using a case statement. A single input signal was routed to one of several output lines based on the value of the select signal, while all remaining outputs remained inactive.
Each possible value of the select signal was explicitly handled within the case statement, providing a clear mapping between the input and the selected output.
Observation
•	Only one output line became active for each select value.
•	The remaining outputs retained their inactive state.
•	RTL simulation and synthesis produced the expected demultiplexer functionality.
Learning Outcome
The case statement provides a simple and readable implementation for demultiplexers when the number of outputs is relatively small.
________________________________________
Lab 9: Demultiplexer Using Procedural for Loop (demux_generate.v)
This experiment implemented the same demultiplexer functionality using a procedural for loop.
Instead of explicitly writing assignments for every output, the loop iterated through all output positions and compared each index with the select signal. The selected output received the input value, while the remaining outputs remained inactive.
Comparison with the Previous Lab
case Statement	Procedural for Loop
Individual cases written manually	Single loop handles all outputs
Less scalable	Easily scalable for larger bus widths
More repetitive	More compact and maintainable
Observation
•	The procedural loop produced the same functionality as the case-based implementation.
•	Simulation waveforms matched the expected demultiplexer behavior.
•	Synthesis generated equivalent hardware despite the different RTL coding style.
Learning Outcome
Procedural for loops improve code scalability and readability without affecting the synthesized hardware functionality.
________________________________________
Lab 10: Ripple Carry Adder Using Generate for Loop
The final experiment demonstrated the implementation of a Ripple Carry Adder (RCA) using the generate for loop.
Instead of manually instantiating multiple Full Adder modules, the generate construct was used to automatically create one Full Adder instance for each bit position. Each Full Adder receives the carry generated by the previous stage and passes its carry output to the next stage, forming the ripple carry structure.
Since every stage performs identical operations, the generate loop provides a compact and scalable method for hardware instantiation.
Observation
•	Multiple Full Adder instances were automatically generated during elaboration.
•	The carry propagated sequentially through each stage of the Ripple Carry Adder.
•	Functional simulation verified correct addition for different input combinations.
•	Logic synthesis successfully instantiated all Full Adders and generated the corresponding gate-level implementation.
Learning Outcome
The generate for loop is a powerful construct for creating repetitive hardware structures. It significantly reduces manual coding effort while improving scalability and maintainability. This approach is widely used in practical digital designs for implementing structures such as Ripple Carry Adders, register arrays, arithmetic units, and bus-oriented circuits.
________________________________________
Overall Summary
These experiments demonstrated two important Verilog looping constructs:
•	Procedural for loops simplify repetitive logic inside always blocks and are useful for implementing scalable multiplexers and demultiplexers without changing the synthesized hardware.
•	Generate for loops instantiate multiple copies of identical hardware modules, making them ideal for designing repetitive architectures such as Ripple Carry Adders.
By comparing different RTL coding styles and verifying both simulation and synthesis results, these labs reinforced the relationship between concise Verilog code and the hardware generated by synthesis tools.

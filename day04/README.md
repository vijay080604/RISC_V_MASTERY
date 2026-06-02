# Day 04 – Building a RISC-V CPU: Microarchitecture Foundations

## Overview

Day 04 marked the beginning of actual CPU design. During the previous sessions, I explored digital logic fundamentals, combinational circuits, sequential circuits, pipelining, and TL-Verilog concepts. However, understanding individual digital design concepts is only the first step.

The next challenge is understanding how these building blocks come together to form a processor capable of executing software instructions.

This session focused on studying the internal structure of a RISC-V processor and understanding the responsibilities of its major functional blocks.

---

# Learning Objectives

By the end of Day 04, I was able to:

* Understand the difference between an ISA and a Microarchitecture.
* Study the major building blocks of a RISC-V processor.
* Analyze how instructions travel through a CPU.
* Understand the role of the Program Counter.
* Investigate Instruction Memory organization.
* Visualize instruction execution inside the processor.
* Prepare for implementing Fetch and Decode stages.

---

# ISA vs Microarchitecture

One of the most important concepts introduced during this session was the distinction between an Instruction Set Architecture (ISA) and a Microarchitecture.

## Instruction Set Architecture (ISA)

The ISA defines the interface between software and hardware.

It specifies:

* Supported instructions
* Register definitions
* Instruction formats
* Memory access behavior
* Software-visible functionality

For this workshop, the target ISA is:

```text
RISC-V RV32I
```

Programs are written according to the ISA specification.

---

## Microarchitecture

The Microarchitecture defines how the processor internally implements the ISA.

It includes:

* Program Counter
* Instruction Memory
* Decode Logic
* Register File
* Arithmetic Logic Unit (ALU)
* Branch Logic
* Control Logic

Different processors can implement the same ISA while using completely different microarchitectures.

---

# Why Microarchitecture Matters

When writing software, we typically see instructions such as:

```assembly
add x5, x1, x2
```

The processor, however, must perform several internal operations before this instruction can be executed:

1. Fetch the instruction from memory.
2. Decode the instruction fields.
3. Read source registers.
4. Perform arithmetic computation.
5. Write the result back.

Microarchitecture defines how these operations are implemented in hardware.

---

# High-Level CPU Organization

A processor is composed of multiple functional blocks working together.

### Practical Reference

![CPU Output](./images04/day4_final_output.png)

### Analysis

The visualization demonstrates how different processor blocks cooperate during instruction execution.

A typical instruction follows this path:

```text
Program Counter
        ↓
Instruction Memory
        ↓
Instruction Decode
        ↓
Register File
        ↓
ALU
        ↓
Write Back
```

Although software developers see a single instruction, internally the processor performs multiple coordinated operations.

---

# Understanding the Program Counter

The Program Counter (PC) is one of the most important components inside a processor.

Its primary responsibility is to store the address of the instruction currently being executed.

Without the Program Counter, the processor would have no way of determining which instruction should be fetched next.

### Responsibilities

* Tracks instruction execution.
* Stores instruction address.
* Supplies address to instruction memory.
* Updates after instruction completion.
* Handles branch and jump operations.

### Practical Reference

![Program Counter Output](./images04/pc_output.png)

### Analysis

The Program Counter continuously changes as the processor executes instructions.

For sequential execution:

```text
PC = PC + 4
```

Since RISC-V instructions occupy 4 bytes, the PC advances by four bytes after every instruction.

This creates the instruction flow required for program execution.

---

# Understanding Instruction Memory

Once the Program Counter provides an address, the processor must retrieve the corresponding instruction.

This task is performed by Instruction Memory.

Instruction Memory acts as a storage location containing all machine instructions that make up a program.

### Responsibilities

* Store executable instructions.
* Receive address from PC.
* Return instruction bits.
* Feed instructions into the decode stage.

### Practical Reference

![Instruction Memory](./images04/instruction_memory.png)

### Analysis

Instruction Memory serves as the processor's source of executable code.

Each memory location contains an encoded machine instruction.

Although programmers write software using high-level languages and assembly code, the processor ultimately executes instructions stored in this memory as binary values.

This image illustrates how instructions are organized before entering the execution pipeline.

---

# Understanding Instruction Flow

One of the key observations from this session was that instruction execution is not a single operation.

Instead, it is a sequence of coordinated hardware activities.

A simplified execution flow can be represented as:

```text
Fetch
  ↓
Decode
  ↓
Execute
  ↓
Write Back
```

Every instruction executed by the processor follows this path.

The remaining sessions of the workshop gradually build and verify each of these stages.

---

# Engineering Takeaways

Throughout this session, I learned:

* The difference between ISA and Microarchitecture.
* Why CPUs require structured execution stages.
* The importance of the Program Counter.
* How Instruction Memory stores executable programs.
* How instructions begin their journey through the processor.
* The relationship between software instructions and hardware execution.

Most importantly, I started viewing a processor not as a black box, but as a collection of cooperating hardware blocks working together to execute instructions.

---

# Reflection

Day 04 introduced the architectural foundation required for CPU implementation.

Instead of focusing on individual logic blocks, this session focused on understanding how an entire processor is organized. The concepts learned here establish the foundation for implementing Fetch Logic, Decode Logic, Register Files, ALUs, and complete instruction execution pipelines in the upcoming sections.
# Investigating the Fetch Stage

After understanding the major building blocks of a processor, the next question naturally arises:

> How does a processor know which instruction to execute?

Before an instruction can be decoded, executed, or written back, it must first be fetched from memory.

This responsibility belongs to the **Fetch Stage**, which acts as the entry point for every instruction executed by the processor.

The fetch stage establishes the connection between the Program Counter and Instruction Memory, ensuring that instructions are continuously supplied to the processor.

---

# Why the Fetch Stage Matters

Every software program consists of a sequence of instructions stored in memory.

For example:

```assembly
add x5, x1, x2
sub x6, x4, x3
and x7, x5, x6
```

Although these instructions appear sequential to the programmer, the processor must actively retrieve them from memory before any execution can occur.

Without an effective fetch mechanism:

* Instructions cannot enter the pipeline.
* The processor remains idle.
* Program execution becomes impossible.

This makes the fetch stage one of the most fundamental components of processor operation.

---

# Relationship Between Program Counter and Instruction Memory

The fetch stage relies on the cooperation of two critical hardware blocks:

### Program Counter (PC)

Stores the address of the instruction that should be fetched.

### Instruction Memory

Stores the machine instructions corresponding to those addresses.

Together, they create the instruction supply mechanism for the processor.

The interaction can be represented as:

```text
Program Counter
        ↓
Instruction Address
        ↓
Instruction Memory
        ↓
Instruction Bits
        ↓
Decode Stage
```

---

# Understanding Program Counter Progression

During normal execution, instructions are processed sequentially.

Since each RISC-V instruction occupies four bytes, the Program Counter advances as:

```text
PC = PC + 4
```

Example:

```text
0x00000000
0x00000004
0x00000008
0x0000000C
```

Each new address points to the next instruction in memory.

This simple mechanism enables the processor to execute programs in order.

---

# Fetch Stage Implementation

To understand instruction fetching more clearly, I investigated the interaction between the Program Counter and Instruction Memory.

The objective was to observe how instruction addresses are generated and how corresponding instructions are retrieved.

---

## Practical Evidence

![PC with Instruction Memory](./images04/pc_with_instruction_memory_combined_output.png)

> Replace the filename above with the exact filename from your folder if there are additional characters after `combined`.

---

## Analysis

The visualization demonstrates the direct relationship between the Program Counter and Instruction Memory.

For every Program Counter value:

1. The PC generates an address.
2. Instruction Memory receives the address.
3. The corresponding instruction is returned.
4. The instruction is forwarded for decoding.

This process repeats continuously throughout program execution.

The fetch stage therefore acts as the processor's instruction delivery mechanism.

---

# Understanding Machine Instructions

One important realization during this section was that the processor never sees source code.

For example:

```c
sum = sum + i;
```

is eventually transformed into:

```assembly
add x10, x11, x12
```

which is then converted into:

```text
Binary Machine Code
```

The fetch stage works exclusively with machine instructions stored inside memory.

This highlights the distinction between software representation and hardware execution.

---

# Fetch Verification

After implementing the fetch logic, the next step was verifying whether instructions were being retrieved correctly.

Verification is essential because every downstream stage depends on accurate instruction delivery.

If the fetch stage fails, the remainder of the processor cannot function correctly.

---

## Practical Evidence

![Fetch Decode Output](./images04/complete_fetch_decode_output.png)

---

## Analysis

The output confirms that instructions are successfully retrieved from memory and forwarded to subsequent stages.

Several important observations can be made:

* Program Counter values advance correctly.
* Instruction addresses match memory locations.
* Retrieved instructions are forwarded without corruption.
* The instruction stream remains continuous.

These observations validate the correctness of the fetch mechanism.

---

# Engineering Observation

One of the most valuable insights from this section was understanding that processor execution begins long before arithmetic operations occur.

When learning computer architecture, many students focus primarily on ALUs and computations.

However, before any computation is possible, the processor must first locate and retrieve the correct instruction.

This makes instruction fetching one of the most critical responsibilities within the entire processor.

---

# Key Learning

This section helped me understand:

* How instructions enter the processor.
* The relationship between Program Counter and Instruction Memory.
* Why PC increments by four bytes.
* How machine instructions are retrieved.
* The role of fetch logic in processor execution.
* The importance of verification before moving to decode logic.

Most importantly, I learned that every instruction executed by a processor begins its journey through the fetch stage.

---

# Part 2 Reflection

Part 2 focused on the first operational stage of the processor.

The concepts explored can be summarized as:

```text
Program Counter
        ↓
Instruction Address
        ↓
Instruction Memory
        ↓
Instruction Fetch
        ↓
Decode Stage
```

By implementing and verifying the fetch mechanism, I established the foundation required for understanding instruction decoding, register access, and execution logic in the upcoming sections.
# Investigating the Decode Stage

After successfully implementing the Fetch Stage, the processor is now capable of retrieving instructions from memory.

However, fetching an instruction is only the beginning of the execution process.

At this point, the processor possesses a 32-bit binary value, but it still does not understand what operation that instruction represents.

This raises an important question:

> How does a processor determine what an instruction actually means?

The answer lies in the **Decode Stage**.

The decode stage acts as the processor's interpreter, transforming raw instruction bits into meaningful control information that can be understood by the execution hardware.

---

# Why Instruction Decoding Is Necessary

When a program is stored in memory, instructions are represented as binary values.

For example, a programmer may write:

```assembly
add x5, x1, x2
```

but the processor ultimately receives something similar to:

```text
00000000001000001000001010110011
```

To a processor, this is simply a collection of bits.

Before execution can begin, the processor must determine:

* Which operation should be performed?
* Which registers are involved?
* Where should the result be stored?
* Is an immediate value required?
* Does the instruction involve branching?

The Decode Stage performs this interpretation.

---

# Understanding the Structure of a RISC-V Instruction

One of the key insights from this session was learning that RISC-V instructions follow a structured format.

Rather than treating instructions as random bit patterns, the processor divides the instruction into predefined fields.

These fields contain information such as:

```text
Opcode
Source Register 1 (rs1)
Source Register 2 (rs2)
Destination Register (rd)
Function Codes
Immediate Values
```

By extracting these fields, the processor can determine exactly how an instruction should be executed.

---

# The Role of the Opcode

Among all instruction fields, the most important is the **Opcode**.

The opcode acts as the instruction identifier.

It tells the processor:

> What type of operation is being requested?

Examples include:

```text
Arithmetic Instructions

Logical Instructions

Load Instructions

Store Instructions

Branch Instructions
```

The decode logic first examines the opcode before performing any further analysis.

This makes the opcode the starting point of instruction classification.

---

# Instruction Classification

After extracting the opcode, the processor groups instructions into categories.

Each category follows a different execution path.

For example:

### Arithmetic Operations

```assembly
add
sub
and
or
```

---

### Immediate Operations

```assembly
addi
andi
ori
```

---

### Memory Operations

```assembly
lw
sw
```

---

### Branch Instructions

```assembly
beq
bne
blt
```

Each category requires different hardware resources and control signals.

The decode stage determines which path should be activated.

---

# Understanding Instruction Type Detection

To better understand instruction classification, I investigated how the processor identifies different instruction formats.

The objective was to observe how raw instruction bits are analyzed and categorized before execution.

---

## Practical Evidence

![Instruction Type Checking](./images04/instruction_type_checking.png)

---

## Analysis

The visualization demonstrates the processor analyzing incoming instructions and determining their corresponding categories.

Several important observations can be made:

* Instructions are not treated equally.
* Different instruction types activate different execution paths.
* Classification occurs before execution begins.
* Control information is generated based on instruction format.

This process ensures that the correct hardware resources are selected for each instruction.

---

# Understanding Immediate Values

Not all instructions operate exclusively on register contents.

Many instructions include embedded constant values known as **immediates**.

Example:

```assembly
addi x5, x1, 10
```

In this instruction:

```text
x1 → Source Register
10 → Immediate Value
```

The decode stage extracts this constant and prepares it for execution.

Without immediate extraction, instructions such as:

* ADDI
* ANDI
* ORI
* Load Instructions
* Store Instructions
* Branch Instructions

would not function correctly.

---

# Decode Stage Data Flow

The decode stage acts as a bridge between instruction fetching and execution.

The overall process can be summarized as:

```text
Fetched Instruction
         ↓
Opcode Extraction
         ↓
Instruction Classification
         ↓
Field Extraction
         ↓
Immediate Generation
         ↓
Control Signal Generation
         ↓
Execution Stage
```

This transformation is one of the most critical steps in processor operation.

---

# Integrating Fetch and Decode

Once decoding logic is implemented, the processor can begin transforming fetched instructions into meaningful actions.

To verify this behavior, I analyzed the combined Fetch and Decode pipeline.

---

## Practical Evidence

![Fetch Decode Integration](./images04/complete_fetch_decode_output.png)

---

## Analysis

The output demonstrates successful interaction between the fetch and decode stages.

The execution flow follows:

```text
Program Counter
         ↓
Instruction Memory
         ↓
Instruction Fetch
         ↓
Instruction Decode
         ↓
Classification
```

Several important observations emerge:

* Instructions are fetched correctly.
* Instruction fields are extracted successfully.
* Decode logic correctly identifies instruction categories.
* Control information is generated for future stages.

This confirms that the processor is now capable of understanding the instructions it retrieves.

---

# Engineering Observation

One of the most valuable lessons from this section was realizing that instruction execution does not begin inside the ALU.

Before arithmetic or logical operations occur, the processor must first understand the instruction.

The decode stage provides this understanding.

In many ways, the decode logic acts as the decision-making center of the processor because it determines how every instruction should be handled.

Without decoding, execution hardware would have no knowledge of what operation to perform.

---

# Key Learning

This section helped me understand:

* Why instruction decoding is required.
* The structure of RISC-V instructions.
* The importance of opcodes.
* How instructions are classified.
* How immediate values are extracted.
* How control information is generated.
* How Fetch and Decode stages interact.

Most importantly, I learned that the decode stage transforms raw binary instructions into meaningful operations that the processor can execute.

---

# Part 3 Reflection

Part 3 introduced one of the most important responsibilities of a processor:

```text
Instruction Fetch
         ↓
Instruction Decode
         ↓
Instruction Understanding
         ↓
Execution Preparation
```

By implementing and verifying decode logic, I moved one step closer to building a complete RISC-V processor.

The processor can now:

* Retrieve instructions from memory.
* Analyze instruction fields.
* Identify instruction types.
* Generate information required for execution.

This establishes the foundation for the next stage of processor development: Register File access and ALU-based execution.
# Investigating the Execution Stage

After implementing the Fetch and Decode stages, the processor can now:

* Retrieve instructions from memory.
* Interpret instruction fields.
* Classify instruction types.
* Generate execution information.

However, the processor still has not performed any actual computation.

This raises the next important question:

> How does the processor perform arithmetic and logical operations?

The answer lies in the **Execution Stage**.

The execution stage is where instructions are transformed into meaningful results. It is the point where the processor begins operating on data and producing outputs.

---

# Understanding the Register File

Before an instruction can be executed, the processor must first obtain the required operands.

These operands are stored inside the **Register File**.

The Register File serves as the processor's high-speed working memory.

Unlike main memory, registers provide extremely fast access and are optimized for frequent operations.

---

## Why Registers Are Important

Consider the instruction:

```assembly
add x5, x1, x2
```

To execute this instruction, the processor must:

1. Read the value stored in x1.
2. Read the value stored in x2.
3. Send both values to the ALU.
4. Compute the result.
5. Store the result into x5.

Without the Register File, arithmetic operations would not be possible.

---

## Responsibilities of the Register File

The Register File performs three major tasks:

### Operand Storage

Stores temporary values required during execution.

### Operand Retrieval

Provides source operands for instructions.

### Result Storage

Stores computation results generated by the ALU.

---

# Understanding Register Access

The Decode Stage extracts:

```text
rs1
rs2
rd
```

from the instruction.

These fields tell the Register File:

```text
Which values should be read?

Where should the result be written?
```

This creates the connection between instruction decoding and execution.

---

# Engineering Observation

One important realization during this section was that instructions themselves do not contain actual data.

Instead, they contain references to registers.

The processor must retrieve the required values before any operation can begin.

This separation between instructions and data is one of the key design principles of modern processors.

---

# Investigating the Arithmetic Logic Unit (ALU)

Once operands are retrieved from the Register File, they are forwarded to the Arithmetic Logic Unit.

The ALU is responsible for performing computations.

It acts as the mathematical engine of the processor.

---

## ALU Responsibilities

The ALU performs:

### Arithmetic Operations

```text
Addition
Subtraction
```

---

### Logical Operations

```text
AND
OR
XOR
```

---

### Comparison Operations

```text
Equal
Not Equal
Less Than
Greater Than
```

---

### Branch Evaluation

Determines whether branch conditions are satisfied.

---

# Why the ALU Matters

Almost every instruction eventually depends on the ALU.

Examples:

```assembly
add x5, x1, x2
```

```assembly
sub x7, x8, x9
```

```assembly
and x4, x2, x3
```

```assembly
beq x1, x2, label
```

Although these instructions appear different, they all require ALU participation.

This makes the ALU one of the most heavily utilized blocks inside a processor.

---

# ALU Implementation Investigation

To understand how execution works, I implemented and analyzed the ALU stage of the processor.

The objective was to verify that the processor could correctly perform computations based on decoded instructions.

---

## Practical Evidence

![ALU Output](./images04/alu_output.png)

---

## Analysis

The visualization demonstrates successful execution of arithmetic and logical operations.

Several important observations can be made:

* Operands are correctly forwarded from the Register File.
* The ALU performs the required operation.
* Results are generated successfully.
* Outputs correspond to instruction requirements.

This confirms that the execution hardware is functioning correctly.

---

# Understanding Data Flow During Execution

The complete execution path can now be represented as:

```text
Instruction
      ↓
Decode
      ↓
Register File Read
      ↓
Operand Selection
      ↓
ALU Operation
      ↓
Result Generation
```

At this stage, the processor has moved beyond instruction interpretation and begun actual computation.

---

# Connecting Decode and Execute Stages

One of the most interesting observations from this session was how closely the Decode and Execute stages are connected.

The Decode Stage determines:

```text
What operation should be performed?
```

The Execute Stage determines:

```text
What result should be produced?
```

Together, these stages transform instruction information into processor behavior.

---

# Example: ADD Instruction Execution

Consider:

```assembly
add x5, x1, x2
```

Execution proceeds as:

### Step 1

Fetch instruction.

```text
add x5, x1, x2
```

---

### Step 2

Decode fields.

```text
rd  = x5
rs1 = x1
rs2 = x2
```

---

### Step 3

Read operands.

```text
Value(x1)
Value(x2)
```

---

### Step 4

Perform ALU operation.

```text
Result = Value(x1) + Value(x2)
```

---

### Step 5

Prepare result for write-back.

```text
x5 ← Result
```

This illustrates the complete execution flow of a simple arithmetic instruction.

---

# Engineering Observation

Before studying CPU design, I often viewed instructions as single operations.

This investigation revealed that even a simple ADD instruction requires multiple coordinated hardware activities.

The processor must:

* Fetch the instruction.
* Decode instruction fields.
* Read registers.
* Execute the operation.
* Prepare the result.

What appears simple from a software perspective is actually a carefully orchestrated sequence of hardware events.

---

# Key Learning

Through this section, I learned:

* The purpose of the Register File.
* How operands are supplied to the ALU.
* The role of arithmetic and logical operations.
* How execution hardware processes instructions.
* The interaction between Decode and Execute stages.
* The complete data path involved in instruction execution.

Most importantly, I learned that the execution stage is where the processor transforms instruction intent into actual computation.

---

# Part 4 Reflection

Part 4 introduced the computational heart of the processor.

The execution flow can now be summarized as:

```text
Fetch
    ↓
Decode
    ↓
Register Access
    ↓
ALU Execution
    ↓
Result Generation
```

At this stage, the processor can:

* Fetch instructions.
* Decode instruction fields.
* Access register operands.
* Perform arithmetic operations.
* Generate execution results.

This establishes the core functionality required for building a complete RISC-V processor and prepares the foundation for integrating all stages into a unified CPU in the final section.
# Integrating the Complete RISC-V Processor

After implementing and verifying the individual stages of the processor, the final objective of Day 04 was to integrate these components into a complete instruction execution flow.

Up to this point, each stage had been studied independently:

```text
Program Counter
        ↓
Instruction Fetch
        ↓
Instruction Decode
        ↓
Register Access
        ↓
ALU Execution
```

While understanding individual stages is important, a processor only becomes useful when all of these blocks operate together as a coordinated system.

This final section focuses on observing the complete processor in action and verifying that instructions successfully travel through the entire execution path.

---

# From Individual Blocks to a Complete CPU

One of the most important lessons in processor design is that a CPU is not a single piece of hardware.

Instead, it is a collection of specialized hardware modules working together.

Throughout Day 04, I progressively built an understanding of these modules:

### Program Counter

Determines which instruction should be fetched.

### Instruction Memory

Provides the instruction associated with the current address.

### Decode Logic

Interprets instruction fields and generates execution information.

### Register File

Supplies operands required by instructions.

### Arithmetic Logic Unit

Performs computations and produces results.

When connected together, these blocks form a complete processor datapath.

---

# Understanding the Complete Instruction Journey

At this stage, I was able to visualize how a single instruction travels through the processor.

The complete execution sequence can be represented as:

```text
Program Counter
        ↓
Instruction Fetch
        ↓
Instruction Decode
        ↓
Register Read
        ↓
ALU Execution
        ↓
Result Generation
        ↓
Next Instruction
```

Although software developers typically view instruction execution as a single operation, the processor performs multiple coordinated activities behind the scenes.

---

# End-to-End Verification

The final verification objective was to ensure that all processor stages function correctly when operating together.

Rather than testing individual modules, the processor was evaluated as a complete system.

The primary verification questions were:

* Are instructions fetched correctly?
* Is decoding functioning properly?
* Are register operands being accessed correctly?
* Is the ALU generating valid results?
* Does instruction flow remain continuous?

Answering these questions validates the correctness of the processor architecture.

---

# Practical Evidence

![Complete CPU Output](./images04/day4_final_output.png)

---

# Analysis

The visualization demonstrates the successful interaction of all processor components.

Several important observations can be made:

### Instruction Flow

Instructions move through the processor in the expected sequence.

---

### Address Generation

The Program Counter continuously updates and supplies valid instruction addresses.

---

### Decode Activity

Instructions are correctly classified and interpreted.

---

### Operand Access

The Register File successfully provides the required source operands.

---

### Execution

The ALU performs computations based on decoded instructions.

---

### Result Propagation

Generated results successfully move through the datapath.

These observations confirm that the major processor components are functioning together correctly.

---

# Understanding Processor Coordination

One of the most important insights from this section was recognizing that processor performance depends on coordination rather than individual components.

For example:

A powerful ALU is useless without:

* Correct instruction fetch.
* Proper decoding.
* Accurate operand retrieval.

Similarly, a perfectly functioning fetch stage cannot execute instructions without downstream hardware.

This highlights the importance of architectural integration.

---

# Revisiting the Sum 1 to 9 Program

Throughout the workshop, a simple summation program was used as the primary verification benchmark.

Although the program itself is straightforward, it exercises several important processor features:

### Arithmetic Operations

Repeated additions.

### Register Access

Continuous register updates.

### Control Flow

Loop execution and branching.

### Instruction Sequencing

Multiple instructions executed in order.

Because of these characteristics, the program serves as an effective validation workload for processor development.

---

# Engineering Observation

Before this workshop, I often viewed a CPU as a mysterious component that somehow executed software.

After studying the processor stage by stage, I developed a much deeper understanding of what actually occurs internally.

The processor no longer appears as a black box.

Instead, it can be viewed as a collection of interconnected hardware structures that cooperate to execute instructions.

This perspective represents one of the most valuable outcomes of Day 04.

---

# Connecting Day 04 to Previous Learning

One of the most interesting observations was seeing how concepts from earlier workshop sessions contributed directly to processor implementation.

### Day 01

Introduced:

```text
Software
        ↓
Compiler
        ↓
Assembly
        ↓
Machine Instructions
```

---

### Day 02

Introduced:

```text
Instruction Analysis
ABI Concepts
Verification Foundations
```

---

### Day 03

Introduced:

```text
Combinational Logic
Sequential Logic
Pipeline Logic
Validity
```

---

### Day 04

Integrated everything into:

```text
A Working Processor Architecture
```

This progression clearly demonstrated how seemingly independent topics combine to form a complete CPU.

---

# Key Learning Outcomes

Through Day 04, I learned:

* The difference between ISA and Microarchitecture.
* The role of the Program Counter.
* How Instruction Memory operates.
* How instructions are fetched.
* How instruction decoding works.
* How registers provide operands.
* How the ALU executes operations.
* How processor stages interact.
* The importance of verification.
* How a complete RISC-V processor is organized.

Most importantly, I learned how individual digital design concepts combine to form an actual processor.

---

# My Understanding After Day 04

At the beginning of this workshop, I primarily viewed processors from a software perspective.

After completing Day 04, I gained a much deeper appreciation for the hardware mechanisms responsible for instruction execution.

The complete execution path can now be summarized as:

```text
Software Program
        ↓
Machine Instructions
        ↓
Program Counter
        ↓
Instruction Fetch
        ↓
Instruction Decode
        ↓
Register Access
        ↓
ALU Execution
        ↓
Result Generation
```

Understanding this flow provided the architectural foundation required for the final phase of the workshop, where the focus shifts toward implementing and validating a complete RISC-V CPU.

---

# Day 04 Summary

Day 04 represented the transition from studying digital logic to understanding processor architecture.

The journey throughout this session can be summarized as:

```text
CPU Building Blocks
        ↓
Instruction Fetch
        ↓
Instruction Decode
        ↓
Execution Logic
        ↓
Processor Integration
        ↓
RISC-V CPU Understanding
```

This session provided the architectural blueprint required for implementing a complete processor and serves as a critical milestone in the overall RISC-V learning journey.

---

# References

* NASSCOM RISC-V MYTH Program
* Makerchip Platform
* TL-Verilog Documentation
* RISC-V ISA Specification
* Workshop Labs and Exercises

---

[⬅ Back to Repository Home](../README.md)

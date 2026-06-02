# Day 02 – Understanding How Software Interacts with Processor Hardware

## Overview

On Day 01, I explored how a high-level C program is transformed into machine instructions using the RISC-V GNU Toolchain.

While this explained how software reaches the processor, it raised a much deeper question:

> Once machine instructions are generated, how does the processor know where to find data, which registers to use, and how different software components communicate with each other?

Answering this question led me into the world of the Application Binary Interface (ABI), memory organization, register conventions, and instruction formats.

Day 02 was particularly important because it shifted my perspective from software compilation to processor execution. Instead of focusing on how instructions are generated, I started exploring how instructions interact with hardware resources such as registers and memory.

Throughout this session, I investigated:

- Application Binary Interface (ABI)
- Register Organization in RV64
- Memory Architecture
- Little Endian Representation
- Load and Store Operations
- RISC-V Instruction Formats
- SPIKE Simulator Fundamentals

This knowledge forms the foundation required for understanding assembly programming, debugging, processor verification, and CPU design.

---

# Learning Journey Map

The journey of Day 02 can be summarized using the following roadmap:

```text
Application Program
        ↓
API
        ↓
ABI
        ↓
Instruction Set Architecture (ISA)
        ↓
Registers & Memory
        ↓
Machine Instructions
        ↓
Processor Execution
        ↓
Debugging & Verification
```

By the end of this session, I gained a much clearer understanding of how software communicates with processor hardware through standardized conventions and instruction execution mechanisms.

---

# Understanding the Role of ABI in Processor Communication

After learning how software is compiled on Day 01, I wanted to understand how software actually interacts with processor hardware during execution.

This investigation introduced me to the concept of the **Application Binary Interface (ABI)**.

The ABI can be viewed as a communication contract between software and hardware. It defines a standardized set of rules that every application, compiler, operating system, and processor follows.

Without an ABI, software components would have no common understanding of:

- How function arguments are passed
- Which registers should be used
- How return values are handled
- How memory should be accessed
- How system calls should be executed

As a result, programs compiled by different toolchains would not be able to execute reliably on the same processor architecture.

From a processor design perspective, the ABI acts as a critical abstraction layer that allows software developers to write programs without worrying about the internal implementation details of the hardware.

One of the most important insights I gained was that processor execution is not only about instructions; it is also about following a common set of communication rules.

---

# API vs ABI vs ISA – Understanding the Complete Software Stack

While studying ABI, I encountered two other important terms:

- API
- ISA

At first glance, all three seem similar, but they serve very different purposes.

The following hierarchy helped me understand their relationship:

```text
Application Program
        ↓
API
        ↓
ABI
        ↓
ISA
        ↓
RTL Implementation
        ↓
Hardware
```

---

## What is an API?

An Application Programming Interface (API) defines how software communicates with software.

Examples include:

```c
printf()
scanf()
fopen()
```

These functions allow applications to interact with software libraries without needing to know their internal implementation.

As a programmer, APIs are usually the first layer of abstraction we encounter.

---

## What is an ABI?

The ABI sits one layer below the API.

While APIs define communication between software components, the ABI defines communication between compiled software and processor hardware.

The ABI specifies:

- Register usage
- Function call conventions
- Stack organization
- Return value handling
- System call interfaces

This standardization allows software to execute consistently across different systems that implement the same architecture.

---

## What is an ISA?

The Instruction Set Architecture (ISA) defines the actual machine language understood by a processor.

Examples of RISC-V instructions include:

```assembly
add
sub
ld
sd
beq
blt
```

These instructions form the vocabulary that hardware understands.

Every processor implementing the RISC-V ISA must execute these instructions according to the architecture specification.

---

## My Key Understanding

One of my biggest takeaways was realizing that:

- APIs connect applications to libraries.
- ABIs connect software to hardware.
- ISAs define the language spoken by hardware.

Together, these layers enable software to run seamlessly on a processor.

---

# Exploring Register Organization in RV64 Architecture

Registers are among the most important resources inside a processor.

Before this session, I knew registers existed, but I had never explored why they are organized the way they are.

Registers serve as high-speed storage locations that hold data currently being processed by the CPU.

Unlike memory accesses, which may require multiple cycles, register operations are typically performed much faster.

This makes registers essential for:

- Arithmetic operations
- Logical operations
- Function communication
- Address calculations
- Temporary data storage

---

## Register Structure in RV64

The RV64 architecture contains:

```text
32 General Purpose Registers
```

Each register is:

```text
64 Bits Wide
```

This width is represented using:

```text
XLEN = 64
```

meaning every register can store a full 64-bit value.

---

## Why Exactly 32 Registers?

This question caught my attention during the session.

Why not 16?

Why not 64?

The answer involves balancing:

- Hardware complexity
- Silicon area
- Instruction encoding efficiency
- Performance

Using 32 registers allows sufficient storage for most programs while keeping instruction formats compact and efficient.

This design decision is one of the reasons RISC-V instructions can remain fixed at 32 bits.

---

## ABI Register Naming Convention

Internally, the processor identifies registers as:

```text
x0 → x31
```

However, programmers rarely use these names directly.

The ABI introduces meaningful aliases:

| Register | ABI Name | Purpose |
|-----------|-----------|----------|
| x0 | zero | Constant 0 |
| x1 | ra | Return Address |
| x2 | sp | Stack Pointer |
| x10-x17 | a0-a7 | Function Arguments |
| x18-x27 | s2-s11 | Saved Registers |
| x28-x31 | t3-t6 | Temporary Registers |

---

## Engineering Observation

One important realization was that ABI register names are not additional hardware.

The processor still contains only x0–x31.

The ABI simply provides human-friendly aliases that make software easier to understand and maintain.

This small abstraction dramatically improves code readability.

---

# Understanding Memory Organization

Registers provide fast storage, but they are limited in number.

Modern programs often require significantly more storage than registers can provide.

This is where memory becomes essential.

Memory stores:

- Program instructions
- Variables
- Arrays
- Stack data
- Function information

Unlike registers, memory is much larger but slower to access.

This tradeoff forms one of the most fundamental concepts in computer architecture.

---

## Byte Addressable Memory

RISC-V uses a byte-addressable memory system.

This means:

```text
1 Memory Address = 1 Byte
```

Example:

```text
Address      Contents

0x0000       8 Bits
0x0001       8 Bits
0x0002       8 Bits
0x0003       8 Bits
```

Every address points to exactly one byte.

Larger values are stored across multiple consecutive addresses.

---

## Storing a 64-Bit Value

Since RV64 registers hold 64-bit values:

```text
64 Bits = 8 Bytes
```

Therefore a single 64-bit number occupies:

```text
8 Consecutive Memory Locations
```

This concept becomes extremely important when working with load and store instructions.

---

# Little Endian Representation – How Memory Stores Data

One of the most interesting discoveries of Day 02 was understanding how multi-byte values are stored in memory.

RISC-V follows a:

```text
Little Endian Memory Organization
```

This means:

```text
Least Significant Byte
Stored at
Lowest Memory Address
```

---

## Example

Consider the following 64-bit value:

```text
0x123456789ABCDEF0
```

It is stored in memory as:

```text
Address      Value

M[0]         F0
M[1]         DE
M[2]         BC
M[3]         9A
M[4]         78
M[5]         56
M[6]         34
M[7]         12
```

Although the bytes appear reversed, the processor automatically reconstructs the original value when loading it into a register.

---

## Why Does This Matter?

Understanding Endianness is critical because:

- Load instructions depend on it
- Store instructions depend on it
- Debugging memory requires it
- Processor verification relies on it

Many real-world bugs occur because developers misunderstand how data is organized in memory.

---

# My Understanding After Part 1

Day 02 transformed my understanding of processor execution.

Instead of viewing execution as simply running instructions, I began seeing the underlying communication mechanisms that make execution possible.

I learned that successful processor execution depends on:

```text
ABI Conventions
        +
Register Organization
        +
Memory Architecture
        +
Instruction Set Architecture
```

Together, these components create the foundation upon which all software execution is built.
# From High-Level Code to Processor Execution

After understanding how the ABI organizes communication between software and hardware, I wanted to observe these concepts in action.

Rather than studying theory alone, I explored how a simple C program is translated into assembly instructions and how those instructions execute on a RISC-V processor.

This investigation provided my first practical exposure to:

- Assembly Language Programming
- ABI Register Usage
- Function Calling Conventions
- Instruction Execution
- Debugging Using SPIKE

The goal was not simply to run a program, but to understand how the processor interprets and executes each instruction.

---

# Investigating a Simple C Program

To begin this exploration, I started with a simple C program that calculates the sum of numbers from 1 to 9.

## C Program

<img src="./images02/1to9_custom.c.png" width="900">

The program itself appears straightforward.

However, from a processor perspective, several important operations occur behind the scenes:

- Variables must be stored somewhere.
- Function arguments must be passed correctly.
- Intermediate results must be preserved.
- The final result must be returned to the calling function.

These responsibilities are governed by the ABI conventions discussed earlier.

---

## Looking Beyond the Source Code

When programmers write C code, they typically focus on the algorithm.

The compiler, however, must translate that algorithm into machine-executable instructions.

This raised an important question:

> What does the processor actually execute?

To answer this question, I moved one level deeper and examined the Assembly Language implementation.

---

# Understanding the Assembly Implementation

Assembly language provides a much closer view of processor behavior.

Unlike C, where operations are expressed at a high level, assembly instructions directly manipulate processor registers and memory locations.

## Assembly Program

<img src="./images02/load.S.png" width="900">

This program performs the same summation operation as the C implementation but expresses every operation explicitly.

---

## Understanding the Program Structure

The assembly routine consists of three major phases:

### Phase 1 – Initialization

Registers are initialized before entering the loop.

Typical responsibilities include:

- Initializing the running sum
- Initializing loop counters
- Preparing argument registers

At this stage, the processor is preparing the execution environment.

---

### Phase 2 – Loop Execution

The processor repeatedly executes instructions that:

- Add values to the running total
- Increment the loop counter
- Compare values
- Branch back to the loop if necessary

Unlike high-level languages, every step of the loop becomes visible.

This helped me understand how processors execute repetitive operations internally.

---

### Phase 3 – Returning the Result

After the loop completes:

- The final result is placed in the return register.
- Control returns to the calling function.

This process follows ABI conventions that define how functions exchange information.

---

# Understanding ABI Register Usage in Practice

One of the most valuable observations from this experiment was seeing ABI conventions being used in a real program.

The ABI specifies that:

```text
a0-a7
```

are argument registers.

This means:

- Function inputs are passed through these registers.
- Return values are typically returned through a0.

---

## Why This Matters

Before learning ABI, I viewed function calls as a software concept.

This experiment revealed that function calls are actually implemented through carefully organized register transfers.

Every function call ultimately becomes:

```text
Move Data Into Registers
        ↓
Execute Instructions
        ↓
Return Data Through Registers
```

This insight helped me connect software development concepts with processor architecture.

---

# Investigating Program Execution Using SPIKE

Understanding assembly code statically is useful.

However, understanding how the processor executes that code at runtime provides a much deeper level of insight.

To observe execution directly, I used:

```bash
spike -d pk sum1to9
```

This launches SPIKE in debug mode.

---

# Why Use SPIKE?

SPIKE acts as a virtual RISC-V processor.

It allows observation of:

- Register contents
- Memory values
- Program Counter updates
- Instruction execution
- Control flow behavior

Normally these internal processor activities are invisible.

SPIKE exposes them for analysis.

---

# Debug Session Analysis

## Debugging Screenshot

<img src="./images02/debug_of_1ton.png" width="900">

The screenshot above captures a live debugging session performed using SPIKE.

Rather than simply running the program to completion, I paused execution and inspected the processor state.

---

## What Was Investigated?

During debugging, I analyzed:

### Register Values

Using:

```bash
reg 0
```

I observed the contents of processor registers during execution.

This helped me verify:

- Function arguments
- Loop counters
- Intermediate calculations
- Final results

---

### Program Counter Movement

Using:

```bash
pc 0
```

I tracked how the processor moved through instructions.

This demonstrated the fetch-decode-execute cycle in action.

---

### Memory Contents

Using:

```bash
mem 0 address
```

I inspected memory locations during execution.

This provided insight into how data is stored and retrieved by the processor.

---

### Step-by-Step Execution

Using:

```bash
step
```

I executed instructions one at a time.

This was particularly useful for understanding:

- Branch instructions
- Loop behavior
- Register updates

---

# Engineering Observation

One of the most important realizations during debugging was that processors are not mysterious black boxes.

When observed carefully, processor behavior follows a logical sequence of:

```text
Fetch
    ↓
Decode
    ↓
Execute
    ↓
Update State
    ↓
Fetch Next Instruction
```

Every instruction modifies some aspect of the processor state.

SPIKE allowed me to observe these changes directly.

---

# What This Experiment Taught Me

Before this exercise, assembly instructions appeared to be isolated commands.

After stepping through execution instruction-by-instruction, I began viewing them as part of a coordinated execution process.

This experiment helped me understand:

- How loops execute internally
- How registers change over time
- How branches alter program flow
- How ABI conventions are enforced
- How software interacts with processor hardware

Most importantly, it transformed assembly language from something I could read into something I could actively analyze and verify.

---

# Key Takeaways from Part 2

- A C program ultimately becomes a sequence of assembly instructions.
- ABI conventions define how functions communicate through registers.
- Assembly language exposes the actual operations performed by the processor.
- SPIKE provides visibility into processor internals during execution.
- Debugging at the instruction level significantly improves understanding of processor behavior.
- Processor execution is a structured sequence of state transitions rather than a collection of isolated instructions.
- # From Software Execution to Processor Verification

Up to this point, I had explored how a C program is translated into assembly instructions and how those instructions execute on a RISC-V processor using SPIKE.

However, another important question remained:

> How does a processor actually execute these instructions in hardware?

To answer this question, I explored a complete execution flow involving a real RISC-V processor implementation, firmware generation, memory loading, and verification.

This investigation marked my first exposure to the practical workflow used in processor development and verification.

---

# Exploring the PicoRV32 Processor Core

Software alone cannot demonstrate processor behavior.

A processor implementation is required to execute instructions.

For this purpose, the workshop introduced the **PicoRV32 Processor Core**.

## PicoRV32 Overview

<img src="./images02/picor32.png" width="900">

PicoRV32 is a lightweight RISC-V processor implemented using Verilog HDL.

Although much smaller than commercial processors, it contains the fundamental building blocks found in any CPU:

- Program Counter
- Register File
- Arithmetic Logic Unit (ALU)
- Instruction Decoder
- Memory Interface
- Control Logic

Studying PicoRV32 helped me understand how software eventually interacts with actual hardware components.

---

## Why Study PicoRV32?

At this stage of the workshop, my focus shifted from:

```text
Writing Programs
```

to

```text
Understanding How Processors Execute Programs
```

PicoRV32 provided a practical platform for observing this interaction.

Rather than treating the CPU as a black box, I could now see how machine instructions are executed by hardware modules.

---

# Setting Up the Verification Environment

Before executing software on the processor, several collateral files needed to be obtained.

These files contained:

- RTL Source Files
- Testbench Files
- Memory Models
- Firmware Loading Logic
- Simulation Infrastructure

---

## Repository Setup

<img src="./images02/github_cloning.png" width="900">

The screenshot above shows the process of cloning and exploring the project repository.

During this stage, I became familiar with the directory structure used in hardware projects.

Unlike software projects, processor repositories contain multiple layers including:

```text
RTL Design
Simulation Environment
Firmware
Memory Files
Verification Components
```

Understanding this structure was an important step toward learning processor verification.

---

# Understanding Firmware Generation

One of the most important discoveries of Day 02 was understanding that processors do not execute C programs directly.

Processors only understand machine instructions.

This means software must undergo several transformations before execution.

---

## Complete Software-to-Hardware Flow

```text
C Program
      ↓
Compilation
      ↓
Assembly Program
      ↓
Object File
      ↓
Machine Instructions
      ↓
Hexadecimal Representation
      ↓
Memory Initialization
      ↓
Processor Execution
```

This flow helped me appreciate how software eventually becomes a stream of binary instructions that hardware can understand.

---

# Converting Programs into Hex Files

After compilation, the generated instructions must be converted into a format suitable for loading into processor memory.

This format is typically a hexadecimal memory file.

---

## Hex Conversion Process

<img src="./images02/hexa_conversion_.png" width="900">

The screenshot above illustrates the conversion process.

At this stage, the program is no longer viewed as source code.

Instead, it is represented as machine-level information that can be stored inside memory.

---

## Why Is Hex Generation Necessary?

The processor fetches instructions from memory.

Therefore instructions must exist inside memory before execution begins.

The hexadecimal file acts as the bridge between software compilation and hardware execution.

Without this step, the processor would have no instructions to execute.

---

# Investigating Firmware Instructions

Once the hex file is generated, the processor sees only machine instructions.

To understand this transformation, I examined the generated firmware contents.

## Firmware Instruction File

<img src="./images02/firmware_hex_instructions.png" width="900">

The screenshot above contains the machine instructions stored in hexadecimal format.

Each line corresponds to encoded instructions generated by the compiler.

---

## What Does This File Represent?

Although the file appears to be a collection of hexadecimal numbers, each value corresponds to:

```text
Opcode
Register Fields
Immediate Values
Control Information
```

Together, these bits describe the operations the processor must perform.

This was my first exposure to the actual binary-level representation of software.

---

# Understanding Instruction Bitstreams

Machine instructions are ultimately represented using binary values.

The processor does not understand:

```text
C Code
```

or even

```text
Assembly Code
```

It understands only binary patterns.

---

## Actual Instruction Bitstream

<img src="./images02/actual_bitstream.png" width="900">

The screenshot above demonstrates how instructions are represented at the bit level.

This visualization helped me connect several concepts learned earlier:

- Instruction Formats
- Opcode Fields
- Register Encoding
- Immediate Encoding

Seeing these binary patterns reinforced the idea that software eventually becomes hardware-readable information.

---

# How Instructions Reach Memory

Generating a firmware file is only part of the process.

The next challenge is loading those instructions into memory before execution begins.

---

## Memory Loading Process

<img src="./images02/read_memory_details.png" width="900">

The screenshot above shows memory-related information used during simulation.

During initialization:

1. Firmware is read from the hex file.
2. Instructions are loaded into memory.
3. Processor reset is applied.
4. Execution begins from the starting address.

---

## Engineering Observation

This experiment revealed an important fact:

Processors do not magically know where instructions are located.

Instruction memory must be initialized before execution starts.

This memory initialization process forms a critical part of processor verification.

---

# Understanding the Role of the Testbench

Once the firmware and memory are ready, the processor requires an environment for execution.

This environment is called a **Testbench**.

---

## Testbench Analysis

<img src="./images02/testbench_code.png" width="900">

The testbench acts as a virtual laboratory for processor verification.

Its responsibilities include:

- Generating clock signals
- Applying reset signals
- Loading firmware
- Initializing memory
- Monitoring outputs
- Controlling simulation flow

Without a testbench, verifying processor behavior would be extremely difficult.

---

## Why Is a Testbench Important?

From a verification perspective, the testbench provides:

```text
Controlled Inputs
+
Observable Outputs
+
Repeatable Execution
```

This allows engineers to verify processor functionality before hardware fabrication.

---

# Final Program Execution

After:

- Compilation
- Firmware Generation
- Memory Loading
- Processor Initialization

the processor finally executes the program.

---

## Program Output

<img src="./images02/sum1ton_custom_output.png" width="900">

The screenshot above shows the successful execution of the custom summation program.

This output served as final confirmation that:

- Compilation succeeded
- Firmware generation succeeded
- Memory loading succeeded
- Processor execution succeeded

The complete software-to-hardware flow worked as expected.

---

# Engineering Reflection

Day 02 fundamentally changed the way I view software execution.

Before this workshop, I viewed program execution as:

```text
Write Code
      ↓
Run Program
      ↓
Get Output
```

After completing these investigations, I now understand that execution actually involves:

```text
Write Code
      ↓
Compile
      ↓
Generate Instructions
      ↓
Create Firmware
      ↓
Load Memory
      ↓
Initialize Processor
      ↓
Execute Instructions
      ↓
Verify Outputs
```

Understanding this flow provided valuable insight into processor architecture and verification methodologies.

---

# Key Takeaways

Throughout Day 02, I learned:

- The purpose and importance of ABI.
- How registers are organized in RV64.
- Memory organization and endianness.
- Load and Store instruction behavior.
- RISC-V instruction formats.
- Assembly language programming.
- Debugging using SPIKE.
- Firmware generation workflows.
- Processor memory initialization.
- Testbench-driven verification.
- Software-to-hardware execution flow.

Most importantly, I learned that processor execution is not a single event but a carefully coordinated sequence of interactions between software, memory, processor hardware, and verification infrastructure.

---

# My Understanding After Day 02

Day 01 taught me how software becomes machine instructions.

Day 02 taught me how those instructions are:

```text
Stored
      ↓
Loaded
      ↓
Executed
      ↓
Verified
```

This day served as my first practical introduction to processor verification and hardware execution flow.

The concepts learned here will serve as the foundation for understanding digital logic design, TL-Verilog, processor microarchitecture, and ultimately building a complete RISC-V CPU in the upcoming sessions.

---

# References

- NASSCOM RISC-V MYTH Program
- RISC-V ISA Specification
- SPIKE Simulator
- PicoRV32 Processor Core
- Workshop Labs and Verification Exercises

---

[⬅ Back to Repository Home](../README.md)
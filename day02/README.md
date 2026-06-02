# Day 02 – ABI, Memory Organization and Basic Verification Flow

## Overview

Day 02 focused on understanding how software communicates with hardware through the Application Binary Interface (ABI).

I explored:

- ABI Fundamentals
- RISC-V Register Convention
- Memory Organization
- Little Endian Representation
- Load and Store Instructions
- RISC-V Instruction Formats
- Assembly Language Programming
- SPIKE Debugging
- Running Programs on a RISC-V Core
- Hex File Generation
- Basic Verification Flow

This session helped me understand how a C program interacts with processor registers and memory at a much deeper level.

---

# Learning Journey Map

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
Assembly Instructions
        ↓
Processor Execution
```

---

# Understanding the Application Binary Interface (ABI)

Before Day 02, I knew how to compile and run programs.

The next question was:

> How does software actually communicate with hardware?

The answer lies in the ABI.

The Application Binary Interface acts as a bridge between:

- Operating System
- Application Programs
- Processor Architecture

It defines:

- Register usage
- Function calling conventions
- System calls
- Memory access mechanisms

Without ABI, software and hardware would not have a standardized communication method.

---

# Relationship Between API, ABI and ISA

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

### Key Understanding

- API connects Applications to Libraries.
- ABI connects Software to Processor Architecture.
- ISA defines machine instructions.
- RTL implements ISA in hardware.

---

# Register Organization in RISC-V

RISC-V defines:

```text
32 Registers
```

Each register is:

```text
64 bits wide (RV64)
```

The width of a register is defined using:

```text
XLEN = 64
```

---

## Important Register Categories

| Register | ABI Name | Purpose |
|-----------|-----------|----------|
| x0 | zero | Constant Zero |
| x1 | ra | Return Address |
| x2 | sp | Stack Pointer |
| x3 | gp | Global Pointer |
| x10-x17 | a0-a7 | Function Arguments |
| x18-x27 | s2-s11 | Saved Registers |
| x28-x31 | t3-t6 | Temporary Registers |

### Key Observation

The ABI gives meaningful names to registers so programmers can use them efficiently during software development.

---

# Memory Organization

Registers are limited.

Therefore data must be stored in memory.

Memory is:

```text
Byte Addressable
```

Meaning:

```text
M[0] → 1 Byte
M[1] → 1 Byte
M[2] → 1 Byte
...
```

A 64-bit value occupies:

```text
8 Bytes
```

---

# Little Endian Representation

RISC-V follows:

```text
Little Endian Memory System
```

This means:

```text
Least Significant Byte
stored at
Lowest Memory Address
```

Example:

```text
M0 M1 M2 M3 M4 M5 M6 M7
```

The processor combines these bytes back into a 64-bit value when loading into registers.

---

# Load and Store Instructions

To move data between memory and registers:

### Load Double Word

```assembly
ld x8,16(x23)
```

Meaning:

- Read data from memory
- Base Address = x23
- Offset = 16
- Store result into x8

---

### Store Double Word

```assembly
sd x8,8(x23)
```

Meaning:

- Store x8 value into memory
- Address = x23 + 8

---

# Understanding RISC-V Instruction Formats

Every RISC-V instruction occupies:

```text
32 Bits
```

---

## R-Type Instructions

Operate only on registers.

Example:

```assembly
add x8,x24,x8
```

Uses:

- rd
- rs1
- rs2

---

## I-Type Instructions

Operate on:

```text
Register + Immediate
```

Example:

```assembly
ld x8,16(x23)
```

---

## S-Type Instructions

Used for memory storage.

Example:

```assembly
sd x8,8(x23)
```

---

# Hands-on Investigation Using SPIKE

SPIKE is a RISC-V ISA Simulator.

It allows execution and debugging without physical hardware.

---

## Running a Program

```bash
spike pk sum1to9
```

---

## Debug Mode

```bash
spike -d pk sum1to9
```

---

## Common Debug Commands

```bash
reg 0
```

Display Registers

```bash
step
```

Execute One Step

```bash
pc 0
```

Display Program Counter

```bash
mem 0 2020
```

Inspect Memory

---

# Assembly Programming Lab

One of the most interesting parts of Day 02 was rewriting a C program using Assembly Language.

---

## C Program

### Screenshot

<img src="./images02/1to9_custom.c.png" width="900">

---

## Assembly Program

### Screenshot

<img src="./images02/load.S.png" width="900">

---

## Understanding the Flow

The C program passes arguments using:

```text
a0
a1
```

The Assembly routine:

- Receives inputs
- Performs computation
- Returns result through a0

This demonstrated how ABI conventions are followed during function calls.

---

# Debugging the Program

To observe execution behavior:

```bash
spike -d pk 1to9_custom.o
```

### Screenshot

<img src="./images02/debug_of_1ton.png" width="900">

### Key Learning

I learned how:

- Registers change during execution
- Loops are executed
- Instructions are decoded
- Program flow can be tracked step-by-step

---

# Running Software on a RISC-V Core

The next step was understanding how software is executed on an actual processor design.

---

## PicoRV32 Core

### Screenshot

<img src="./images02/picor32.png" width="900">

PicoRV32 is a lightweight RISC-V processor core implemented in Verilog.

It provides a practical environment for executing compiled RISC-V programs.

---

# GitHub Repository Setup

The workshop provided collateral files containing processor source code and verification files.

### Screenshot

<img src="./images02/github_cloning.png" width="900">

### Activities

- Cloning repository
- Exploring project structure
- Viewing RTL files
- Understanding verification setup

---

# Converting Programs into Hex Files

Processors do not execute C code directly.

Programs must be converted into memory-loadable hexadecimal format.

---

## Hex Generation Flow

```text
C Program
      ↓
Compilation
      ↓
Object File
      ↓
Hex File
      ↓
Memory
      ↓
Processor Execution
```

---

## Conversion Process

### Screenshot

<img src="./images02/hexa_conversion_.png" width="900">

---

## Firmware Hex File

### Screenshot

<img src="./images02/firmware_hex_instructions.png" width="900">

This file contains machine instructions that will be loaded into processor memory.

---

# Understanding Actual Bitstream Storage

### Screenshot

<img src="./images02/actual_bitstream.png" width="900">

This experiment helped me understand how instructions are represented as binary values before execution.

---

# Reading Memory Contents

### Screenshot

<img src="./images02/read_memory_details.png" width="900">

This demonstrated:

- Memory initialization
- Instruction storage
- Data placement in memory

---

# Understanding the Testbench

### Screenshot

<img src="./images02/testbench_code.png" width="900">

The testbench acts as the verification environment for the processor.

It:

- Loads firmware
- Initializes memory
- Starts execution
- Monitors output

---

# Final Program Output

### Screenshot

<img src="./images02/sum1ton_custom_output.png" width="900">

Result successfully generated by the custom Assembly implementation.

---

# Key Takeaways

By the end of Day 02, I understood:

- What ABI is and why it is important
- How software accesses processor resources
- Register naming conventions in RISC-V
- Little Endian memory organization
- Load and Store instructions
- RISC-V instruction formats
- Assembly language programming
- Debugging using SPIKE
- Running software on PicoRV32
- Converting programs into firmware hex files
- Basic processor verification flow

---

# My Understanding After Day 02

Day 01 taught me how software becomes machine code.

Day 02 taught me how that machine code interacts with:

```text
Registers
Memory
ABI
Instruction Formats
Processor Hardware
```

This was my first step toward understanding processor verification and CPU design.

---

# References

- NASSCOM RISC-V MYTH Workshop
- RISC-V ISA Specification
- SPIKE Simulator
- PicoRV32 Core
- Workshop Labs and Notes

---

[⬅ Back to Repository Home](../README.md)
# Day 01 – Understanding How Software Reaches Hardware

## Overview

Day 01 focused on understanding what happens between writing a C program and executing it on a processor.

Before this session, I knew how to write programs, but I had never explored how those programs are translated into instructions that a processor can understand. Through hands-on experiments, I learned how compilation works, how assembly code is generated, and how numbers are represented internally by a processor.

---

## What I Explored

The main goal of Day 01 was to answer a simple question:

```text
How does a C program become something a processor can execute?
```

To understand this, I explored:

* RISC-V Instruction Set Architecture (ISA)
* GNU Toolchain
* Assembly Code Generation
* Binary Number Representation
* Signed and Unsigned Integers

By the end of the day, I had a much clearer understanding of how software and hardware are connected.

---

## Lab 1 – Compiling and Running a C Program

I started by creating and executing a simple C program.

### Commands Used

```bash
gcc filename.c
./a.out
```

### Output

<img src="./images01/riscv_sum1ton.png" width="900">

### What I Observed

This was my first step in connecting software with processor execution.

Although the program looked simple, it helped me realize that every high-level instruction eventually gets translated into machine instructions executed by the processor.

---

## Lab 2 – Generating RISC-V Assembly

Next, I used the RISC-V GNU Toolchain to generate assembly code from the same C program.

### Commands Used

```bash
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o filename.o filename.c
```

```bash
riscv64-unknown-elf-objdump -d filename.o | less
```

### Assembly Output

<img src="./images01/main.png" width="900">

### What I Observed

This was one of the most interesting parts of the day.

I could now see how a simple C statement is translated into multiple assembly instructions.

Instead of treating the compiler as a black box, I was able to observe the actual instructions that would eventually run on a RISC-V processor.

This helped me understand the relationship between:

```text
C Program
    ↓
Assembly Code
    ↓
Machine Instructions
    ↓
Processor Execution
```

---

## Lab 3 – Sum of Numbers from 1 to N

To better understand loops and arithmetic operations, I analyzed a program that calculates the sum of numbers from 1 to N.

### Program Output

<img src="./images01/sum1ton.png" width="900">

### What I Observed

While studying the generated assembly, I noticed how loops are implemented using branch instructions and register operations.

This was my first practical exposure to how high-level constructs such as loops are represented at the instruction level.

---

## Lab 4 – Sum of Numbers from 1 to 1000

I then repeated the experiment with a larger iteration count.

### Program Output

<img src="./images01/sum1to1000.png" width="900">

### What I Observed

Even though the program logic remained the same, analyzing larger examples helped me become more comfortable reading assembly code and identifying execution patterns.

It also highlighted how processors repeatedly execute the same instructions while updating register values.

---

## Understanding Integer Representation

Another important topic covered during Day 01 was number representation.

I explored how processors store and interpret values internally using binary representation.

Instead of focusing on mathematical definitions, I tried to understand why signed and unsigned numbers behave differently despite occupying the same number of bits.

### Key Understanding

For a 64-bit system:

```text
Unsigned Range
0 to (2^64 - 1)

Signed Range
-2^63 to (2^63 - 1)
```

This helped me understand why processors treat the Most Significant Bit (MSB) differently depending on the data type being used.

---

## Key Takeaways

By the end of Day 01, I was able to:

* Understand how software reaches hardware.
* Compile and execute C programs.
* Generate and inspect RISC-V assembly code.
* Explore the GNU Toolchain workflow.
* Understand basic processor instruction execution.
* Learn how signed and unsigned numbers are represented internally.

---

## My Reflection

The biggest takeaway from Day 01 was realizing that processors never execute C code directly.

Everything ultimately becomes instructions, registers, and binary data.

Understanding this flow provided the foundation for the rest of the workshop, where the focus gradually shifted from writing software to understanding and building the hardware that executes it.

---

[⬅ Back to Repository Home](../README.md)

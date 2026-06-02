# Day 03 – Digital Logic Design with TL-Verilog and Makerchip

## Overview

Day 03 marked my transition from software concepts to digital hardware design.

During the first two days, I explored how software is compiled and executed on a RISC-V processor. In this session, I started investigating the building blocks that make processor hardware possible.

Using TL-Verilog and Makerchip, I learned how digital systems are constructed using combinational logic, sequential logic, pipelines, and validity-based design methodologies.

---

## What I Explored

Topics covered during Day 03:

- Logic Gates
- Combinational Logic
- Sequential Logic
- Pipeline Logic
- Hierarchical Design
- TL-Verilog Fundamentals
- Makerchip Workflow
- Validity in Pipelines

This day provided the digital design foundation required for building a processor in the upcoming sessions.

---

# Exploring Digital Logic Fundamentals

One of the biggest takeaways from this session was understanding that complex hardware systems are built from very simple building blocks.

Rather than viewing a processor as a large system, I started viewing it as a collection of smaller reusable components connected together.

### Logic Gates

![AND Gate](./images03/and.png)

### Buffer

![Buffer](./images03/buffer.png)

### Inverter

![Inverter](./images03/inverter.png)

### What I Observed

- Logic gates form the foundation of every digital system.
- Complex hardware is created by combining simple logic structures.
- Even the most advanced processors ultimately rely on these basic building blocks.

---

# Understanding Combinational Logic

After exploring individual gates, I moved to combinational circuits where outputs depend only on current inputs.

This helped me understand how hardware performs immediate computations without storing previous information.

### Hierarchical Design Example

![Hierarchical Design](./images03/hierarchy_example_output.png)

### What I Observed

- Large systems are easier to build using reusable modules.
- Hierarchical design improves readability and debugging.
- Hardware development follows a modular approach similar to software engineering.

---

# Exploring Makerchip and TL-Verilog

Day 03 also introduced me to Makerchip, a browser-based environment for TL-Verilog development.

Using Makerchip, I could:

- Write TL-Verilog code
- Simulate designs
- View waveforms
- Debug circuits
- Visualize hardware behavior

One of the most valuable lessons was learning how heavily hardware development relies on waveform analysis.

---

# Lab 1 – Vector Arrays

### Output

![Vector Array Output](./images03/vector_output.png)

### What I Learned

Vector arrays make it easier to work with groups of bits rather than handling individual signals.

This concept becomes extremely important when designing datapaths, buses, and processor registers.

---

# Lab 2 – Multiplexer Design

### Output

![Multiplexer Output](./images03/multiplexer_output.png)

### What I Learned

This was my first implementation of a hardware routing element.

The experiment showed how a select signal can control data flow between multiple sources.

It also helped me understand why multiplexers appear throughout processor datapaths.

---

# Lab 3 – Calculator Design

### Output

![Calculator Program](./images03/calculator_program.png)

### What I Learned

Unlike a multiplexer, which routes information, the calculator performs actual computations.

This experiment helped me understand how arithmetic operations are implemented directly in hardware.

---

# Exploring Sequential Logic

Combinational circuits react instantly to inputs, but they cannot remember information.

Sequential circuits solve this problem by introducing state and clock-based operation.

This concept is fundamental to processor design because processors must continuously store and update information.

---

# Lab 4 – Counter Design

### Output

![Counter Output](./images03/counter_output.png)

### What I Learned

The counter demonstrated how hardware can store a value and update it across clock cycles.

This was my first practical example of state retention.

---

# Lab 5 – Fibonacci Generator

### Output

![Fibonacci Series](./images03/fibonacci_series.png)

### What I Learned

Unlike the counter, each output depended on previously generated values.

This experiment clearly demonstrated how sequential circuits can implement algorithms that require memory.

---

# Lab 6 – Sequential Calculator

### Output

![Sequential Calculator Output](./images03/sequential_calulator_output.png)

### What I Learned

The introduction of feedback allowed the calculator to reuse previous results.

This helped me understand how hardware accumulates and processes information across multiple cycles.

---

# Exploring Pipeline Logic

After understanding sequential circuits, I moved on to pipelining.

One of the biggest insights from this section was learning that performance improvements do not always come from increasing clock frequency.

Instead, work can be divided across multiple stages and processed simultaneously.

---

# Lab 7 – Pythagorean Pipeline

### Output

![Pythagorean Pipeline](./images03/pythagoras_theorem.png)

### What I Learned

Breaking a computation into stages makes large operations easier to manage and optimize.

This concept directly relates to modern processor pipelines.

---

# Lab 8 – Retiming

### Output

![Retimed Pythagorean Example](./images03/retimed_pythagorean_example.png)

### What I Learned

Retiming showed how computations can be redistributed across stages without changing functionality.

This improves timing and balances pipeline workloads.

---

# Lab 9 – Three-Stage Pipeline

### Output

![3 Stage Pipeline Output](./images03/3_pipelined_output.png)

### What I Learned

This experiment demonstrated how multiple pieces of data can occupy different stages simultaneously.

It was my first practical exposure to hardware parallelism.

---

# Understanding Validity in TL-Verilog

The final topic of Day 03 was validity.

Validity allows a pipeline to distinguish between useful and unnecessary transactions, reducing wasted activity and simplifying control logic.

This concept becomes extremely important in processor pipelines.

---

# Lab 10 – Valid Transaction Flow

### Output

![Validity Output](./images03/validity_output.png)

### What I Learned

Validity acts as a control mechanism that allows only meaningful transactions to move through the pipeline.

---

# Lab 11 – Error Comparator

### Output

![Error Comparator Output](./images03/error_comparator_output.png)

### What I Learned

Hardware design is not only about computation but also about ensuring correctness.

This experiment introduced the importance of verification and error detection mechanisms.

---

# Key Takeaways

By the end of Day 03, I was able to:

- Understand digital logic fundamentals.
- Build combinational circuits using TL-Verilog.
- Design sequential circuits with state retention.
- Explore feedback-based computation.
- Understand pipeline architecture.
- Learn retiming concepts.
- Work with validity-based design.
- Simulate and debug hardware using Makerchip.

---

# My Reflection

Day 01 taught me how software becomes machine instructions.

Day 02 taught me how those instructions interact with processor hardware.

Day 03 taught me how the hardware itself is built.

The biggest takeaway from this session was realizing that modern processors are not created as a single large design. They are built layer by layer, starting from simple logic gates and gradually evolving into complex pipelined systems.

This understanding provided the foundation required for the next stage of the workshop, where the focus shifts from digital design concepts to building an actual RISC-V processor.

---

[⬅ Back to Repository Home](../README.md)
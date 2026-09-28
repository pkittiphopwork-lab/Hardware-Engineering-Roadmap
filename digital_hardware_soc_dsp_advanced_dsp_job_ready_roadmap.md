# Job-Ready Learning Roadmap: Digital Hardware, SoC Architecture, DSP, and Advanced DSP

**Target roles:** Digital Hardware Engineer, RTL Design Engineer, FPGA Engineer, DSP/FPGA Engineer, Digital IC Design Engineer, RISC-V/CPU Engineer, SoC Design Engineer, Hardware Accelerator Engineer, SoC Architect  
**Level:** Fundamentals → Intermediate → Advanced → Production-oriented  
**Primary languages:** SystemVerilog/Verilog, Python, C/C++, MATLAB/Octave  
**Primary implementation targets:** FPGA and ASIC front-end  
**Last reviewed:** September 2026

---

# 1. Purpose of This Roadmap

This roadmap is designed for an engineer who wants to become strong in the intersection of:

```text
Digital Hardware Design
        +
RTL / FPGA
        +
DSP Architecture
        +
Advanced DSP
        +
Computer Architecture
        +
RISC-V
        +
SoC Architecture
        +
Verification
        +
Synthesis / STA / PPA
```

The target is not simply:

> “I know Verilog.”

The target is:

> “I can take an algorithm or system specification, design the architecture and microarchitecture, implement synthesizable RTL, verify it, close timing, integrate it into an SoC, evaluate PPA/performance, and debug the implementation on real hardware.”

A complete engineering flow should eventually look like this:

```text
Requirement
    │
    ▼
Algorithm / System Model
    │
    ▼
Architecture
    │
    ▼
Microarchitecture
    │
    ▼
RTL
    │
    ├─────────────┐
    ▼             ▼
Simulation      Formal
    │             │
    └──────┬──────┘
           ▼
       Synthesis
           │
           ▼
   Static Timing Analysis
           │
           ▼
     PPA Optimization
           │
           ▼
 FPGA Implementation
     or ASIC Flow
           │
           ▼
   Hardware Validation
           │
           ▼
 Performance Benchmark
```

---

# 2. Skill Depth Target

A practical target profile for DSP/FPGA/Digital-IC/RISC-V/SoC work is:

| Skill Area | Target Depth |
|---|---|
| Digital logic and RTL | Expert-level core skill |
| Microarchitecture | Expert-level core skill |
| SystemVerilog | Expert-level core skill |
| FPGA architecture | Deep |
| DSP fundamentals | Deep |
| Fixed-point DSP | Deep |
| DSP hardware architecture | Deep |
| Computer architecture | Deep |
| RISC-V ISA and microarchitecture | Deep |
| SoC interconnect / AXI | Deep |
| Cache and memory system | Deep |
| Functional verification | Deep |
| Formal verification | Strong |
| Synthesis / STA / timing closure | Strong |
| CDC / RDC | Strong |
| Python / NumPy | Strong |
| C/C++ | Working-to-strong |
| Embedded Linux / drivers | Supporting |
| ASIC physical design | Understanding + basic hands-on |
| Qt/QML | Optional/supporting |

---

# 3. Foundation Layer

## 3.1 Mathematics

You should be comfortable with:

- algebra
- complex numbers
- Euler's formula
- trigonometry
- logarithms and exponentials
- vectors and matrices
- linear algebra
- probability and statistics
- binary arithmetic
- two's complement
- modulo arithmetic
- finite precision arithmetic

Signal-processing mathematics:

- discrete-time sequences
- summation notation
- convolution
- difference equations
- complex exponentials
- Fourier series
- Fourier transform
- DFT/FFT
- z-transform

## 3.2 Programming

### Python

Learn:

- functions/classes
- NumPy
- SciPy
- matplotlib
- pytest
- binary file/data handling
- automation scripting

Use Python for:

```text
DSP reference models
fixed-point experiments
cocotb
regression
report parsing
data visualization
benchmarking
```

References:

- NumPy: https://numpy.org/doc/
- SciPy Signal: https://docs.scipy.org/doc/scipy/reference/signal.html
- pytest: https://docs.pytest.org/

### C/C++

Learn enough to:

- write hardware benchmarks
- write bare-metal/RISC-V test programs
- access memory-mapped peripherals
- understand memory alignment/cache behavior
- build Linux/FPGA control applications

Prioritize:

- pointers
- arrays
- structs
- bit manipulation
- integer widths
- alignment
- compiler/linker basics
- MMIO

---

# 4. Track A — Digital Hardware Engineering

This is the primary foundation for FPGA, Digital IC, DSP accelerators, CPU design, and SoC design.

## 4.1 Digital Logic Fundamentals

Master combinational logic:

- logic gates
- multiplexers
- decoders
- encoders
- priority encoders
- comparators
- adders/subtractors
- shifters
- barrel shifters
- ALUs
- leading-zero detectors
- population counters

Understand the mapping:

```text
Boolean function
     ↓
logic structure
     ↓
RTL
     ↓
synthesized hardware
```

### Practice

Implement:

1. parameterized mux
2. decoder
3. priority encoder
4. ripple-carry adder
5. carry-lookahead adder
6. barrel shifter
7. ALU
8. leading-zero counter
9. population counter

### Practice websites

- HDLBits: https://hdlbits.01xz.net/wiki/Main_Page
- MakerCode: https://makercode.jixiao-ai.com/
- ChipVerify: https://chipverify.com/digital-fundamentals
- VLSI Verify: https://vlsiverify.com/
- EcrioniX: https://ecrionix.org/vlsi/

## 4.2 Sequential Logic

Master:

- D flip-flops
- registers
- counters
- shift registers
- synchronous/asynchronous reset
- clock enable
- state storage

Learn timing concepts early:

- clock-to-Q
- setup
- hold
- propagation delay

## 4.3 Finite-State Machines

Learn:

- Moore vs Mealy
- binary/one-hot/Gray encoding
- state transition design
- output decode
- illegal-state recovery

Implement:

- traffic light
- UART controller
- SPI controller
- packet parser
- DMA control FSM
- AXI control state machine

Useful tutorials:

- Project F: https://projectf.io/tutorials/
- Nandland: https://nandland.com/learn-verilog/
- EcrioniX: https://ecrionix.org/vlsi/

## 4.4 Verilog/SystemVerilog for RTL

SystemVerilog should become the main RTL language.

Learn:

```text
module
logic
parameter
localparam
always_comb
always_ff
assign
case
generate
typedef
enum
struct
packed arrays
unpacked arrays
interfaces
```

Understand:

- blocking vs nonblocking
- simulation scheduling
- width extension/truncation
- signed/unsigned arithmetic
- multiple drivers
- latch inference
- synthesis-safe coding

### Production coding habits

1. Explicit widths
2. Explicit signedness
3. No accidental latches
4. Clear register boundaries
5. Clean reset strategy
6. Parameterize reusable hardware
7. Separate datapath and control where useful
8. Avoid simulation-only constructs in synthesizable RTL

References:

- ChipVerify: https://chipverify.com/verilog
- VLSI Verify: https://vlsiverify.com/
- SystemVerilog Academy: https://www.systemverilogacademy.com/
- Nandland: https://nandland.com/learn-verilog/

## 4.5 Microarchitecture Design

This is the skill that separates an RTL coder from a digital hardware designer.

For every specification ask:

```text
What is the datapath?
What is the control path?
What is the latency?
What is the required throughput?
What is the target Fmax?
What is the area/resource budget?
How is overflow handled?
How does backpressure work?
What happens on reset/error?
How will I verify it?
```

Example requirement:

```text
Y = A × B + C
```

Possible implementations:

```text
A ─┐
   ×────┐
B ─┘    +──── Y
C ──────┘
```

or pipelined:

```text
A ─┐
   × ─ FF ─┐
B ─┘       + ─ FF ─ Y
C ─────────┘
```

or resource-shared:

```text
shared multiplier
+ shared adder
+ FSM scheduler
```

Compare latency, throughput, Fmax, power, and area.

## 4.6 Pipelining

Master:

- pipeline stage
- latency
- throughput
- initiation interval
- pipeline balancing
- register placement

Know the difference:

```text
Latency = time from input to output
Throughput = how frequently new outputs are produced
```

Practice:

- non-pipelined MAC
- two-stage MAC
- pipelined adder tree
- multi-stage FIR datapath

Always report:

- Fmax
- LUT
- FF
- DSP
- latency

## 4.7 Handshake and Flow Control

Master ready/valid.

```text
Transfer = VALID && READY
```

Learn:

- backpressure
- skid buffer
- elastic pipelines
- registered ready
- stable transaction rules

Build blocks that survive randomly toggling READY.

## 4.8 FIFO Architecture

### Synchronous FIFO

Learn:

- write/read pointers
- full/empty
- occupancy
- simultaneous read/write

### Asynchronous FIFO

Learn:

- dual-clock memory
- Gray-code pointers
- pointer synchronization
- full/empty detection across domains

Projects:

1. sync FIFO
2. fall-through FIFO
3. async FIFO
4. almost-full/almost-empty FIFO

## 4.9 Clock Domain Crossing

CDC is mandatory production knowledge.

Learn:

- metastability
- MTBF
- 2-FF synchronizer
- pulse/toggle synchronizer
- handshake
- Gray code
- async FIFO

Use the correct pattern:

```text
single bit → synchronizer
pulse → pulse/toggle scheme
multi-bit control → handshake
stream → async FIFO
```

Do not use a false-path constraint as a substitute for safe CDC logic.

References:

- AMD CDC guidance: https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Clock-Domain-Crossing
- AMD async clock guidance: https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Constraining-Asynchronous-Clock-Groups-and-Clock-Domain-Crossings
- EcrioniX: https://ecrionix.org/

## 4.10 Reset Architecture / RDC

Learn:

- synchronous reset
- asynchronous assertion
- synchronous deassertion
- reset synchronizer
- reset sequencing
- reset domain crossing

Design systems with dependency-aware reset release.

## 4.11 FPGA Architecture

Understand actual FPGA resources:

- LUT
- FF
- carry chain
- distributed RAM
- BRAM
- URAM
- DSP slice
- clock buffers
- PLL/MMCM
- I/O banks
- transceivers

Understand synthesis inference:

```text
large memory → BRAM
multiply-add → DSP
counter → LUT/FF/carry
```

Inspect utilization and technology mapping rather than trusting synthesis blindly.

## 4.12 Synthesis

Understand:

```text
RTL
 ↓
elaboration
 ↓
optimization
 ↓
technology mapping
 ↓
netlist
```

Use:

- Vivado
- Yosys

References:

- Yosys: https://yosyshq.readthedocs.io/
- Synthesis primer: https://yosyshq.readthedocs.io/projects/yosys/en/stable/appendix/primer.html

## 4.13 Static Timing Analysis

Master:

```text
launch FF
  ↓ Tcq
combinational logic
  ↓ routing
capture FF
```

Learn:

- setup
- hold
- slack
- WNS
- TNS
- skew
- jitter
- clock uncertainty

Constraints:

- `create_clock`
- generated clocks
- `set_input_delay`
- `set_output_delay`
- asynchronous clock groups
- false paths
- multicycle paths

References:

- AMD UG949: https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Using-This-Guide
- Timing closure: https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Timing-Closure
- Constraints: https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Design-Constraints
- UG906: https://docs.amd.com/r/en-US/ug906-vivado-design-analysis/Implementation-Analysis-and-Closure-Techniques

## 4.14 Timing Closure

Diagnose:

- logic depth
- fanout
- routing
- DSP pipeline usage
- BRAM register usage
- reset/control fanout
- congestion

Architectural fixes:

- pipeline
- retime
- reduce mux depth
- balance trees
- localize control
- use dedicated resources
- parallelize when needed

Rule:

> Fix architecture and constraints before trying random tool directives.

## 4.15 Power-Aware RTL

Learn:

```text
Pdynamic ≈ α C V² f
```

Topics:

- switching activity
- clock gating
- operand isolation
- data gating
- memory enable
- power/performance trade-offs

ASIC awareness:

- power domains
- retention
- isolation
- UPF concepts

## 4.16 DFT Awareness

Know:

- scan chain
- ATPG
- stuck-at fault
- transition fault
- MBIST
- JTAG/boundary scan

You do not need to become a DFT specialist, but understand how RTL choices affect testability.

---
# 5. Track B — SoC Architecture

The SoC architect must understand the whole data/control path, not only the CPU.

```text
CPU
 │
 ├── Cache
 ├── MMU
 │
 ▼
Interconnect
 │
 ├── SRAM
 ├── DDR Controller
 ├── DMA
 ├── UART/SPI/I2C
 ├── Timers
 ├── Interrupt Controller
 └── Accelerators
```

## 5.1 Computer Architecture Fundamentals

Learn:

- ISA vs microarchitecture
- datapath/control path
- fetch/decode/execute/memory/writeback
- single-cycle processor
- multi-cycle processor
- pipelined processor

Build in this order:

```text
single-cycle CPU
      ↓
multi-cycle CPU
      ↓
5-stage CPU
```

Study:

- hazards
- forwarding
- stalls
- branch flush
- exceptions

## 5.2 RISC-V ISA

Start with:

- RV32I
- RV64I concepts
- instruction formats
- registers
- load/store
- branch/jump
- M extension
- C extension
- CSR
- privileged architecture

Official references:

- Ratified specifications: https://docs.riscv.org/
- Unprivileged ISA: https://docs.riscv.org/reference/isa/unpriv/unpriv-index.html
- Privileged ISA: https://docs.riscv.org/reference/isa/priv/priv-intro.html

Practice:

- write assembly
- compile C to RISC-V assembly
- inspect machine code
- implement an instruction decoder
- run code in Spike/QEMU/Ripes

Ripes:

- https://github.com/mortbopet/Ripes

## 5.3 Pipeline Design

Study a classic pipeline:

```text
IF → ID → EX → MEM → WB
```

Understand:

- RAW/WAR/WAW concepts
- forwarding
- load-use hazard
- control hazard
- valid/kill bits
- flush
- pipeline interlocks

Then progress toward:

- scoreboarding
- multi-cycle execution units
- issue control

## 5.4 Branch Architecture

Learn progressively:

1. always-not-taken
2. static prediction
3. 1-bit/2-bit predictor
4. branch target buffer
5. return-address stack
6. global/local history concepts

Measure prediction accuracy and pipeline penalty.

## 5.5 Load-Store Unit

Learn:

- address generation
- alignment
- byte enables
- sign extension
- memory responses
- outstanding requests
- store buffering concept
- load/store hazards

Advanced:

- load queue
- store queue
- memory disambiguation

## 5.6 Cache Fundamentals

Understand address decomposition:

```text
address = tag | index | offset
```

Cache types:

- direct mapped
- set associative
- fully associative

Policies:

- write-through
- write-back
- write allocate
- no-write allocate

Learn:

- hit/miss
- dirty line
- refill
- eviction
- replacement policy

Implement a small direct-mapped cache before a multi-way cache.

## 5.7 Memory Hierarchy

Reason about:

```text
Registers
 ↓
L1 Cache
 ↓
L2 / SRAM
 ↓
DDR
```

Metrics:

- latency
- bandwidth
- hit rate
- miss penalty

Do not evaluate a processor only by clock frequency.

## 5.8 MMU / TLB

For Linux-capable SoCs learn:

- virtual address
- physical address
- page
- page table
- page-table walker
- TLB
- page fault
- access permission

Study CVA6 MMU:

- https://docs.openhwgroup.org/projects/cva6-user-manual/03_cva6_design/MMU.html

## 5.9 Privilege / Exceptions / Interrupts

Learn:

- M-mode
- S-mode
- U-mode
- CSR
- trap
- exception
- timer/software/external interrupt
- `mstatus`, `mtvec`, `mepc`, `mcause`

Implement:

- illegal-instruction trap
- timer interrupt
- external interrupt

## 5.10 SoC Interconnect

Master:

- APB
- AHB concepts
- AXI4-Lite
- AXI4
- AXI4-Stream

Critical AXI ideas:

```text
independent channels
VALID/READY
burst
ID
outstanding transactions
ordering
backpressure
response
```

References:

- Arm AXI training overview: https://developer.arm.com/community/arm-community-blogs/b/announcements/posts/new-on-coursera-amba-axi-protocols-overview
- AMD common bus interfaces: https://docs.amd.com/r/en-US/ug994-vivado-ip-subsystems/Common-Internal-Bus-Interfaces

Practice progression:

1. AXI-Lite slave
2. AXI-Lite master
3. AXI-Stream source/sink
4. burst engine
5. interconnect/arbitration concepts

## 5.11 Address Map Design

Design maps deliberately:

```text
0x0000_0000  Boot ROM
0x1000_0000  UART
0x1000_1000  Timer
0x1000_2000  GPIO
0x2000_0000  SRAM
0x8000_0000  DDR
```

Learn:

- alignment
- decode windows
- reserved regions
- register ABI stability

## 5.12 Interrupt Architecture

Understand the path:

```text
Peripheral
 ↓
Interrupt Controller
 ↓
CPU
 ↓
Trap Handler
 ↓
Driver
```

Study:

- priority
- masking
- pending state
- claim/complete style controllers
- timer interrupts

## 5.13 DMA Architecture

Understand the motivation:

```text
CPU-driven copy:
peripheral → CPU → RAM

DMA:
peripheral → DMA → RAM
              ↑
         CPU controls
```

Learn:

- descriptors
- rings
- bursts
- scatter-gather
- completion interrupts
- buffer ownership
- alignment
- coherent vs non-coherent DMA
- IOMMU overview

## 5.14 Cache Coherency

Study:

- coherence problem
- private caches
- snoop concept
- MESI overview
- coherent interconnect concept

Understand why DMA and multiple masters complicate memory correctness.

## 5.15 Clock, Reset, and Power Architecture

Create a domain table:

| Domain | Example Frequency | Function |
|---|---:|---|
| CPU | 1 GHz | processor |
| AXI | 500 MHz | interconnect |
| DSP | 400 MHz | accelerator |
| peripheral | 100 MHz | low-speed I/O |

For every crossing specify:

- synchronous/asynchronous relationship
- CDC method
- reset source
- power state

Study:

- PLLs
- clock gating
- reset sequence
- power domains
- DVFS concept
- retention/isolation concepts

## 5.16 Hardware/Software Partitioning

For every function ask whether it belongs in:

```text
CPU software
DSP core
FPGA/ASIC accelerator
DMA engine
RTOS task
```

Compare:

- latency
- throughput
- power
- area
- memory bandwidth
- flexibility
- development cost

## 5.17 Performance Modeling

Learn to estimate before RTL.

Example:

```text
4 ADC channels
16 bits/sample
100 MS/s

Bandwidth = 4 × 2 × 100M = 800 MB/s
```

Then ask:

- can the stream interface sustain it?
- can DMA sustain it?
- can DDR sustain it?
- can the accelerator consume it?

Metrics:

- latency
- throughput
- IPC/CPI
- utilization
- memory bandwidth
- cache miss rate
- queue occupancy

## 5.18 Security Basics for Architects

Know at least:

- secure boot
- root of trust concept
- privilege separation
- PMP
- MMU protection
- debug access control
- signed firmware

## 5.19 Production SoC References

### CVA6

- Documentation: https://docs.openhwgroup.org/projects/cva6-user-manual/
- Design intro: https://docs.openhwgroup.org/projects/cva6-user-manual/03_cva6_design/intro.html

Use CVA6 to study an application-class RISC-V implementation with pipeline, cache/MMU, privilege, AXI, and FPGA/ASIC targets.

### Ibex

- https://github.com/lowRISC/ibex

Use Ibex to study production-quality embedded RISC-V RTL and a serious verification structure.

---

# 6. Track C — DSP Fundamentals

Learn every DSP topic twice:

```text
mathematical view
      +
hardware implementation view
```

## 6.1 Signals and Systems

Learn:

- discrete-time signals
- impulse/step
- sinusoid
- complex exponential
- periodicity
- energy/power

System properties:

- linearity
- time invariance
- causality
- stability

## 6.2 Sampling

Master:

- sampling frequency
- Nyquist criterion
- aliasing
- anti-alias filter
- reconstruction

Python exercise:

1. generate a sine
2. sample at several rates
3. compute FFT
4. observe aliasing

## 6.3 Convolution

Understand:

```text
y[n] = Σ x[k] h[n-k]
```

Interpret it as filtering/system response.

Implement:

- Python direct convolution
- optimized NumPy version
- FIR RTL implementation

## 6.4 Frequency-Domain Analysis

Learn:

- DTFT
- DFT
- FFT
- magnitude
- phase
- spectrum
- bin spacing
- leakage
- windows

Key relation:

```text
FFT bin spacing = Fs / N
```

Understand the difference between bin spacing and true resolving power.

## 6.5 Z-Transform

Learn:

- transfer function
- pole/zero
- stability
- difference equations

Connect:

```text
difference equation ↔ H(z) ↔ filter architecture
```

## 6.6 FIR Filters

Learn:

- impulse response
- linear phase
- coefficient symmetry
- window method
- equiripple concept

Hardware architectures:

### Fully parallel

```text
x → delays → multipliers → adder tree
```

### Time-multiplexed

```text
RAM + shared MAC + scheduler
```

Compare:

- area
- latency
- throughput
- Fmax

## 6.7 IIR Filters

Learn:

- feedback
- poles/zeros
- direct form I/II
- transposed forms
- biquad/SOS
- coefficient quantization
- limit cycles

Hardware concern:

> Fixed-point quantization can change stability and noise behavior.

## 6.8 FFT

Learn:

- DFT derivation
- radix-2
- butterflies
- twiddle factors
- bit reversal

Hardware architectures:

- iterative memory-based
- pipelined
- streaming

Start with 4/8/16-point implementations before 1024-point designs.

## 6.9 Fixed-Point Arithmetic

This is mandatory for FPGA/ASIC DSP.

Learn:

- Q format
- sign/integer/fraction bits
- scale
- range
- precision
- truncation
- rounding
- saturation
- wrapping
- guard bits

Workflow:

```text
floating model
   ↓
collect ranges
   ↓
choose word lengths
   ↓
fixed-point model
   ↓
quantization/error analysis
   ↓
RTL
```

References:

- DSPRelated fixed point: https://www.dsprelated.com/showarticle/1482.php
- MathWorks Fixed-Point Designer: https://www.mathworks.com/help/fixedpoint/
- Quantization topics: https://www.mathworks.com/help/fixedpoint/quantization.html
- Project F math/FPGA posts: https://projectf.io/posts/

## 6.10 Quantization Analysis

Measure:

- absolute/RMS error
- SQNR/SNR
- overflow count
- saturation count
- frequency-response error

Create word-length sweeps:

```text
12 bit
14 bit
16 bit
18 bit
20 bit
```

Plot accuracy vs resource cost.

## 6.11 DSP Verification

Use a golden model:

```text
Python / MATLAB
      │
expected data
      │
      ▼
scoreboard ← RTL output
```

Test:

- impulse
- step
- sine
- multi-tone
- white noise
- full-scale input
- overflow cases

Use:

- NumPy
- SciPy
- cocotb

cocotb: https://docs.cocotb.org/en/stable/

## 6.12 Core DSP Resources

### Academic foundation

MIT OpenCourseWare Discrete-Time Signal Processing:

https://ocw.mit.edu/courses/res-6-dtsp-discrete-time-signal-processing/

### Practical DSP

DSPRelated:

- https://www.dsprelated.com/
- https://www.dsprelated.com/freebooks/dspguide/

### Python

SciPy Signal:

https://docs.scipy.org/doc/scipy/reference/signal.html

### MATLAB/Simulink

DSP System Toolbox:

https://www.mathworks.com/help/dsp/

The toolbox documentation covers FIR/IIR, multirate DSP, adaptive filters, transforms, streaming systems, and fixed-point workflows.

---
# 7. Track D — Advanced DSP

Advanced DSP is especially useful for FPGA accelerators, SDR, communications, radar, instrumentation, and real-time signal-classification systems.

## 7.1 Multirate DSP

Learn:

- decimation
- interpolation
- rational sample-rate conversion
- anti-alias filters
- anti-imaging filters

Understand:

```text
Decimation:
filter → downsample

Interpolation:
upsample → filter
```

Practice in SciPy using:

- `decimate`
- `resample`
- `resample_poly`
- `upfirdn`

Reference:

https://docs.scipy.org/doc/scipy/reference/signal.html

## 7.2 Polyphase Filters

Polyphase decomposition is critical for efficient rate conversion and channelizers.

Study:

- polyphase decomposition
- efficient decimators
- efficient interpolators
- coefficient phase organization

Practical article:

https://www.dsprelated.com/showarticle/198.php

Build:

1. software polyphase resampler
2. time-multiplexed FPGA polyphase FIR
3. parallel version
4. compare resources/throughput

## 7.3 CIC Filters

Learn:

- integrator
- comb
- rate change
- differential delay
- passband droop
- word growth
- compensation FIR

Typical DDC chain:

```text
ADC
 ↓
Mixer/NCO
 ↓
CIC
 ↓
Compensation FIR
 ↓
Halfband FIR
 ↓
Baseband
```

Analyze internal bit growth carefully.

## 7.4 Halfband Filters

Learn why approximately every other coefficient is zero and how that reduces multiplier cost.

Use for:

- decimation-by-2
- interpolation-by-2
- multistage rate conversion

## 7.5 Digital Down Converter (DDC)

Architecture:

```text
IF/RF Samples
     ↓
NCO
     ↓
Complex Mixer
     ↓
Low-Pass Filter
     ↓
Decimator
     ↓
I/Q Baseband
```

Learn:

- phase accumulator
- sine/cosine generation
- mixer
- complex arithmetic
- CIC/FIR stages
- scaling

Build first in Python, then fixed-point, then RTL.

## 7.6 Digital Up Converter (DUC)

Architecture:

```text
I/Q Baseband
 ↓
Interpolation
 ↓
NCO/Mixer
 ↓
Digital IF
```

Learn image rejection and interpolation filter design.

## 7.7 NCO / DDS

Learn:

- phase accumulator
- tuning word
- frequency resolution
- LUT-based waveform generation
- phase truncation
- SFDR
- dither concepts

Approximate relation:

```text
Fout = FTW / 2^N × Fclk
```

## 7.8 CORDIC

Study:

- rotation mode
- vectoring mode
- iterative architecture
- fully pipelined architecture

Applications:

- sine/cosine
- atan
- magnitude
- phase
- coordinate rotation

## 7.9 Adaptive Filters

Learn:

- LMS
- NLMS
- RLS concepts
- convergence
- step-size trade-offs
- misadjustment

Applications:

- adaptive noise cancellation
- echo cancellation
- channel equalization

Implementation ladder:

```text
floating Python
 ↓
fixed-point Python
 ↓
RTL
 ↓
hardware convergence test
```

MathWorks DSP toolbox includes adaptive-filter modeling:

https://www.mathworks.com/help/dsp/

## 7.10 Filter Banks

Study:

- analysis filter bank
- synthesis filter bank
- subband decomposition
- polyphase filter bank

Applications:

- audio codecs
- SDR
- channelization
- spectral processing

## 7.11 Polyphase FFT Channelizer

Architecture:

```text
Wideband ADC
      ↓
Polyphase FIR Bank
      ↓
FFT
      ↓
N Channels
```

This is one of the best advanced DSP/FPGA portfolio projects because it combines:

- multirate DSP
- FIR
- FFT
- memory architecture
- parallelism
- fixed point
- high-throughput streaming

## 7.12 Spectral Estimation

Learn:

- periodogram
- Welch method
- window selection
- averaging
- spectral leakage
- noise floor

Advanced overview:

- autocorrelation methods
- MUSIC
- ESPRIT

## 7.13 Correlation and Matched Filtering

Learn:

- autocorrelation
- cross-correlation
- matched filter
- detection threshold

Applications:

- preamble detection
- synchronization
- radar
- ranging
- pattern recognition

## 7.14 Communications DSP

Understand:

- BPSK
- QPSK
- QAM
- FSK
- MSK
- GMSK
- OFDM

Receiver dataflow:

```text
ADC
 ↓
DDC
 ↓
AGC
 ↓
Matched Filter
 ↓
Timing Recovery
 ↓
Carrier Recovery
 ↓
Demodulation
 ↓
Decoder
```

You do not need to build the whole receiver immediately. Learn each block and its hardware implications.

## 7.15 Synchronization Algorithms

Study:

- carrier-frequency offset
- carrier phase
- symbol timing
- PLL concepts
- Costas loop
- Gardner timing detector
- early-late detector

Hardware concerns:

- loop latency
- numerical precision
- NCO resolution
- saturation

## 7.16 Beamforming

Learn:

```text
channel 0 × complex weight ─┐
channel 1 × complex weight ─┼→ sum → beam
channel 2 × complex weight ─┤
channel N × complex weight ─┘
```

Study:

- phase steering
- time delay
- complex MAC
- array geometry basics

This maps naturally to parallel FPGA architectures.

## 7.17 DSP Accelerator Architecture

For every algorithm compare these architectures.

### Fully parallel

- high throughput
- high area

### Time multiplexed

- lower area
- lower throughput

### SIMD/vectorized

- multiple packed elements per cycle

### Streaming pipeline

- one new sample/vector per cycle after pipeline fill

### Block accelerator

```text
input buffer
 ↓
compute engine
 ↓
output buffer
```

## 7.18 DSP Memory Architecture

Study:

- circular buffer
- ping-pong buffer
- multi-bank RAM
- coefficient RAM
- twiddle ROM
- line buffer
- double buffering

Example:

```text
DMA fills Buffer A
DSP computes Buffer B
then swap
```

Memory architecture often determines DSP throughput.

## 7.19 Parallelism

Learn:

- data-level parallelism
- lane parallelism
- operator parallelism
- temporal parallelism

Example:

```text
4 samples/cycle
 ↓
4-lane FIR
```

Calculate required lane count from sample rate and clock frequency.

## 7.20 Folding and Resource Sharing

Map many algorithm operations onto fewer functional units using:

- time multiplexing
- FSM scheduling
- shared multipliers
- shared adders

Compare:

- area
- throughput
- control complexity
- memory bandwidth

## 7.21 Advanced Fixed-Point DSP

Study:

- block floating point
- scaling schedules
- FFT stage growth
- headroom
- saturation placement
- accumulated quantization noise

For FFT, decide:

```text
grow width?
shift every stage?
block floating-point?
saturate?
```

## 7.22 Numerical Performance Metrics

Do not verify DSP only by exact integer equality.

Measure:

- absolute error
- relative error
- RMS error
- SNR
- SQNR
- SFDR
- EVM
- passband ripple
- stopband rejection

Generate automated benchmark reports.

---

# 8. Verification Skills Required Across All Tracks

A professional RTL designer should be able to verify their own IP even if a dedicated DV team exists.

## 8.1 Simulation

Learn:

- directed test
- self-checking test
- randomized stimulus
- reset stress
- protocol stress
- corner cases

Tools:

- Verilator
- Icarus Verilog
- Questa
- VCS
- Xcelium

## 8.2 cocotb

Architecture:

```text
Generator
 ↓
Driver
 ↓
DUT
 ↓
Monitor
 ↓
Scoreboard
```

Use cocotb especially for DSP because NumPy/SciPy can become the golden model.

Official docs:

https://docs.cocotb.org/en/stable/

## 8.3 SystemVerilog Assertions

Learn properties for:

- handshake stability
- request/response ordering
- FIFO safety
- state-machine legality
- bounded latency

Tutorials:

- VLSI Verify: https://vlsiverify.com/
- ChipVerify: https://chipverify.com/
- SystemVerilog Academy: https://www.systemverilogacademy.com/

## 8.4 Formal Verification

Learn:

```text
assume
assert
cover
```

Good targets:

- FIFO
- arbiter
- AXI-Lite control logic
- protocol bridges
- counters

SymbiYosys:

https://symbiyosys.readthedocs.io/

The current documentation covers bounded/unbounded safety checking, cover-trace generation, and liveness flows.

## 8.5 Functional Coverage

Understand:

- coverage plan
- coverpoints
- bins
- cross coverage
- coverage closure

Do not confuse code coverage with functional coverage.

---

# 9. Toolchain for Job-Ready Skills

| Domain | Learning/Open Tools | Common Industry Tools |
|---|---|---|
| RTL | SystemVerilog, Verible | SystemVerilog + internal style/lint flows |
| Simulation | Verilator, Icarus | VCS, Xcelium, Questa |
| Waveform | GTKWave | Verdi, simulator GUIs |
| FPGA | Yosys/nextpnr on supported parts | Vivado, Quartus, Libero |
| Synthesis | Yosys | Vivado, Design Compiler/Fusion Compiler, Genus |
| STA | OpenSTA | Vivado timing, PrimeTime, Tempus |
| Verification | cocotb, pytest | UVM, commercial simulators |
| Formal | SymbiYosys | Jasper, VC Formal, Questa Formal |
| DSP model | NumPy/SciPy/Octave | MATLAB/Simulink |
| CPU simulation | Spike, QEMU, Ripes | vendor/reference models + commercial tools |
| ASIC flow | OpenROAD, KLayout | Innovus, Fusion Compiler, Calibre |

Useful official references:

- Yosys: https://yosyshq.readthedocs.io/
- OpenSTA: https://opensta.readthedocs.io/
- OpenROAD: https://openroad.readthedocs.io/

---

# 10. Practice Website Directory

## 10.1 HDLBits

https://hdlbits.01xz.net/wiki/Main_Page

Best for:

- beginner/intermediate Verilog
- combinational circuits
- sequential circuits
- FSM practice

Method:

```text
solve without hints
 ↓
simulate mentally
 ↓
submit
 ↓
review errors
 ↓
re-solve later
```

## 10.2 MakerCode

https://makercode.jixiao-ai.com/

Useful for:

- digital-design exercises
- parameterized RTL
- hardware interview-style problems
- embedded/RTL challenge practice

## 10.3 ChipVerify

https://chipverify.com/

Useful for:

- digital fundamentals
- Verilog
- SystemVerilog
- UVM
- assertions
- protocols
- synthesis concepts

ChipVerify Lab:

https://lab.chipverify.com/public/pages/guide

The browser lab supports simulation, Verilator/Icarus, Yosys synthesis, OpenSTA timing analysis, and lint-style workflows.

## 10.4 VLSI Verify

https://vlsiverify.com/

Useful for:

- Verilog/SystemVerilog
- SVA
- UVM
- protocols
- quizzes/problems

## 10.5 EcrioniX

https://ecrionix.org/

Useful for:

- RTL
- STA
- CDC
- async FIFO
- low-power design
- DFT
- ASIC flow

## 10.6 Nandland

https://nandland.com/

Verilog tutorials:

https://nandland.com/learn-verilog/

Good for small, clean beginner/intermediate FPGA designs.

## 10.7 Project F

https://projectf.io/tutorials/

Posts:

https://projectf.io/posts/

Excellent for:

- FPGA math
- fixed point
- practical SystemVerilog
- algorithms
- video/graphics datapaths
- memory and pipelining

## 10.8 SystemVerilog Academy

https://www.systemverilogacademy.com/

Good for:

- SystemVerilog
- assertions
- UVM
- verification concepts

## 10.9 EDA Playground

https://www.edaplayground.com/

Use for quick browser-based experiments in:

- Verilog
- SystemVerilog
- assertions
- UVM

---

# 11. Engineering Article / Design-Reading Websites

## ZipCPU

https://zipcpu.com/

Read for:

- AXI
- formal verification
- CPU design
- FIFOs
- bus protocols
- engineering reasoning

## Project F

https://projectf.io/posts/

Read for:

- fixed-point arithmetic
- FPGA algorithms
- practical pipelines
- arithmetic hardware

## DSPRelated

https://www.dsprelated.com/

Recommended:

- Fixed point: https://www.dsprelated.com/showarticle/1482.php
- Polyphase FIR: https://www.dsprelated.com/showarticle/198.php
- Interpolator design: https://www.dsprelated.com/showarticle/1542.php
- DSP Guide: https://www.dsprelated.com/freebooks/dspguide/

## AMD Documentation

Use official documentation as production training material.

- UltraFast methodology: https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Using-This-Guide
- Timing closure: https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Timing-Closure
- Implementation analysis: https://docs.amd.com/r/en-US/ug906-vivado-design-analysis/Implementation-Analysis-and-Closure-Techniques
- CDC: https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Clock-Domain-Crossing

---

# 12. Project Ladder

Projects should become increasingly integrated.

## Project 1 — Parameterized FIFO

Requirements:

- configurable width/depth
- simultaneous read/write
- robust reset

Verification:

- randomized cocotb
- assertions
- formal safety properties

## Project 2 — Asynchronous FIFO

Skills:

- CDC
- Gray code
- dual clocks
- reset synchronization

Tests:

- unrelated random clock frequencies
- reset during traffic
- full/empty transitions

## Project 3 — AXI-Lite Register Bank

```text
AXI-Lite
 ↓
Decode
 ↓
CONTROL / STATUS / DATA / RESULT
```

Requirements:

- AW/W independent timing
- byte strobes
- backpressure
- valid responses

## Project 4 — AXI-Stream Processing Pipeline

```text
AXI-Stream
 ↓
Gain
 ↓
Saturation
 ↓
Filter
 ↓
AXI-Stream
```

Randomize backpressure.

## Project 5 — Fixed-Point FIR Accelerator

Workflow:

```text
SciPy/MATLAB design
 ↓
floating model
 ↓
fixed-point model
 ↓
RTL
 ↓
cocotb
 ↓
synthesis
 ↓
STA
```

Report:

- frequency response
- numerical error
- Fmax
- LUT/FF/DSP
- latency/throughput

## Project 6 — Digital Down Converter

```text
input samples
 ↓
NCO
 ↓
complex mixer
 ↓
CIC
 ↓
FIR
 ↓
I/Q
```

## Project 7 — FFT Accelerator

Start small then scale.

Measure:

- architecture type
- latency
- samples/cycle
- bit growth
- DSP/BRAM usage
- error

## Project 8 — RV32I CPU

Implement:

```text
IF → ID → EX → MEM → WB
```

Features:

- hazards
- forwarding
- branch handling
- basic traps

Verification:

- assembly tests
- cocotb
- RISC-V test programs

## Project 9 — Small RISC-V SoC

```text
RV32 CPU
 │
Bus
 ├ UART
 ├ Timer
 ├ GPIO
 ├ SRAM
 └ DSP accelerator
```

Write bare-metal C software to exercise every peripheral.

## Project 10 — RISC-V DSP Coprocessor

Operations:

- MAC
- dot product
- saturation
- packed arithmetic

Study CV-X-IF:

https://docs.openhwgroup.org/projects/openhw-group-core-v-xif/en/latest/

## Project 11 — Polyphase Channelizer

```text
wideband input
 ↓
polyphase filter bank
 ↓
FFT
 ↓
channel outputs
```

## Project 12 — End-to-End FPGA/SoC Signal Processor

```text
ADC/test data
 ↓
FPGA DSP
 ├ DDC
 ├ FIR
 ├ FFT
 └ feature extraction
 ↓
DMA
 ↓
CPU
 ↓
C/C++ benchmark/application
```

Report:

- throughput
- end-to-end latency
- memory bandwidth
- CPU utilization
- FPGA resource utilization
- timing
- numerical accuracy

---

# 13. Production Engineering Workflow for Every Serious IP

## Step 1 — Requirement Specification

Write:

- purpose
- interfaces
- clocks
- reset
- numeric format
- latency
- throughput
- error behavior
- target Fmax/resource budget

## Step 2 — Reference Model

DSP:

- Python/MATLAB floating point
- fixed-point model

CPU/SoC:

- ISA/reference behavior

## Step 3 — Architecture

Draw system block diagrams and dataflow.

## Step 4 — Microarchitecture

Define:

- pipeline registers
- datapath
- control FSM
- memories
- resource-sharing schedule

## Step 5 — Interface Specification

Document:

- ready/valid behavior
- register map
- transaction timing
- backpressure
- errors

## Step 6 — RTL Implementation

Write synthesizable SystemVerilog.

## Step 7 — Lint

Find:

- widths
- latches
- dead code
- multiple drivers
- suspicious signedness

## Step 8 — Simulation

Use:

- directed tests
- randomized tests
- reset stress
- edge cases

## Step 9 — Assertions

Encode protocol/safety requirements.

## Step 10 — Formal

Prove important invariants where practical.

## Step 11 — Synthesis

Check inference and utilization.

## Step 12 — Constraints

Create correct clocks/I/O relationships.

## Step 13 — STA

Analyze setup/hold and unconstrained paths.

## Step 14 — Optimization

Change architecture/RTL based on evidence.

## Step 15 — Hardware Bring-Up

Use:

- ILA
- internal status registers
- loopback modes
- debug counters

## Step 16 — Benchmark

Always report:

```text
Fmax
latency
throughput
LUT/logic
FF
BRAM/SRAM
DSP/multiplier count
power estimate
numerical accuracy
```

---
# 14. Job-Ready Exit Criteria

You are approaching job-ready status when you can demonstrate the following without relying on step-by-step tutorials.

## 14.1 Digital Hardware

Explain clearly:

- combinational vs sequential logic
- blocking vs nonblocking
- latch inference
- FSM design choices
- pipeline latency vs throughput
- ready/valid
- FIFO architecture
- async FIFO
- metastability
- CDC/RDC
- reset sequencing

Build from scratch:

- UART
- synchronous FIFO
- asynchronous FIFO
- arbiter
- register block
- AXI-Lite slave
- AXI-Stream pipeline

## 14.2 FPGA

Explain:

- LUT/FF/carry chain
- BRAM/URAM
- DSP slices
- clocking resources
- resource inference

Read and act on:

- utilization reports
- timing reports
- implementation reports
- CDC reports

Fix:

- setup violation
- excessive fanout
- unsafe CDC
- poor memory/DSP inference

## 14.3 DSP

Explain and implement:

- sampling/aliasing
- convolution
- FIR/IIR
- FFT
- interpolation/decimation
- fixed point
- saturation/rounding

Build:

- fixed-point FIR
- FFT butterfly
- CIC
- NCO
- DDC

Analyze:

- quantization error
- SNR/SQNR
- spectral behavior
- resource/accuracy trade-offs

## 14.4 SoC / RISC-V

Explain:

- ISA vs microarchitecture
- pipeline hazards
- caches
- TLB/MMU
- interrupts
- DMA
- AXI
- memory maps
- HW/SW partitioning

Build:

- simple RISC-V pipeline
- small SoC
- software-visible register map
- custom accelerator interface

## 14.5 Verification

Create:

- self-checking testbench
- cocotb driver/monitor/scoreboard
- protocol assertions
- formal safety properties
- verification plan

## 14.6 Synthesis and Timing

Explain:

- synthesis flow
- setup/hold
- slack/WNS/TNS
- clocks/generated clocks
- asynchronous clock groups
- false path vs real CDC

Be able to read a top critical path and propose an architectural fix.

---

# 15. Recommended Study Sequence

Do not study all topics randomly.

## Phase 1 — Digital Hardware Foundation

**Suggested duration:** 6–8 weeks

Study:

- Boolean logic
- combinational/sequential circuits
- SystemVerilog
- FSM
- basic timing

Practice:

- HDLBits
- MakerCode
- ChipVerify

Deliverables:

- ALU
- UART TX/RX
- FSM designs

## Phase 2 — FPGA RTL and Microarchitecture

**Suggested duration:** 6–8 weeks

Study:

- pipelining
- FIFO
- FPGA resources
- CDC/RDC
- ready/valid
- synthesis

Deliverables:

- parameterized FIFO
- async FIFO
- pipelined arithmetic unit

## Phase 3 — DSP Fundamentals

**Suggested duration:** 6–8 weeks

Study:

- sampling
- convolution
- FIR/IIR
- FFT
- z-transform

Tools:

- Python/NumPy/SciPy
- MATLAB/Octave optionally

Deliverables:

- DSP notebooks/scripts
- filter design report

## Phase 4 — Fixed-Point DSP Hardware

**Suggested duration:** 6–8 weeks

Study:

- Q formats
- range/precision
- scaling
- rounding/saturation
- hardware FIR/FFT

Deliverables:

- fixed-point FIR RTL
- cocotb golden-model verification
- synthesis/timing report

## Phase 5 — AXI and SoC Fundamentals

**Suggested duration:** 6–10 weeks

Study:

- AXI-Lite
- AXI-Stream
- memory maps
- DMA concepts
- interrupts

Deliverables:

- AXI-Lite register bank
- AXI-Stream DSP block

## Phase 6 — RISC-V / Computer Architecture

**Suggested duration:** 8–12 weeks

Study:

- RV32I
- assembly
- pipeline
- forwarding/stalls
- CSR/trap basics
- cache fundamentals

Deliverables:

- RV32I core or meaningful modification of an open core
- small SoC

## Phase 7 — Advanced DSP

**Suggested duration:** 8–12 weeks

Study:

- multirate
- polyphase
- CIC
- DDC/DUC
- adaptive DSP
- channelizers

Deliverable:

- DDC or channelizer FPGA design

## Phase 8 — Production Methodology

**Continuous**

Deepen:

- verification
- formal
- lint
- synthesis
- STA
- timing closure
- PPA
- FPGA debugging

---

# 16. Suggested 12-Month Intensive Plan

This is aggressive and assumes consistent weekly practice.

## Month 1

- Digital logic
- Verilog/SystemVerilog basics
- HDLBits
- combinational/sequential design

Project: ALU + counter + FSM

## Month 2

- UART
- SPI basics
- FIFO
- simulation/waveforms

Project: UART + FIFO

## Month 3

- pipelining
- CDC
- async FIFO
- synthesis
- timing basics

Project: async FIFO with cocotb

## Month 4

- DSP sampling/convolution
- FIR/IIR
- Python/SciPy

Project: filter-design notebook and floating-point reference model

## Month 5

- fixed-point arithmetic
- FPGA DSP resources
- FIR RTL

Project: fixed-point FIR accelerator

## Month 6

- FFT
- streaming architecture
- AXI-Stream

Project: small streaming FFT or butterfly pipeline

## Month 7

- AXI-Lite
- memory maps
- DMA concepts
- interrupts

Project: reusable AXI-Lite peripheral

## Month 8

- RISC-V RV32I
- assembly
- datapath/control

Project: single-cycle/multicycle core

## Month 9

- 5-stage pipeline
- hazard/forwarding
- CSR basics

Project: pipelined RISC-V core

## Month 10

- cache
- SoC bus
- timer/UART
- performance modeling

Project: small RISC-V SoC

## Month 11

- multirate DSP
- CIC
- DDC
- polyphase

Project: DDC

## Month 12

- formal
- advanced timing closure
- PPA optimization
- full integration

Capstone: RISC-V + DSP accelerator or FPGA DDC/channelizer subsystem

---

# 17. Weekly Engineering Routine

A productive week:

| Day | Focus |
|---|---|
| Monday | theory/specification reading |
| Tuesday | architecture + RTL |
| Wednesday | RTL / DSP model |
| Thursday | verification/formal |
| Friday | synthesis/STA/PPA |
| Saturday | integration/project |
| Sunday | documentation/refactor/review |

Recommended effort ratio:

```text
20% theory/readings
40% implementation
25% verification/debug
15% reports/documentation
```

A failed design that you debug deeply often teaches more than another tutorial.

---

# 18. How to Read Professional Specifications

Professional engineers must be comfortable reading original specifications.

Use three passes.

## Pass 1 — Architecture

Read:

- overview
- block diagram
- terminology

## Pass 2 — Behavior

Read:

- interfaces
- transactions
- timing
- corner cases

## Pass 3 — Implementation

Build one small example.

Example for AXI:

```text
read overview
 ↓
study five channels
 ↓
draw VALID/READY waveforms
 ↓
implement AXI-Lite slave
 ↓
randomize AW/W ordering
 ↓
randomize backpressure
```

Do not try to memorize entire standards.

---

# 19. Portfolio Repository Structure

Suggested organization:

```text
digital-hardware/
├── rtl/
├── tb/
├── formal/
├── constraints/
├── scripts/
└── docs/

dsp/
├── python_model/
├── fixed_point/
├── rtl/
├── tb/
└── reports/

riscv-soc/
├── cpu/
├── interconnect/
├── peripherals/
├── accelerator/
├── software/
├── tb/
└── docs/
```

Each serious project should contain:

- `README.md`
- architecture diagram
- interface/register specification
- verification plan
- synthesis report
- timing report
- benchmark results

---

# 20. Documentation You Should Learn to Write

## Architecture Specification

Include:

- goals
- block diagrams
- throughput/latency targets
- clock domains
- memory bandwidth

## Microarchitecture Specification

Include:

- FSM
- datapath
- registers
- pipeline stages
- buffer depths

## Register Specification

Include:

- offset
- field
- reset value
- access type
- semantics

## Verification Plan

Include:

- feature
- directed tests
- random tests
- assertions
- coverage

## Timing/Clock Document

Include:

- clock sources
- frequencies
- relationships
- CDC structures
- timing exceptions with justification

## DSP Numeric Design Document

Include:

- signal ranges
- Q formats
- coefficient widths
- intermediate growth
- rounding/saturation points
- error results

---

# 21. Interview Preparation Topics

For Digital Hardware/FPGA/SoC/DSP roles, be ready for the following.

## RTL

- blocking vs nonblocking
- latch inference
- synchronizers
- FIFO full/empty
- ready/valid
- FSM choices
- pipelining

## Timing

- setup/hold
- slack
- CDC vs STA
- false path
- multicycle path

## FPGA

- LUT vs BRAM vs DSP
- inferred memory
- clocking
- timing closure

## DSP

- sampling/aliasing
- FIR vs IIR
- FFT complexity
- fixed point
- quantization
- decimation/interpolation

## Computer Architecture

- hazards
- forwarding
- cache
- branch prediction
- MMU/TLB

## SoC

- AXI-Lite vs AXI4 vs AXI-Stream
- DMA
- interrupts
- memory maps
- bandwidth calculation

## Verification

- scoreboard
- assertions
- random testing
- formal vs simulation

---

# 22. Minimal Core Resource Stack

If the full list is overwhelming, prioritize these.

## Digital Hardware

1. HDLBits  
   https://hdlbits.01xz.net/wiki/Main_Page

2. ChipVerify  
   https://chipverify.com/

3. EcrioniX  
   https://ecrionix.org/

4. Project F  
   https://projectf.io/tutorials/

5. AMD UltraFast Methodology  
   https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Using-This-Guide

## SoC / RISC-V

6. RISC-V Specifications  
   https://docs.riscv.org/

7. CVA6  
   https://docs.openhwgroup.org/projects/cva6-user-manual/

8. Ibex  
   https://github.com/lowRISC/ibex

9. Arm AXI training overview  
   https://developer.arm.com/community/arm-community-blogs/b/announcements/posts/new-on-coursera-amba-axi-protocols-overview

## DSP

10. MIT Discrete-Time Signal Processing  
    https://ocw.mit.edu/courses/res-6-dtsp-discrete-time-signal-processing/

11. DSPRelated  
    https://www.dsprelated.com/

12. SciPy Signal  
    https://docs.scipy.org/doc/scipy/reference/signal.html

13. MathWorks DSP System Toolbox  
    https://www.mathworks.com/help/dsp/

14. Fixed-Point Designer  
    https://www.mathworks.com/help/fixedpoint/

## Verification

15. cocotb  
    https://docs.cocotb.org/en/stable/

16. SymbiYosys  
    https://symbiyosys.readthedocs.io/

17. VLSI Verify  
    https://vlsiverify.com/

## Synthesis / STA

18. Yosys  
    https://yosyshq.readthedocs.io/

19. OpenSTA  
    https://opensta.readthedocs.io/

20. AMD Timing Closure  
    https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Timing-Closure

---

# 23. Current Reference Notes (September 2026)

The following references were checked against currently available material.

## AMD FPGA Design Methodology

AMD UltraFast Design Methodology Guide UG949 2026.1 covers RTL design methodology, constraints, implementation, design closure, timing, power, and debug:

https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Using-This-Guide

Timing closure:

https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Timing-Closure

CDC:

https://docs.amd.com/r/en-US/ug949-vivado-design-methodology/Clock-Domain-Crossing

Implementation analysis:

https://docs.amd.com/r/en-US/ug906-vivado-design-analysis/Implementation-Analysis-and-Closure-Techniques

## RISC-V

Official ratified specification library:

https://docs.riscv.org/

The current library exposes the official unprivileged and privileged ISA specifications.

## CVA6

https://docs.openhwgroup.org/projects/cva6-user-manual/

Useful as a reference for application-class RISC-V architecture, including pipeline, MMU/cache, AXI, and FPGA/ASIC integration.

## Ibex

https://github.com/lowRISC/ibex

Useful as a public production-quality embedded RISC-V RTL and verification reference.

## HDLBits

https://hdlbits.01xz.net/wiki/Main_Page

Provides small auto-checked Verilog circuit-design exercises.

## ChipVerify

https://chipverify.com/

Contains learning material for digital fundamentals, Verilog, SystemVerilog, UVM, synthesis, assertions, protocols, and verification.

ChipVerify Lab:

https://lab.chipverify.com/public/pages/guide

The lab documentation describes browser-based simulation, Yosys synthesis, OpenSTA timing analysis, and lint-style workflows.

## EcrioniX

https://ecrionix.org/

Contains learning paths around RTL, STA, CDC, verification, DFT, low-power, and ASIC concepts.

## DSP

MIT OCW:

https://ocw.mit.edu/courses/res-6-dtsp-discrete-time-signal-processing/

DSPRelated:

https://www.dsprelated.com/

SciPy signal processing:

https://docs.scipy.org/doc/scipy/reference/signal.html

MathWorks DSP System Toolbox:

https://www.mathworks.com/help/dsp/

Fixed-point design:

https://www.mathworks.com/help/fixedpoint/

## cocotb

https://docs.cocotb.org/en/stable/

The current cocotb documentation describes Python-based HDL co-simulation with reusable/randomized testbench support and CI integration.

## Formal

SymbiYosys:

https://symbiyosys.readthedocs.io/

## Synthesis

Yosys:

https://yosyshq.readthedocs.io/

## SoC Interconnect

Arm AXI overview/training announcement:

https://developer.arm.com/community/arm-community-blogs/b/announcements/posts/new-on-coursera-amba-axi-protocols-overview

AMD common AXI interfaces:

https://docs.amd.com/r/en-US/ug994-vivado-ip-subsystems/Common-Internal-Bus-Interfaces

---

# 24. Final Professional Target

A strong Digital Hardware / SoC / DSP engineer should be able to take a problem such as:

> Design a real-time DSP subsystem integrated into a RISC-V SoC.

and execute this flow:

```text
System Requirements
        ↓
DSP Algorithm
        ↓
Python/MATLAB Golden Model
        ↓
Fixed-Point Analysis
        ↓
Architecture
        ↓
Microarchitecture
        ↓
SystemVerilog RTL
        ↓
AXI Interface
        ↓
cocotb / Assertions / Formal
        ↓
Synthesis
        ↓
STA / Timing Closure
        ↓
RISC-V SoC Integration
        ↓
C/C++ Benchmark
        ↓
FPGA Hardware Validation
        ↓
Performance / PPA / Numeric Report
```

The key career skill is not knowing isolated tools. It is being able to reason across:

```text
algorithm
→ architecture
→ RTL
→ verification
→ timing
→ memory movement
→ SoC integration
→ real hardware behavior
```

That is the level at which FPGA, Digital IC, DSP, RISC-V, and SoC design become one coherent engineering discipline.

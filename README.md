# Complete Embedded Systems, FPGA, Verification, and RISC-V SoC Learning Roadmap

**Scope:** Beginner → Intermediate → Advanced → Production-oriented  
**Languages:** C, C++, Python, Verilog/SystemVerilog, QML  
**Primary targets:** MCU bare-metal firmware, RTOS firmware, Embedded Linux/PetaLinux applications, Qt/QML HMIs, FPGA/RTL/DSP, verification, timing closure, ASIC/SoC design, and RISC-V systems  
**Last reviewed:** September 2026

---

## 0. How to Use This Roadmap

This roadmap is intentionally broader than a normal “embedded systems roadmap.” It is designed for someone who eventually wants to understand an entire digital product stack:

```text
Application / HMI
        │
        ▼
Qt/QML / C++ Application
        │
        ▼
Embedded Linux / PetaLinux
        │
        ▼
Linux Driver / RTOS Firmware
        │
        ▼
AXI / APB / DMA / Interrupts
        │
        ▼
FPGA / RTL / DSP
        │
        ▼
RISC-V / SoC Architecture
        │
        ▼
Synthesis / STA / Physical Design
        │
        ▼
ASIC / FPGA Implementation
```

You should **not** finish all of Track 1 before touching Track 6, then all of Track 6 before touching Track 7. A better approach is to build the skills in parallel:

```text
                    ┌─ Embedded C / Bare Metal ───────────┐
Programming ────────┤                                      ├─ RTOS
                    └─ C++ / Linux / Build Systems ───────┘

                    ┌─ Digital Logic ─ RTL ─ FPGA ─ DSP ───┐
Hardware ───────────┤                                      ├─ Timing
                    └─ Verification ─ Formal ─ ASIC ───────┘

                    ┌─ Linux ─ PetaLinux ─ Qt/QML ─────────┐
System Integration ─┤                                      ├─ Production
                    └─ Drivers ─ DMA ─ Memory System ──────┘

                    ┌─ ISA ─ Pipeline ─ Cache ─ Bus ───────┐
SoC ────────────────┤                                      ├─ RISC-V SoC
                    └─ Verification ─ RTL-to-GDS ──────────┘
```

### Recommended learning rule

For every major concept:

1. Learn the theory.
2. Implement the smallest possible example.
3. Simulate it.
4. Run it on hardware when possible.
5. Break it deliberately.
6. Debug the failure.
7. Add automated tests.
8. Measure performance/timing/memory.
9. Document the architecture.
10. Rebuild it in a cleaner form.

That loop is much more valuable than watching long courses without building anything.

---

# 1. Global Prerequisites

Before going deeply into the ten tracks, establish the following foundations.

## 1.1 Linux development environment

Learn:

- Ubuntu or another mainstream Linux distribution
- shell navigation
- `grep`, `find`, `sed`, `awk`
- pipes and redirection
- process management
- permissions
- environment variables
- SSH
- serial terminals
- package managers
- archives
- shell scripts
- WSL if your main PC is Windows

Important commands:

```bash
pwd
ls -la
cd
mkdir
rm
cp
mv
find
grep
less
head
tail
ps
top
htop
kill
chmod
chown
ssh
scp
tar
make
cmake
ninja
git
gdb
objdump
readelf
nm
size
```

### Tools

- Ubuntu / WSL2
- Bash
- Git
- VS Code
- Vim/GVim or Neovim if desired
- Docker
- Python virtual environments

---

## 1.2 Git and source-control discipline

Learn:

- repository
- working tree
- staging area
- commit
- branch
- merge
- rebase
- tag
- release
- `.gitignore`
- submodules
- pull requests
- code review
- semantic versioning

Practice workflow:

```text
Issue
 ↓
Branch
 ↓
Implementation
 ↓
Unit Test
 ↓
Commit
 ↓
Pull Request
 ↓
CI
 ↓
Review
 ↓
Merge
 ↓
Tag / Release
```

Do this even for personal RTL projects.

---

## 1.3 Build systems

You will encounter several build systems.

### Must know

- Make
- CMake
- Ninja
- GCC/Clang command line

### Later

- Meson
- Yocto/BitBake
- Bazel in some large projects

Useful reference:

- CMake Tutorial: https://cmake.org/cmake/help/latest/guide/tutorial/index.html
- GNU Make Manual: https://www.gnu.org/software/make/manual/
- GCC Documentation: https://gcc.gnu.org/onlinedocs/
- GDB Manual: https://sourceware.org/gdb/current/onlinedocs/gdb

---

# 2. Track 1 — Embedded C and Bare-Metal Firmware

## Goal

Be able to take a microcontroller datasheet/reference manual and create firmware that controls hardware **without depending entirely on vendor HAL code**.

Target capability:

```text
Datasheet / Reference Manual
        ↓
Memory Map
        ↓
Peripheral Registers
        ↓
C Driver
        ↓
Interrupt / DMA
        ↓
Application
```

---

## 2.1 Level 1 — C Fundamentals

Learn:

### Data representation

- binary
- hexadecimal
- signed vs unsigned integers
- two's complement
- integer overflow
- integer promotions
- fixed-width types

Prefer:

```c
#include <stdint.h>

uint8_t
uint16_t
uint32_t
uint64_t
int8_t
int16_t
int32_t
int64_t
```

Understand why:

```c
uint32_t x;
```

is often preferable to:

```c
unsigned int x;
```

for hardware-facing code.

### Core syntax

- variables
- operators
- `if`
- `switch`
- `for`
- `while`
- functions
- return values
- parameters
- scope
- storage duration

### Exercises

Write:

1. temperature threshold logic
2. state machine
3. CRC calculator
4. circular counter
5. moving average
6. bit-field parser
7. packet checksum
8. software timer

---

## 2.2 Level 2 — Pointers and Memory

This is one of the most important Embedded C subjects.

Learn:

- address
- dereference
- pointer arithmetic
- pointer to pointer
- pointer to function
- `void *`
- array decay
- pointer/array differences
- alignment
- aliasing
- lifetime

Example concept:

```text
RAM

0x20000000   0x0000002A
     ▲
     │
  pointer
```

Practice:

```c
uint32_t value = 42;
uint32_t *p = &value;

*p = 100;
```

Then understand how the exact same idea becomes hardware register access:

```c
volatile uint32_t *gpio =
    (volatile uint32_t *)0x40020000;
```

### Reference

- C language reference: https://en.cppreference.com/w/c/language
- Pointer reference: https://en.cppreference.com/w/c/language/pointer

---

## 2.3 Level 3 — Arrays, Structures, Unions, Enums

Learn:

- arrays
- multidimensional arrays
- strings
- `struct`
- padding
- alignment
- `union`
- `enum`
- `typedef`
- bit-fields and their portability limitations

Example:

```c
typedef struct
{
    uint16_t temperature;
    uint16_t humidity;
    uint8_t status;
} SensorData_t;
```

Use structures for:

- driver contexts
- configuration
- state machines
- packets
- register abstractions
- sensor data
- protocol frames

---

## 2.4 Level 4 — Embedded-Specific C Keywords

Master:

### `volatile`

Use cases:

- MMIO registers
- ISR-shared state in carefully controlled situations
- hardware status registers

Understand that:

```text
volatile ≠ atomic
volatile ≠ mutex
volatile ≠ memory barrier
volatile ≠ thread safety
```

Reference:

- https://en.cppreference.com/w/c/language/volatile

### `const`

Understand:

```c
const uint8_t *p;
uint8_t * const p;
const uint8_t * const p;
```

### `static`

Understand:

- static local storage
- file-private symbols
- internal linkage

### `extern`

Understand compilation units and shared declarations.

### `restrict`

Learn later for optimization-sensitive DSP/memory code.

---

## 2.5 Level 5 — Bit Manipulation

Master:

```c
&
|
^
~
<<
>>
```

Practice:

```c
reg |=  (1U << 5);   // set
reg &= ~(1U << 5);   // clear
reg ^=  (1U << 5);   // toggle
```

Exercises:

- extract bits `[7:4]`
- pack protocol fields
- manipulate peripheral control registers
- implement masks
- encode flags

You should eventually be able to read a datasheet register table and immediately create correct masks.

---

## 2.6 Level 6 — Compilation and Linking

Learn the flow:

```text
main.c
driver.c
startup.s
   │
   ▼
Preprocessor
   │
   ▼
Compiler
   │
   ▼
Assembler
   │
   ▼
Object Files
   │
   ▼
Linker
   │
   ▼
ELF
   │
   ├─ HEX
   └─ BIN
```

Learn:

- preprocessor
- assembler
- linker
- linker script
- ELF
- sections
- symbols
- relocation

Understand:

```text
.text
.rodata
.data
.bss
.stack
.heap
.vector_table
```

Use:

```bash
arm-none-eabi-size firmware.elf
arm-none-eabi-nm firmware.elf
arm-none-eabi-objdump -d firmware.elf
arm-none-eabi-readelf -S firmware.elf
```

### Excellent article series

Memfault “Zero to main()” series:

- Bootloader article: https://interrupt.memfault.com/blog/how-to-write-a-bootloader-from-scratch
- Browse the wider Interrupt/Memfault archive: https://interrupt.memfault.com/

---

## 2.7 Level 7 — MCU Architecture

Learn Cortex-M concepts:

- registers
- stack pointer
- program counter
- exception model
- vector table
- NVIC
- SysTick
- privilege
- MSP/PSP
- exception stack frame

Reference:

- Arm CMSIS-Core: https://arm-software.github.io/CMSIS_6/latest/Core/index.html

CMSIS exposes standardized access to:

- NVIC
- SysTick
- MPU
- FPU
- cache-related functions on supported cores
- core registers

---

## 2.8 Level 8 — Memory-Mapped I/O

Understand:

```text
CPU Load/Store
      │
      ▼
Address Decoder
      │
      ▼
Peripheral Register
      │
      ▼
GPIO/UART/SPI/etc.
```

Practice implementing a register:

```c
#define REG32(addr) (*(volatile uint32_t *)(addr))
```

Then stop using simplistic macros everywhere and move toward clean device-header or driver abstractions.

---

## 2.9 Level 9 — Bare-Metal Peripheral Drivers

Recommended order:

```text
GPIO
 ↓
Timer
 ↓
UART
 ↓
External Interrupt
 ↓
PWM
 ↓
ADC
 ↓
SPI
 ↓
I2C
 ↓
DMA
 ↓
Watchdog
```

For each peripheral:

1. read block diagram
2. find clock source
3. enable peripheral clock
4. configure pins
5. configure registers
6. implement polling mode
7. implement interrupt mode
8. implement DMA when relevant
9. implement error handling
10. test corner cases

---

## 2.10 Level 10 — Interrupts

Learn:

- ISR
- interrupt vector
- priority
- nesting
- latency
- critical section
- race condition
- interrupt-safe code
- deferred processing

Pattern:

```text
Hardware IRQ
   │
   ▼
ISR
   │
   ├─ clear interrupt
   ├─ capture minimal state
   └─ signal foreground processing
           │
           ▼
      main loop / task
```

Rule:

> Keep ISR work bounded and predictable.

---

## 2.11 Level 11 — Firmware Architecture

Learn:

- super loop
- cooperative scheduler
- finite-state machines
- event-driven firmware
- driver/HAL/application layering
- callback patterns
- dependency inversion
- configuration structures

Suggested architecture:

```text
Application
    │
Services
    │
Device Drivers
    │
MCU Drivers
    │
CMSIS
    │
Hardware
```

### UML useful for Embedded C

Focus on:

- State Machine Diagram
- Sequence Diagram
- Activity Diagram
- Component Diagram
- Timing Diagram

Use UML to model firmware before implementation.

---

## 2.12 Level 12 — Defensive Embedded C

Learn:

- undefined behavior
- buffer overflows
- integer overflow
- null pointers
- dangling pointers
- bounds checking
- watchdog recovery
- reset reason logging
- assertions
- fail-safe defaults

Explore:

- MISRA C
- CERT C
- static analysis

References:

- MISRA: https://misra.org.uk/
- SEI CERT C: https://wiki.sei.cmu.edu/confluence/display/c/SEI+CERT+C+Coding+Standard

---

## 2.13 Tools for Track 1

### Free/Open

- GCC Arm Embedded
- Clang
- GDB
- OpenOCD
- VS Code
- CMake
- Ninja
- cppcheck
- clang-tidy
- Unity
- CMock
- Ceedling

### Vendor/Industry

- STM32CubeIDE
- STM32CubeMX
- Keil MDK
- IAR Embedded Workbench
- SEGGER Embedded Studio
- J-Link
- Lauterbach TRACE32

---

## 2.14 Practice Websites

### Embedded-specific

- EWskills — https://www.ewskills.com/
  - Embedded C
  - C++ for Embedded Systems
  - microcontrollers
  - Verilog
- MakerCode — https://makercode.jixiao-ai.com/
  - RTL
  - Embedded C
  - hardware interview practice
- Microsoft MakeCode Maker — https://maker.makecode.com/
  - useful for beginner MCU experimentation, although it is not a substitute for C/register-level work

### General C practice

- Exercism C — https://exercism.org/tracks/c
- LeetCode — https://leetcode.com/problemset/
- HackerRank C — https://www.hackerrank.com/domains/c

Use LeetCode primarily for:

- arrays
- pointers
- data structures
- algorithm discipline

Do **not** use LeetCode as your primary embedded-systems curriculum.

---

## 2.15 Track 1 Projects

### Beginner

1. GPIO LED/button without HAL
2. timer-based LED scheduler
3. UART command interface
4. PWM LED dimmer
5. ADC voltage monitor

### Intermediate

6. UART interrupt + ring buffer
7. SPI sensor driver
8. I2C sensor driver
9. DMA UART receiver
10. bootloader

### Advanced

11. bare-metal data acquisition framework
12. fault logger with watchdog
13. flash-based configuration system
14. MCU firmware update protocol
15. register-level driver library

### Exit criteria

You are ready for Track 2 when you can explain:

- why `volatile` is needed for MMIO
- why it does not make shared data thread-safe
- stack vs heap
- `.data` vs `.bss`
- interrupt latency
- startup code
- linker script
- MMIO
- DMA concept
- race condition
- ring buffer

---

# 3. Track 2 — Modern Embedded C/C++, RTOS, and Memory Systems

## Goal

Move from “firmware that works” to firmware that is:

- modular
- concurrent
- deterministic
- testable
- memory-aware
- production maintainable

---

## 3.1 Modern C++ for Embedded

Do not begin by learning every desktop C++ feature.

Prioritize:

- references
- classes
- constructors/destructors
- RAII
- namespaces
- templates
- `constexpr`
- `enum class`
- `std::array`
- type safety
- move semantics
- smart pointers where appropriate
- compile-time programming
- `std::span` if toolchain supports it
- atomics

Understand the trade-offs of:

- exceptions
- RTTI
- dynamic allocation
- STL containers
- virtual functions

In safety/resource-constrained firmware, these features may be restricted rather than universally forbidden.

Useful:

- cppreference C++: https://en.cppreference.com/w/cpp
- Embedded Artistry: https://embeddedartistry.com/

---

## 3.2 Memory Allocation Strategies

Learn:

- static allocation
- stack allocation
- heap allocation
- pools
- arenas
- slab allocators
- fixed-block allocators
- object pools

Understand fragmentation.

For hard real-time code, dynamically allocating memory in the time-critical path is often undesirable.

Embedded Artistry memory allocator example:

- https://embeddedartistry.com/blog/2017/02/15/implementing-malloc-first-fit-free-list/

Embedded C++ without relying heavily on heap:

- https://embeddedartistry.com/blog/2020/02/17/qa-how-can-i-use-c-on-embedded-projects-when-i-cant-use-the-heap/

Explore the Embedded Template Library:

- https://www.etlcpp.com/

---

## 3.3 RTOS Fundamentals

Start with FreeRTOS, then explore Zephyr.

Learn:

- task/thread
- scheduler
- preemptive scheduling
- cooperative scheduling
- priority
- tick
- context switch
- task states
- blocking
- delay
- queue
- semaphore
- mutex
- event group
- notification
- software timer
- work queue

FreeRTOS documentation:

- https://www.freertos.org/Documentation/
- queues: https://www.freertos.org/Documentation/02-Kernel/02-Kernel-features/02-Queues-mutexes-and-semaphores/01-Queues
- mutexes: https://www.freertos.org/Documentation/02-Kernel/02-Kernel-features/02-Queues-mutexes-and-semaphores/04-Mutexes

Zephyr:

- https://docs.zephyrproject.org/latest/
- kernel services: https://docs.zephyrproject.org/latest/kernel/services/index.html
- threads: https://docs.zephyrproject.org/latest/kernel/services/threads/index.html

---

## 3.4 RTOS Scheduling

Understand:

```text
Ready
Running
Blocked
Suspended
```

Study:

- fixed-priority scheduling
- round robin
- starvation
- priority inversion
- priority inheritance
- deadline concepts
- WCET
- CPU utilization
- response time

Do not confuse:

```text
High priority
```

with:

```text
Important code
```

Priority should reflect timing requirements and dependency structure.

---

## 3.5 Synchronization

Learn differences between:

### Mutex

Resource ownership.

### Binary semaphore

Event synchronization.

### Counting semaphore

Resource/event count.

### Queue

Transfer data with synchronization.

### Event flags

Represent several asynchronous conditions.

### Atomic

Small lock-free state changes.

---

## 3.6 Race Conditions and Memory Ordering

Study:

- data race
- critical section
- atomic operation
- compiler reordering
- CPU reordering
- memory barriers
- acquire/release
- sequential consistency

C11 atomics reference:

- https://en.cppreference.com/w/c/language/atomic

Never use `volatile` as a replacement for atomics/mutexes.

---

## 3.7 MCU Memory System

Learn:

```text
CPU
 │
 ├─ I-Cache
 ├─ D-Cache
 │
 ▼
Bus Matrix
 │
 ├─ Flash
 ├─ SRAM
 ├─ External SDRAM
 └─ Peripherals
```

Subjects:

- memory map
- MPU
- cache
- cache line
- TCM
- SRAM banks
- DMA access
- bus masters
- cache maintenance
- alignment

CMSIS MPU reference:

- https://arm-software.github.io/CMSIS_6/latest/Core/group__mpu__functions.html

CMSIS cache reference:

- https://arm-software.github.io/CMSIS_6/latest/Core/group__cache__functions__m7.html

---

## 3.8 DMA + Cache Coherency

This is a major transition from beginner to advanced embedded engineering.

Scenario:

```text
CPU writes buffer
       │
       ▼
D-Cache contains new data
       │
       X
RAM still contains old data
       │
       ▼
DMA reads RAM
```

Possible failure:

> DMA transmits stale data.

You need to understand:

- clean cache
- invalidate cache
- cache line alignment
- coherent vs non-coherent DMA
- ownership transitions

Practice this explicitly on Cortex-M7 or Cortex-A systems.

---

## 3.9 MPU and Protection

Learn:

- privileged/unprivileged execution
- read/write/execute permissions
- no-execute regions
- stack protection
- peripheral access restrictions

Goal:

```text
Task A → memory region A
Task B → memory region B
Driver → peripherals
```

instead of every module being allowed to corrupt everything.

---

## 3.10 Modern Embedded Architecture

Build architecture like:

```text
                 Application
                     │
              ┌──────┴──────┐
              │             │
        Control Task   Communication Task
              │             │
           Queue          Queue
              │             │
              └──────┬──────┘
                     │
                  Drivers
                     │
                  Hardware
```

Learn:

- message passing
- ownership
- dependency injection
- interfaces
- state machine per subsystem
- service layer
- hardware abstraction
- unit-testable drivers

---

## 3.11 Debugging RTOS Systems

Learn to diagnose:

- stack overflow
- deadlock
- priority inversion
- watchdog reset
- hard fault
- queue overflow
- heap corruption
- task starvation

Tools:

- FreeRTOS trace hooks
- SEGGER SystemView
- Percepio Tracealyzer
- J-Link
- SWO/ITM
- GDB
- core dumps where available

---

## 3.12 Projects

### Project A — RTOS Sensor Node

```text
Sensor Task
     │
   Queue
     ▼
Processing Task
     │
   Queue
     ▼
UART Task
```

Requirements:

- no blocking inside ISR
- watchdog
- error status
- stack usage measurements

### Project B — RTOS Data Acquisition

```text
Timer
 ↓
ADC + DMA
 ↓
ISR
 ↓
Processing Task
 ↓
FIR
 ↓
Logging Task
```

### Project C — RTOS Motor Controller

Tasks:

- sensor
- PID
- communication
- fault monitor
- logger

### Exit criteria

Explain:

- mutex vs semaphore
- ISR-to-task signaling
- priority inversion
- stack sizing
- atomic vs volatile
- cache/DMA problem
- MPU
- static vs dynamic allocation
- deterministic design

---

# 4. Track 3 — C/C++ Applications for PetaLinux / Embedded Linux / Memory Systems

## Goal

Build production-style applications that run on an application processor such as Cortex-A/RISC-V Linux and communicate efficiently with FPGA or external hardware.

PetaLinux is AMD's embedded Linux SDK for FPGA-based SoC systems and includes Yocto-based tooling. Current reference documentation:

- AMD PetaLinux UG1144: https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Introduction

PetaLinux supports custom C and C++ applications:

- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Adding-Custom-Applications

---

## 4.1 Linux Fundamentals

Learn:

- processes
- threads
- virtual memory
- userspace/kernel space
- filesystem
- permissions
- shell
- services
- systemd
- `/proc`
- `/sys`
- `/dev`
- environment
- signals

Understand:

```text
Application
    │
System Call
    │
Kernel
    │
Driver
    │
Hardware
```

---

## 4.2 POSIX C/C++

Learn:

### File descriptors

```c
open()
close()
read()
write()
```

### Device control

```c
ioctl()
```

### Memory mapping

```c
mmap()
munmap()
```

### Event handling

```c
select()
poll()
epoll()
```

### Threads

```c
pthread_create()
pthread_join()
pthread_mutex_*
pthread_cond_*
```

### IPC

- pipe
- FIFO
- Unix domain socket
- shared memory
- message queue
- semaphore

### Networking

- TCP
- UDP
- sockets
- multicast where relevant

---

## 4.3 Linux Memory Model

Understand:

```text
User Virtual Address
        │
        ▼
Page Table
        │
        ▼
Physical Address
        │
        ▼
DDR
```

Study:

- virtual memory
- physical memory
- page
- page table
- MMU
- TLB
- page fault
- anonymous mapping
- file-backed mapping
- shared mapping
- copy-on-write

---

## 4.4 Memory-Mapped FPGA Access

Begin with UIO for suitable prototype/simple device cases:

```text
FPGA AXI-Lite IP
      │
      ▼
generic-uio
      │
      ▼
/dev/uio0
      │
     mmap()
      │
      ▼
C/C++ Application
```

Linux UIO documentation:

- https://docs.kernel.org/driver-api/uio-howto.html

Use UIO to learn the relationship between:

- physical AXI address
- device tree
- Linux driver
- userspace virtual address
- `mmap`

---

## 4.5 Device Tree

Learn:

- node
- property
- `compatible`
- `reg`
- `interrupts`
- clocks
- DMA channels
- aliases
- phandles
- bindings

Concept:

```dts
accelerator@a0000000 {
    compatible = "vendor,my-accelerator";
    reg = <0x0 0xa0000000 0x0 0x10000>;
};
```

Linux DT overview:

- https://docs.kernel.org/devicetree/usage-model.html

PetaLinux System Device Tree:

- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Setting-Up-the-System-Devicetree

---

## 4.6 Linux Kernel Modules

Learn:

- module init/exit
- kernel logging
- module parameters
- character devices
- file operations
- sysfs
- procfs where appropriate

Then move to platform drivers.

---

## 4.7 Platform Drivers

Important for FPGA IP integrated into SoCs.

Learn:

- `platform_device`
- `platform_driver`
- `probe()`
- `remove()`
- resource acquisition
- IRQ
- MMIO
- DMA
- device tree matching

Reference:

- https://docs.kernel.org/driver-api/driver-model/platform.html

Architecture:

```text
Device Tree
    │
compatible
    │
    ▼
Platform Driver
    │
    ├─ MMIO
    ├─ IRQ
    └─ DMA
```

---

## 4.8 Linux DMA

Learn the difference between:

```text
CPU virtual address
CPU physical address
DMA/bus address
```

Do not assume they are identical.

Linux DMA documentation:

- https://docs.kernel.org/core-api/dma-api.html
- https://docs.kernel.org/6.9/core-api/dma-api-howto.html

Topics:

- `dma_alloc_coherent`
- streaming DMA
- `dma_map_single`
- `dma_unmap_single`
- scatter-gather
- DMA directions
- non-coherent platforms
- cache ownership
- IOMMU

---

## 4.9 AXI DMA Data Path

Production-style concept:

```text
ADC
 ↓
FPGA DSP
 ↓
AXI-Stream
 ↓
AXI DMA
 ↓
DDR
 ↓
C/C++ Application
 ↓
Network / Storage / GUI
```

Learn:

- descriptors
- ring buffers
- interrupt completion
- buffer ownership
- zero-copy concepts
- cache coherence
- throughput
- latency

---

## 4.10 Build and Deployment

Learn:

- cross compilation
- sysroot
- SDK
- toolchain file
- CMake cross-build
- shared libraries
- static libraries
- RPATH
- package installation

PetaLinux custom application flow:

```bash
petalinux-create apps --template c++ --name myapp --enable
petalinux-build
```

Reference:

- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Adding-Custom-Applications
- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Building-User-Applications

---

## 4.11 Yocto / BitBake

PetaLinux users should eventually understand the underlying Yocto model.

Learn:

- layer
- recipe
- `.bb`
- `.bbappend`
- machine
- distro
- image
- package
- task
- sstate
- `devtool`

Yocto docs:

- https://docs.yoctoproject.org/
- devtool reference: https://docs.yoctoproject.org/ref-manual/devtool-reference.html

Do not treat PetaLinux commands as magic wrappers forever.

---

## 4.12 Linux Performance/Debug Tools

Learn:

```bash
gdb
gdbserver
strace
ltrace
perf
top
htop
vmstat
iostat
dmesg
journalctl
devmem
hexdump
readelf
objdump
nm
ldd
```

Advanced:

- ftrace
- trace-cmd
- perf events
- flame graphs
- eBPF concepts

---

## 4.13 Production C/C++ Architecture

Recommended:

```text
Application
    │
Business / Control Logic
    │
Device Service
    │
Hardware Abstraction Library
    │
Linux Driver / UIO
    │
FPGA
```

Avoid spreading raw:

```c
mmap()
ioctl()
register offsets
```

through the UI and application code.

Create a clean hardware API.

---

## 4.14 Projects

### Beginner

1. C app reading `/sys`
2. UART C++ application
3. TCP server
4. multithreaded producer-consumer

### Intermediate

5. UIO AXI-Lite register controller
6. interrupt-driven UIO application
7. device tree overlay/custom node
8. custom PetaLinux recipe

### Advanced

9. custom platform driver
10. DMA-based acquisition pipeline
11. C++ shared library wrapping FPGA
12. service daemon + IPC
13. watchdog and fault recovery
14. systemd service packaging

### Exit criteria

You can explain:

- userspace vs kernel
- virtual vs physical vs DMA address
- `mmap`
- `ioctl`
- UIO
- device tree
- kernel module
- platform driver
- DMA
- cache coherency
- cross compilation
- Yocto recipe

---

# 5. Track 4 — Qt/QML for PetaLinux / Embedded Linux / Memory-Aware UI Systems

## Goal

Build a production embedded HMI where QML provides the UI and C++ provides the backend/system logic.

Recommended architecture:

```text
QML / Qt Quick
      │
Signals / Slots / Properties
      │
C++ Backend
      │
Hardware/Service Layer
      │
Linux Driver
      │
FPGA / Device
```

Qt explicitly supports C++ and QML integration:

- https://doc.qt.io/qt-6/qtqml-cppintegration-overview.html

Qt Embedded Linux platform overview:

- https://doc.qt.io/qt-6.8/embedded-linux.html

---

## 5.1 C++ Prerequisites

Before serious Qt:

- class
- inheritance
- composition
- constructor/destructor
- RAII
- reference
- smart pointer
- templates
- lambda
- STL
- thread safety
- move semantics

---

## 5.2 Qt Core

Learn:

- `QObject`
- meta-object system
- signals
- slots
- properties
- parent/child ownership
- event loop
- timers
- `QThread`
- worker-object pattern
- `QFile`
- networking
- serial port if used
- models

Important principle:

> Do not perform long blocking operations in the GUI thread.

---

## 5.3 QML Fundamentals

Learn:

- item hierarchy
- properties
- property binding
- signals
- handlers
- anchors
- layouts
- states
- transitions
- animations
- reusable components

Start with:

- rectangles
- text
- buttons
- pages
- lists
- settings panel

---

## 5.4 Qt Quick Controls

Build:

- navigation
- menu
- dialog
- tabs
- sliders
- switches
- gauges
- custom controls

Create a consistent UI design system rather than styling every component independently.

---

## 5.5 C++ ↔ QML Integration

Learn:

- `Q_PROPERTY`
- `Q_INVOKABLE`
- registered QML types
- singleton types
- context properties
- signals/slots
- model/view architecture

Pattern:

```text
QML Button
    │
    ▼
C++ Controller
    │
    ▼
Hardware Service
    │
    ▼
FPGA
```

Do not let QML contain low-level device access.

---

## 5.6 Model/View for Real Applications

Learn:

- `QAbstractListModel`
- `QAbstractTableModel`
- roles
- proxy model
- asynchronous data updates

Use this for:

- log tables
- sensor lists
- alarms
- device lists
- measurement history

---

## 5.7 Multithreading

Architecture:

```text
GUI Thread
   │
   ├─ renders QML
   └─ receives signals
        ▲
        │ queued signal
        │
Worker Thread
   │
   ├─ DMA read
   ├─ network
   └─ processing
```

Learn:

- event loops per thread
- queued connections
- worker object
- mutex where needed
- lock-free/ring-buffer design for high-rate data

---

## 5.8 High-Rate Data and Plotting

For waveform/spectrum applications:

Avoid:

```text
one QML signal per sample
```

Instead:

```text
DMA block
 ↓
C++ buffer
 ↓
decimation / aggregation
 ↓
UI update at 30–60 Hz
```

Keep acquisition rate independent from display refresh rate.

Example:

```text
ADC: 10 MS/s
FPGA: processing
DMA: blocks of 8192 samples
C++: 100–1000 blocks/s
UI: 30–60 frames/s
```

---

## 5.9 Graphics Stack

Understand:

- framebuffer
- DRM/KMS
- EGL
- OpenGL ES
- Wayland
- compositor
- EGLFS
- LinuxFB

Qt Embedded Linux supports several platform plugins such as EGLFS, LinuxFB, and Wayland depending on configuration.

Reference:

- https://doc.qt.io/qt-6.8/embedded-linux.html

---

## 5.10 Memory and Performance

Measure:

- RAM usage
- texture memory
- object count
- startup time
- frame time
- dropped frames
- CPU/GPU utilization

Avoid:

- creating/destroying thousands of QML objects repeatedly
- excessive JavaScript loops
- copying large sample arrays repeatedly
- updating bindings at sample rate

---

## 5.11 Build and Deployment

Learn:

- Qt + CMake
- cross compilation
- sysroot
- toolchain
- QML modules
- deployment
- plugins
- fonts
- resources

Build flow:

```text
Host PC
  │
Qt/CMake
  │
Cross Compiler
  │
PetaLinux SDK/sysroot
  │
ARM64 Binary
  │
Target RootFS
```

---

## 5.12 Projects

### Beginner

1. Qt temperature dashboard
2. settings page
3. serial monitor
4. system status dashboard

### Intermediate

5. QML + C++ backend
6. threaded sensor reader
7. real-time chart
8. configuration manager
9. alarm/event model

### Advanced

10. FPGA spectrum analyzer UI
11. oscilloscope-like HMI
12. vibration-monitoring UI
13. multi-process HMI + backend service
14. startup/recovery/error-state UX

### Exit criteria

You can design:

```text
QML
 ↓
C++ Backend
 ↓
Threaded Data Service
 ↓
Linux Driver
 ↓
FPGA
```

without blocking the UI or duplicating low-level hardware logic in QML.

---

# 6. Track 5 — End-to-End Hardware Production Flow: PetaLinux + RTOS + FPGA + RTL

## Goal

Understand how a real embedded/FPGA product moves from requirements to field deployment.

This is not one tool. It is a lifecycle.

---

## 6.1 Phase 1 — Requirements

Define:

- functional requirements
- performance
- latency
- throughput
- power
- safety
- security
- boot time
- reliability
- interfaces
- environmental constraints
- update requirements
- manufacturing requirements

Example:

```text
Input:
4 ADC channels
1 MS/s/channel
16 bit

Processing:
FIR + FFT + RMS

Output:
Ethernet
Touchscreen

Latency:
< 20 ms
```

---

## 6.2 Phase 2 — HW/SW Partitioning

Ask:

### Put in FPGA if:

- deterministic cycle-level timing
- very high parallelism
- high-rate streaming
- custom protocol timing
- heavy fixed-point DSP
- many operations per sample

### Put in MCU/RTOS if:

- hard/firm real-time control
- low-level control loops
- safety supervisor
- fast boot
- deterministic peripheral handling

### Put in Linux if:

- networking
- filesystem
- UI
- configuration
- complex protocols
- databases
- update manager
- high-level orchestration

Example:

```text
                  Product
                     │
         ┌───────────┼───────────┐
         │           │           │
       FPGA         RTOS       Linux
         │           │           │
      DSP/DMA     Safety       UI/API
     streaming    control      storage
```

---

## 6.3 Phase 3 — Interface Specification

Before coding, document:

- address map
- register map
- reset behavior
- clock domains
- interrupt semantics
- DMA ownership
- packet format
- error codes

Example register table:

| Offset | Name | Access | Description |
|---:|---|---|---|
| `0x00` | CONTROL | RW | start/reset |
| `0x04` | STATUS | RO | busy/error |
| `0x08` | LENGTH | RW | block length |
| `0x0C` | IRQ_STATUS | RW1C | interrupt flags |

This specification becomes the contract between RTL and software.

---

## 6.4 Phase 4 — RTL Development

Process:

```text
Specification
 ↓
Microarchitecture
 ↓
RTL
 ↓
Lint
 ↓
Simulation
 ↓
Formal
 ↓
Synthesis
 ↓
Timing
```

Required artifacts:

- block diagram
- interface timing
- register spec
- reset spec
- CDC plan
- verification plan

---

## 6.5 Phase 5 — FPGA Integration

Typical AMD flow:

```text
RTL/IP
  ↓
Vivado
  ↓
Block Design
  ↓
AXI
  ↓
Synthesis
  ↓
Implementation
  ↓
Bitstream
  ↓
XSA
  ↓
PetaLinux / Vitis
```

Check:

- pin constraints
- clock constraints
- reset
- CDC
- AXI address map
- interrupts
- DMA
- ILA probes

---

## 6.6 Phase 6 — RTOS Integration

If there is an auxiliary MCU or RPU:

- startup
- drivers
- scheduler
- IPC
- watchdog
- heartbeat
- error handling

Possible SoC architecture:

```text
Cortex-A / Linux
      │
 OpenAMP/RPMsg
      │
Cortex-R / RTOS
      │
 Safety / Real-time IO
```

Study:

- OpenAMP: https://www.openampproject.org/
- RPMsg concepts
- shared memory
- interprocessor interrupts

---

## 6.7 Phase 7 — Linux Integration

Build:

- bootloader
- kernel
- device tree
- root filesystem
- applications
- services
- drivers

PetaLinux current reference:

- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Introduction

---

## 6.8 Phase 8 — Application/UI

```text
QML
 ↓
C++
 ↓
Hardware Service
 ↓
Driver
 ↓
AXI/DMA
 ↓
FPGA
```

Define stable APIs so the GUI does not depend directly on register offsets.

---

## 6.9 Phase 9 — Verification Strategy

Use multiple layers:

```text
RTL unit simulation
       │
       ▼
Formal properties
       │
       ▼
IP integration simulation
       │
       ▼
FPGA hardware test
       │
       ▼
Driver test
       │
       ▼
Application test
       │
       ▼
System test
```

---

## 6.10 Phase 10 — CI/CD

Automate:

- formatting
- lint
- C/C++ build
- unit tests
- cocotb regressions
- Verilator lint
- synthesis smoke tests
- formal checks
- documentation build
- Linux application build

Tools:

- GitHub Actions
- GitLab CI
- Jenkins
- Docker

---

## 6.11 Phase 11 — Hardware Bring-Up

Checklist:

```text
Power rails
 ↓
Clock
 ↓
Reset
 ↓
JTAG
 ↓
Boot ROM
 ↓
DDR
 ↓
UART console
 ↓
Ethernet
 ↓
FPGA configuration
 ↓
Peripheral tests
```

Use:

- oscilloscope
- DMM
- logic analyzer
- JTAG
- UART
- ILA
- VIO

---

## 6.12 Phase 12 — Manufacturing Test

Create:

- boundary tests
- board self-test
- serial number programming
- calibration
- Ethernet test
- memory test
- FPGA loopback
- sensor sanity test
- current consumption check
- test logs

Design-for-test should begin during architecture, not after the PCB arrives.

---

## 6.13 Phase 13 — Release and Field Update

Linux:

- package versioning
- A/B update
- rollback
- signed images
- OTA
- recovery mode

Investigate:

- RAUC — https://rauc.io/
- SWUpdate — https://sbabic.github.io/swupdate/

RTOS:

- MCUboot — https://docs.mcuboot.com/

---

## 6.14 Production Capstone

Build a complete product such as:

### FPGA Vibration Analyzer

```text
Accelerometer / ADC
        ↓
FPGA
├─ decimation
├─ FIR
├─ FFT
└─ RMS
        ↓
AXI DMA
        ↓
DDR
        ↓
Embedded Linux
├─ C++ acquisition service
├─ data logger
├─ network API
└─ Qt/QML HMI
```

Include:

- RTOS supervisor if available
- watchdog
- boot/recovery
- logging
- CI
- test plan
- manufacturing self-test

---

# 7. Track 6 — FPGA / RTL Design / DSP

## Goal

Design synthesizable RTL that meets:

- function
- frequency
- area
- power
- CDC
- reset
- verification requirements

---

## 7.1 Digital Design Fundamentals

Master:

- Boolean algebra
- combinational logic
- mux
- decoder
- encoder
- adder
- comparator
- flip-flop
- latch
- counter
- register
- shift register
- FSM

Learn timing:

- propagation delay
- setup
- hold
- clock-to-Q
- critical path

---

## 7.2 Verilog/SystemVerilog RTL

Learn:

- module
- ports
- parameters
- continuous assignment
- `always_comb`
- `always_ff`
- blocking/nonblocking
- generate
- arrays
- packed/unpacked data
- `typedef`
- enum
- interface concepts later

Use SystemVerilog where tool support allows.

---

## 7.3 Coding Discipline

Understand:

```text
Combinational logic:
always_comb

Sequential logic:
always_ff @(posedge clk)
```

Avoid accidental latches.

Learn:

- default assignment
- complete case
- width matching
- signed arithmetic
- parameterization

Use linters.

---

## 7.4 FSM

Implement:

- Moore
- Mealy
- one-process
- two-process

Practice:

- UART TX
- UART RX
- SPI controller
- packet parser
- sequence detector

---

## 7.5 FIFO

Learn:

- synchronous FIFO
- full/empty
- pointer wrap
- almost full
- occupancy
- simultaneous read/write

Then:

- asynchronous FIFO
- Gray code
- pointer synchronization

---

## 7.6 Clock Domain Crossing

Learn:

- metastability
- 2-FF synchronizer
- pulse synchronizer
- handshake
- toggle synchronizer
- async FIFO
- reset-domain crossing

Useful educational reference:

- EcrioniX: https://ecrionix.org/
- metastability/CDC: https://ecrionix.org/vlsi/metastability-cdc-fifo/
- CDC reset synchronization: https://ecrionix.org/clock-domain-crossing/day-07-reset-synchronization/

Never fix CDC simply by adding timing exceptions without proper circuitry.

---

## 7.7 Basic Protocols

Implement:

- UART
- SPI
- I2C
- PWM
- simple streaming valid/ready

Then move to:

- APB
- AXI4-Lite
- AXI4-Stream
- AXI4 Full

---

## 7.8 AXI-Lite

Understand independent channels:

```text
AW
W
B
AR
R
```

Practice:

1. single-register slave
2. multi-register slave
3. byte strobes
4. backpressure
5. invalid address response

Verify with cocotb.

---

## 7.9 AXI-Stream

Learn:

- `TVALID`
- `TREADY`
- `TDATA`
- `TLAST`
- `TKEEP`

Build:

```text
Source
 ↓
FIFO
 ↓
DSP Block
 ↓
Sink
```

with arbitrary backpressure.

---

## 7.10 Pipelining

Learn:

```text
Combinational:

A ───────── logic ───────── Y
          long delay

Pipelined:

A ─ logic ─ FF ─ logic ─ FF ─ Y
```

Metrics:

- latency
- throughput
- initiation interval

Do not confuse latency with throughput.

---

## 7.11 FPGA Memories

Learn:

- LUT RAM
- block RAM
- UltraRAM
- ROM
- single port
- simple dual port
- true dual port

Understand synchronous read latency and inference patterns.

---

# 7.12 DSP Mathematics

Learn:

- sampling
- Nyquist
- aliasing
- convolution
- correlation
- FIR
- IIR
- FFT
- DFT
- interpolation
- decimation
- polyphase
- CIC
- NCO/DDS
- CORDIC

Resource:

- DSPRelated free books: https://www.dsprelated.com/freebooks/dspguide/

---

## 7.13 Fixed-Point DSP

Critical for FPGA.

Learn:

- Q format
- range
- precision
- quantization
- rounding
- truncation
- saturation
- overflow
- guard bits

Example:

```text
Q1.15
1 sign/integer bit
15 fractional bits
```

Design a reference model in Python/NumPy, then implement identical fixed-point behavior in RTL.

---

## 7.14 FIR

Progress:

1. direct-form FIR
2. pipelined FIR
3. symmetric FIR
4. time-multiplexed MAC
5. parallel FIR
6. AXI-Stream FIR

Measure:

- DSP blocks
- LUTs
- registers
- maximum frequency
- latency
- throughput

---

## 7.15 IIR

Learn:

- Direct Form I/II
- biquad
- stability
- coefficient quantization
- saturation
- feedback arithmetic

Verify fixed-point against floating-point model.

---

## 7.16 FFT

Learn architecture:

- radix-2
- radix-4
- butterfly
- twiddle ROM
- bit reversal
- streaming architecture
- block architecture

Do not start by designing a huge FFT core from scratch. Build small butterfly units first.

---

## 7.17 DSP Slices

For AMD FPGAs study DSP48 architecture.

Reference:

- AMD DSP48E2 documentation: https://docs.amd.com/r/en-US/ug1704-spartan-ultrascaleplus-libraries/DSP48E2
- UltraScale DSP guide overview: https://docs.amd.com/r/en-US/conversion-methodology/DSP-Slice-Architecture

Understand how synthesis maps:

```text
multiply
multiply-add
accumulator
pre-adder
```

into dedicated DSP resources.

---

## 7.18 FPGA Debugging

Tools:

- Vivado ILA
- VIO
- SignalTap for Intel devices
- on-board logic analyzer
- test pins
- counters/status registers

Add observability deliberately.

---

## 7.19 Practice Sites

### Highly recommended

- HDLBits — https://hdlbits.01xz.net/wiki/Main_Page
- MakerCode — https://makercode.jixiao-ai.com/
- LeetSilicon — https://lab.leetsilicon.com/
- EWskills — https://www.ewskills.com/
- ChipVerify — https://www.chipverify.com/
- VLSI Verify — https://vlsiverify.com/
- EDA Playground — https://www.edaplayground.com/
- EcrioniX — https://ecrionix.org/
- Nandland — https://nandland.com/
- FPGA4student — https://www.fpga4student.com/p/verilog-project.html

### Reading for deeper architecture

- ZipCPU — https://zipcpu.com/

---

## 7.20 Projects

1. counters/FSM
2. UART
3. SPI
4. synchronous FIFO
5. asynchronous FIFO
6. AXI-Lite register block
7. AXI-Stream FIFO
8. FIR accelerator
9. FFT butterfly
10. CIC decimator
11. DMA-connected DSP pipeline
12. complete acquisition accelerator

### Exit criteria

You can explain:

- synthesizable vs simulation-only constructs
- timing path
- CDC
- reset synchronization
- FIFO
- valid/ready
- AXI-Lite vs AXI-Stream
- fixed point
- latency vs throughput
- resource inference

---

# 8. Track 7 — Co-Simulation and cocotb

## Goal

Use Python to build scalable RTL testbenches, reference models, reusable drivers, and automated regressions.

cocotb official documentation:

- https://docs.cocotb.org/en/stable/

As of 2026, cocotb 2.x is the current major line; always consult the stable documentation for API details.

---

## 8.1 Prerequisites

Learn Python:

- functions
- classes
- list/dict
- generator
- decorator basics
- `async`
- `await`
- packages
- pytest
- NumPy

---

## 8.2 First cocotb Test

Understand:

```text
RTL Simulator
    │
VPI/VHPI/FLI
    │
Python
    │
cocotb
```

Learn:

- DUT handles
- timers
- clock
- edge triggers
- coroutines
- assertions

---

## 8.3 Testbench Structure

Move from:

```python
dut.a.value = 1
dut.b.value = 2
```

to:

```text
Test
 │
 ├─ Driver
 ├─ Monitor
 ├─ Reference Model
 └─ Scoreboard
```

---

## 8.4 Driver

Responsibilities:

- convert transaction into pin-level behavior
- honor ready/valid
- handle backpressure
- enforce protocol timing

---

## 8.5 Monitor

Responsibilities:

- observe DUT
- reconstruct transactions
- avoid driving signals
- report activity to scoreboard

---

## 8.6 Scoreboard

Compare:

```text
Expected
   ▲
Reference Model
   ▲
Input Transaction

versus

DUT Output
```

---

## 8.7 Randomized Verification

Generate:

- random values
- edge cases
- min/max values
- bursts
- backpressure
- reset during activity
- simultaneous read/write

Always seed and log random tests.

---

## 8.8 DSP Co-Simulation

Excellent use case:

```text
Python NumPy Model
        │
        ▼
Expected Fixed-Point Data
        │
        ▼
Scoreboard
        ▲
        │
RTL FIR/FFT Output
```

Test:

- impulse
- step
- sine wave
- random samples
- full-scale input
- overflow
- saturation

---

## 8.9 AXI Verification

Explore:

- cocotbext-axi
- AXI-Lite master/slave
- AXI-Stream source/sink
- memory models

Build tests for:

- handshake stalls
- partial writes
- repeated accesses
- reset
- backpressure

---

## 8.10 Simulation Backends

Open/free:

- Verilator
- Icarus Verilog
- GHDL

Commercial often used in industry:

- Questa
- VCS
- Xcelium
- Riviera-PRO

EDA Playground is useful for browser experiments:

- https://www.edaplayground.com/

---

## 8.11 CI Regression

Run:

```text
git push
  ↓
CI
  ↓
lint
  ↓
compile
  ↓
cocotb
  ↓
formal
  ↓
synthesis smoke test
```

cocotb supports machine-readable regression results and CI-friendly workflows.

---

## 8.12 Project Progression

1. DFF
2. counter
3. ALU
4. FIFO
5. UART
6. AXI-Lite slave
7. AXI-Stream pipeline
8. asynchronous FIFO
9. FIR
10. DMA-facing block

### Exit criteria

You can create a testbench containing:

- stimulus generator
- driver
- monitor
- scoreboard
- reference model
- randomization
- reset tests
- protocol backpressure
- CI execution

---

# 9. Track 8 — Synthesis, Yosys, Static Timing Analysis, Timing Optimization, and Constraints

## Goal

Understand what happens after RTL simulation.

```text
RTL
 ↓
Elaboration
 ↓
Synthesis
 ↓
Technology Mapping
 ↓
Netlist
 ↓
Placement
 ↓
Routing
 ↓
Timing Analysis
```

---

## 9.1 Synthesis Fundamentals

Learn:

- elaboration
- constant propagation
- dead-code removal
- mux inference
- FSM encoding
- arithmetic inference
- RAM inference
- DSP inference
- technology mapping

---

## 9.2 Yosys

Official documentation:

- https://yosyshq.readthedocs.io/
- synthesis tutorial: https://yosyshq.readthedocs.io/projects/yosys/en/latest/getting_started/example_synth.html

Learn commands/concepts:

- `read_verilog`
- hierarchy
- `proc`
- `opt`
- `fsm`
- memory passes
- `techmap`
- ABC mapping
- `stat`
- write netlist

Example exercise:

```text
counter.sv
 ↓
Yosys
 ↓
generic cells
 ↓
mapped cells
 ↓
statistics
```

---

## 9.3 FPGA Synthesis Tools

Production tools:

### AMD

- Vivado
- Vitis

### Intel

- Quartus Prime

### Microchip

- Libero SoC

### Lattice

- Radiant
- Diamond

### Open source

- Yosys
- nextpnr
- Project IceStorm
- Project Trellis
- Project X-Ray-related ecosystems

---

## 9.4 Static Timing Analysis

Understand the core equation:

```text
Required Time - Arrival Time = Slack
```

For setup:

```text
slack >= 0 → setup timing passes
slack < 0  → setup violation
```

For hold, similarly understand minimum-delay requirements.

Learn:

- launch flip-flop
- capture flip-flop
- clock-to-Q
- combinational delay
- routing delay
- setup
- hold
- skew
- jitter
- uncertainty

---

## 9.5 Clock Constraints

Start with:

```tcl
create_clock
```

Then:

- generated clocks
- clock uncertainty
- clock groups

AMD timing constraints reference:

- UG903: https://docs.amd.com/r/en-US/ug903-vivado-using-constraints/Timing-Constraints

---

## 9.6 I/O Constraints

Learn:

```tcl
set_input_delay
set_output_delay
```

These model external device timing relative to FPGA clocks.

Do not leave external interfaces unconstrained.

---

## 9.7 Timing Exceptions

Learn carefully:

```tcl
set_false_path
set_multicycle_path
set_max_delay
set_min_delay
set_clock_groups
```

Warning:

> A timing exception is not a repair for bad RTL.

Every exception should have a documented functional reason.

---

## 9.8 CDC and Timing

Asynchronous clocks require:

1. correct CDC circuit
2. correct timing constraints

AMD documentation explicitly separates asynchronous clock relationships from normal synchronous timing analysis.

Reference:

- https://docs.amd.com/r/en-US/ug903-vivado-using-constraints/Asynchronous-Clock-Domain-Crossings

---

## 9.9 Timing Closure

If timing fails, inspect:

- logic depth
- fanout
- routing
- clocking
- RAM/DSP placement
- reset fanout
- clock enables

Optimization methods:

### RTL

- pipeline
- reduce combinational depth
- balance arithmetic
- register boundaries
- avoid giant muxes
- duplicate control logic where appropriate

### Architecture

- increase parallelism
- use dedicated DSP/RAM
- change interface boundaries
- add latency to improve frequency

### Implementation

- floorplanning
- placement directives
- physical optimization
- replication
- retiming

---

## 9.10 OpenSTA

OpenSTA supports standard timing formats such as:

- Verilog netlist
- Liberty
- SDC
- SDF
- SPEF

Reference:

- https://github.com/The-OpenROAD-Project/OpenSTA
- https://opensta.readthedocs.io/

Practice:

```text
Netlist
+
.lib
+
SDC
 ↓
OpenSTA
 ↓
report_checks / timing report
```

---

## 9.11 Timing Project

Take one RTL datapath and create versions:

```text
Version A: 1 stage
Version B: 2 stages
Version C: 4 stages
```

Measure:

| Version | Latency | Fmax | Registers | DSP |
|---|---:|---:|---:|---:|
| A | 1 | ? | ? | ? |
| B | 2 | ? | ? | ? |
| C | 4 | ? | ? | ? |

This teaches architectural timing optimization.

---

## 9.12 Exit criteria

You can explain:

- RTL vs netlist
- synthesis
- technology mapping
- setup/hold
- slack
- generated clock
- input/output delay
- false path
- multicycle path
- CDC constraint
- timing closure
- pipeline trade-off

---

# 10. Track 9 — Functional Verification and Formal Verification

## Goal

Move from “I simulated some examples” to a verification methodology capable of finding corner-case bugs systematically.

---

## 10.1 Verification Planning

Before writing tests, create:

```text
Feature
 ↓
Requirement
 ↓
Test
 ↓
Coverage
 ↓
Pass Criteria
```

Example:

| Feature | Test | Assertion | Coverage |
|---|---|---|---|
| FIFO empty | read empty | no underflow | empty state |
| FIFO full | write full | no overflow | full state |
| simultaneous RW | random | count valid | cross coverage |

---

## 10.2 Directed Simulation

Use for:

- smoke tests
- known corner cases
- simple blocks

But directed tests alone do not scale.

---

## 10.3 Self-Checking Testbenches

A test should determine pass/fail automatically.

Avoid:

> “Open waveform and visually inspect every signal.”

Use:

- assertions
- scoreboard
- reference model

---

## 10.4 Functional Coverage

Learn:

- coverpoint
- bins
- cross coverage
- coverage closure

Ask:

> Which meaningful scenarios have not occurred?

Do not confuse code coverage with functional coverage.

---

## 10.5 SystemVerilog Verification

Learn:

- class
- randomization
- constraint
- mailbox
- interface
- virtual interface
- assertion
- coverage

References:

- ChipVerify: https://www.chipverify.com/
- VLSI Verify: https://vlsiverify.com/
- EDA Playground: https://www.edaplayground.com/

---

## 10.6 UVM

Learn after SystemVerilog OOP.

Architecture:

```text
Test
 ↓
Environment
 ↓
Agent
 ├─ Sequencer
 ├─ Driver
 └─ Monitor
      │
      ▼
Scoreboard
```

Learn:

- sequence item
- sequence
- sequencer
- driver
- monitor
- agent
- env
- scoreboard
- config DB
- factory
- phases
- TLM
- RAL
- functional coverage

ChipVerify UVM tutorials:

- https://www.chipverify.com/uvm

UVM is particularly useful for large reusable verification environments.

---

## 10.7 Assertions / SVA

Learn:

- immediate assertion
- concurrent assertion
- sequence
- property
- implication
- `$past`
- `$stable`
- `$rose`
- `$fell`
- disable iff

Examples of useful properties:

```text
valid must remain asserted until ready
FIFO count never exceeds depth
request eventually receives response
write response only after accepted write
```

---

## 10.8 Formal Verification Fundamentals

Formal explores state space mathematically rather than relying only on hand-selected simulation vectors.

Core terms:

```text
assume
assert
cover
```

Study:

- bounded model checking
- induction
- safety property
- liveness property
- reachability
- counterexample
- state-space explosion

---

## 10.9 SymbiYosys

Official:

- https://symbiyosys.readthedocs.io/

Supports workflows for:

- bounded safety checking
- unbounded safety checking
- cover traces
- liveness checks

Modes include:

```text
bmc
prove
cover
live
```

---

## 10.10 Best Formal Training Blocks

### FIFO

Properties:

- no overflow
- no underflow
- ordering preserved
- count in range

### Arbiter

Properties:

- no two grants simultaneously
- grant only after request
- fairness if modeled

### AXI-Lite register block

Properties:

- response follows accepted request
- no lost transaction
- valid stability

### Async FIFO

More advanced:

- pointer invariants
- Gray transitions
- ordering

---

## 10.11 Equivalence

Study:

- RTL vs optimized RTL
- RTL vs synthesized netlist
- pre/post ECO
- combinational equivalence
- sequential equivalence concepts

Formal equivalence is highly relevant in ASIC flows.

---

## 10.12 Commercial Formal Tools

Common industry families include:

- Cadence Jasper
- Synopsys VC Formal
- Siemens Questa Formal

Use open-source tools to learn the concepts; commercial tools provide substantially broader engines, debug, capacity, and methodology support.

---

## 10.13 RISC-V Verification

Advanced RISC-V verification often combines:

```text
RTL Core
   │
Instruction Trace
   │
   ├───────────────┐
   ▼               ▼
RTL Result       ISS Result
   │               │
   └──── Compare ──┘
```

Useful projects:

- riscv-tests: https://github.com/riscv-software-src/riscv-tests
- riscv-dv: https://github.com/chipsalliance/riscv-dv
- Spike ecosystem: https://github.com/riscv-software-src/riscv-isa-sim

Ibex verification is an excellent public reference:

- https://github.com/lowRISC/ibex

---

## 10.14 Verification Portfolio Projects

1. cocotb FIFO verification
2. formal FIFO
3. AXI-Lite randomized test
4. AXI-Lite assertions
5. async FIFO simulation + formal
6. UART protocol monitor
7. UVM APB agent
8. UVM AXI-Lite environment
9. RISC-V ALU formal properties
10. RISC-V instruction co-simulation

### Exit criteria

You can distinguish:

- directed testing
- random testing
- coverage
- assertions
- formal
- equivalence
- simulation
- co-simulation

and choose the correct technique for each problem.

---

# 11. Track 10 — ASIC Design and RISC-V SoC Design

## Goal

Progress from RTL blocks to a complete processor/SoC and understand the RTL-to-GDSII flow.

---

# 11.1 Semiconductor / ASIC Foundation

Learn:

- CMOS logic
- standard cells
- PVT
- process corner
- voltage corner
- temperature corner
- library
- Liberty `.lib`
- LEF
- DEF
- GDSII
- parasitics
- SPEF
- SDF

You do not need to become an analog transistor designer, but you must understand what the digital backend is optimizing.

---

## 11.2 ASIC Front-End Flow

```text
Specification
 ↓
Microarchitecture
 ↓
RTL
 ↓
Lint
 ↓
CDC/RDC
 ↓
Functional Verification
 ↓
Formal
 ↓
Synthesis
 ↓
STA
```

---

## 11.3 ASIC Back-End Flow

```text
Synthesized Netlist
 ↓
Floorplan
 ↓
Power Distribution
 ↓
Placement
 ↓
Clock Tree Synthesis
 ↓
Routing
 ↓
Parasitic Extraction
 ↓
STA
 ↓
DRC/LVS
 ↓
GDSII
```

---

## 11.4 Open-Source RTL-to-GDS Learning

OpenROAD Flow Scripts:

- https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
- https://openroad-flow-scripts.readthedocs.io/

The flow integrates stages including synthesis, floorplanning, placement, CTS, routing, and signoff-oriented checks.

OpenROAD:

- https://openroad.readthedocs.io/

Yosys:

- https://yosyshq.readthedocs.io/

OpenSTA:

- https://opensta.readthedocs.io/

KLayout:

- https://www.klayout.de/

Sky130 ecosystem:

- https://skywater-pdk.readthedocs.io/

### Note on OpenLane

The original OpenLane project is now maintenance-oriented and its project page directs new designs toward successor tooling. For current learning, prioritize OpenROAD Flow Scripts and current open-flow documentation rather than assuming older OpenLane tutorials represent the latest preferred flow.

---

# 11.5 RISC-V ISA

Official RISC-V specifications:

- Unprivileged ISA: https://docs.riscv.org/reference/isa/unpriv/unpriv-index.html
- Privileged ISA: https://docs.riscv.org/reference/isa/priv/priv-index.html

Start with:

- RV32I
- instruction formats
- registers
- loads/stores
- branches
- jumps
- arithmetic
- immediate operations

Then:

- M
- C
- A
- F/D depending on goal
- bit-manipulation concepts

---

## 11.6 RISC-V Assembly

Understand:

```text
C
 ↓
Compiler
 ↓
Assembly
 ↓
Machine Code
```

Use:

- `riscv64-unknown-elf-gcc`
- `objdump`
- Spike
- Ripes

Ripes is useful for visualizing pipelines, caches, C-to-assembly, and memory-mapped I/O:

- https://github.com/mortbopet/Ripes

---

## 11.7 Processor Microarchitecture

Begin:

```text
Single-cycle CPU
```

Then:

```text
Multi-cycle
```

Then:

```text
5-stage pipeline

IF
 ↓
ID
 ↓
EX
 ↓
MEM
 ↓
WB
```

Learn:

- data hazard
- forwarding
- stall
- control hazard
- branch handling
- exception
- interrupt

---

## 11.8 CSR and Privilege

Learn:

- Machine mode
- Supervisor mode
- User mode
- CSR
- trap
- exception
- interrupt
- `mstatus`
- `mepc`
- `mcause`
- `mtvec`

Read the official privileged specification.

---

## 11.9 Interrupt Architecture

For a SoC study:

- local timer/software interrupts
- external interrupt controller
- PLIC concepts
- newer interrupt-controller architectures as relevant to the target profile

Understand the complete path:

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

---

## 11.10 SoC Interconnect

Study:

- Wishbone
- APB
- AHB
- AXI
- OBI where relevant

Learn:

- master/initiator
- slave/target
- address decoding
- arbitration
- response
- ordering
- burst
- backpressure

---

## 11.11 Memory Subsystem

Progress:

```text
CPU
 ↓
Scratchpad RAM
```

then:

```text
CPU
 ↓
I-Cache / D-Cache
 ↓
Memory Bus
 ↓
RAM
```

then:

```text
CPU
 ↓
Cache
 ↓
MMU
 ↓
TLB
 ↓
AXI
 ↓
DDR
```

Learn:

- direct mapped
- set associative
- replacement
- write-through
- write-back
- write allocate
- cache coherence concept
- TLB
- page table
- virtual memory

---

## 11.12 Linux-Capable RISC-V

To run Linux, the problem is much larger than implementing RV32I.

Study:

- privilege
- MMU
- supervisor mode
- timer
- interrupts
- device tree
- boot firmware
- SBI
- UART
- storage
- memory system

Study OpenSBI:

- https://github.com/riscv-software-src/opensbi

Study application-class open core:

- CVA6: https://docs.openhwgroup.org/projects/cva6-user-manual/

CVA6 supports FPGA/ASIC targets and configurations with MMU/cache functionality suitable for application-class experimentation.

---

## 11.13 Embedded RISC-V Core Study

Ibex:

- https://github.com/lowRISC/ibex

Good for learning:

- production-quality SystemVerilog structure
- embedded-class core architecture
- verification structure
- synthesis concerns

---

## 11.14 Custom Instructions / Coprocessors

After mastering a baseline core, explore custom instructions.

OpenHW CV-X-IF:

- https://docs.openhwgroup.org/projects/openhw-group-core-v-xif/en/latest/intro.html

CV-X-IF is intended to connect custom or standardized instruction extensions implemented in a coprocessor without deeply modifying the CPU.

CVA6 coprocessor tutorial:

- https://docs.openhwgroup.org/projects/cva6-user-manual/01_cva6_user/CVX_Interface_Coprocessor.html

Example evolution:

```text
RV32IM
   │
   ▼
Custom Instruction
   │
   ▼
DSP Coprocessor
   ├─ MAC
   ├─ DOTP
   ├─ SAT
   └─ PACK
```

---

## 11.15 SoC Peripherals

Build:

- UART
- timer
- GPIO
- SPI
- interrupt controller
- DMA
- debug block
- boot ROM

Create a memory map:

```text
0x0000_0000  Boot ROM
0x1000_0000  UART
0x1000_1000  GPIO
0x1000_2000  Timer
0x2000_0000  SRAM
```

---

## 11.16 DFT Basics

For ASIC production, learn:

- scan chain
- scan insertion
- ATPG
- stuck-at faults
- transition faults
- MBIST
- JTAG boundary scan
- test coverage

You do not need to become a DFT specialist immediately, but a SoC designer must know why test structures affect architecture and implementation.

---

## 11.17 Power

Learn:

- clock gating
- power gating
- dynamic power
- leakage
- switching activity
- voltage domains
- isolation
- retention
- UPF concepts

ChipVerify includes introductory UPF learning material:

- https://www.chipverify.com/

---

## 11.18 Commercial ASIC Tool Families

Examples commonly encountered in industry include:

### Simulation / Verification

- Synopsys VCS
- Cadence Xcelium
- Siemens Questa

### Synthesis

- Synopsys Design Compiler / Fusion Compiler
- Cadence Genus

### Formal

- Synopsys VC Formal
- Cadence Jasper
- Siemens Questa Formal

### STA

- Synopsys PrimeTime
- Cadence Tempus

### Place and Route

- Synopsys Fusion Compiler
- Cadence Innovus

### Physical Verification

- Siemens Calibre
- KLayout for open/learning flows

Licensing and exact product combinations vary by company and process.

---

## 11.19 RISC-V SoC Project Ladder

### Project 1

RV32I ALU

### Project 2

single-cycle RV32I processor

### Project 3

5-stage RV32I pipeline

### Project 4

hazard/forwarding unit

### Project 5

CSR/trap support

### Project 6

AXI/OBI/Wishbone bridge

### Project 7

UART + timer SoC

### Project 8

instruction/data cache

### Project 9

DMA

### Project 10

custom DSP instruction

### Project 11

FPGA prototype

### Project 12

OpenROAD ASIC implementation

---

# 12. Practice and Learning Platform Directory

This section separates **practice**, **tutorial**, **reference**, and **professional tool documentation**.

---

## 12.1 Embedded C / C++

### Practice

- EWskills — https://www.ewskills.com/
- MakerCode — https://makercode.jixiao-ai.com/
- Exercism C — https://exercism.org/tracks/c
- LeetCode — https://leetcode.com/
- HackerRank — https://www.hackerrank.com/

### Reference

- cppreference C — https://en.cppreference.com/w/c
- cppreference C++ — https://en.cppreference.com/w/cpp
- Embedded Artistry — https://embeddedartistry.com/
- Memfault Interrupt — https://interrupt.memfault.com/
- Arm CMSIS — https://arm-software.github.io/CMSIS_6/latest/Core/index.html

---

## 12.2 RTOS

- FreeRTOS — https://www.freertos.org/
- Zephyr — https://docs.zephyrproject.org/latest/

Practice by porting the same application between the two kernels.

---

## 12.3 Embedded Linux / PetaLinux

- AMD PetaLinux UG1144 — https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Introduction
- Linux Kernel Docs — https://docs.kernel.org/
- Yocto Project — https://docs.yoctoproject.org/
- U-Boot — https://docs.u-boot.org/
- Buildroot — https://buildroot.org/docs.html

---

## 12.4 Qt/QML

- Qt Documentation — https://doc.qt.io/
- Qt QML + C++ — https://doc.qt.io/qt-6/qtqml-cppintegration-overview.html
- Qt Embedded Linux — https://doc.qt.io/qt-6.8/embedded-linux.html
- QML Book — https://www.qt.io/product/qt6/qml-book

---

## 12.5 RTL / FPGA

### Practice

- HDLBits — https://hdlbits.01xz.net/wiki/Main_Page
- MakerCode — https://makercode.jixiao-ai.com/
- LeetSilicon — https://lab.leetsilicon.com/
- EWskills — https://www.ewskills.com/
- EDA Playground — https://www.edaplayground.com/

### Tutorials

- ChipVerify — https://www.chipverify.com/
- VLSI Verify — https://vlsiverify.com/
- Nandland — https://nandland.com/
- FPGA4student — https://www.fpga4student.com/p/verilog-project.html
- ZipCPU — https://zipcpu.com/
- EcrioniX — https://ecrionix.org/

---

## 12.6 DSP

- DSPRelated — https://www.dsprelated.com/
- DSP Guide — https://www.dsprelated.com/freebooks/dspguide/
- MATLAB documentation — https://www.mathworks.com/help/matlab/
- SciPy Signal — https://docs.scipy.org/doc/scipy/reference/signal.html
- NumPy — https://numpy.org/doc/

Use Python/MATLAB as the golden model for FPGA DSP.

---

## 12.7 cocotb

- cocotb docs — https://docs.cocotb.org/en/stable/
- cocotb website — https://www.cocotb.org/
- EDA Playground — https://www.edaplayground.com/

---

## 12.8 Synthesis / STA

- Yosys — https://yosyshq.readthedocs.io/
- OpenSTA — https://opensta.readthedocs.io/
- AMD UG903 — https://docs.amd.com/r/en-US/ug903-vivado-using-constraints/Timing-Constraints
- OpenROAD — https://openroad.readthedocs.io/

---

## 12.9 Formal Verification

- SymbiYosys — https://symbiyosys.readthedocs.io/
- Yosys formal commands — https://yosyshq.readthedocs.io/
- ZipCPU formal articles — https://zipcpu.com/
- VLSI Verify Assertions — https://vlsiverify.com/
- ChipVerify — https://www.chipverify.com/

---

## 12.10 RISC-V

- RISC-V specifications — https://docs.riscv.org/reference/isa/
- Ripes — https://github.com/mortbopet/Ripes
- Ibex — https://github.com/lowRISC/ibex
- CVA6 — https://docs.openhwgroup.org/projects/cva6-user-manual/
- CV-X-IF — https://docs.openhwgroup.org/projects/openhw-group-core-v-xif/en/latest/
- Spike — https://github.com/riscv-software-src/riscv-isa-sim
- riscv-tests — https://github.com/riscv-software-src/riscv-tests
- riscv-dv — https://github.com/chipsalliance/riscv-dv

---

# 13. Tool Matrix: Learning vs Production

| Area | Learning / Open Source | Common Production Examples |
|---|---|---|
| Embedded C | GCC, Clang | GCC, Clang, IAR, Arm Compiler |
| Build | Make, CMake, Ninja | CMake, Ninja, vendor systems, Yocto |
| MCU Debug | OpenOCD, GDB | J-Link, Lauterbach, vendor probes |
| RTOS | FreeRTOS, Zephyr | FreeRTOS, Zephyr, commercial RTOSes |
| Linux | Yocto, Buildroot | Yocto/PetaLinux, vendor BSPs |
| GUI | Qt/QML | Qt/QML |
| RTL simulation | Verilator, Icarus | VCS, Xcelium, Questa |
| Python verification | cocotb | cocotb + commercial simulators |
| SV/UVM | EDA Playground | VCS/Xcelium/Questa |
| FPGA synth/P&R | Yosys/nextpnr | Vivado, Quartus, Libero |
| ASIC synthesis | Yosys | Design Compiler/Fusion Compiler, Genus |
| STA | OpenSTA | PrimeTime, Tempus |
| Formal | SymbiYosys | Jasper, VC Formal, Questa Formal |
| Physical design | OpenROAD | Innovus, Fusion Compiler |
| DRC/LVS | KLayout/Netgen/Magic | Calibre and foundry-qualified flows |
| RISC-V ISS | Spike, QEMU | Spike/reference models/vendor models |
| CI | GitHub Actions/GitLab CI/Jenkins | GitLab/Jenkins/GitHub/self-hosted CI |

---

# 14. Recommended Integrated Learning Order

Do not follow the original ten topics strictly from 1 to 10. Use this sequence.

---

## Phase 0 — Foundations

**Duration guideline:** 3–6 weeks

Study:

- Linux
- Git
- C basics
- binary/hex
- digital logic
- Python basics

Build:

- C command-line utilities
- simple logic simulations

---

## Phase 1 — Bare Metal + RTL Fundamentals

**Duration guideline:** 2–3 months

Parallel path A:

```text
C
 ↓
Pointers
 ↓
Bitwise
 ↓
MMIO
 ↓
GPIO/UART
```

Parallel path B:

```text
Digital Logic
 ↓
Verilog/SystemVerilog
 ↓
FSM
 ↓
UART
```

Interesting exercise:

> Implement UART in RTL and write a bare-metal C driver that communicates with it.

---

## Phase 2 — RTOS + Verification + Synthesis

**Duration guideline:** 2–3 months

Study:

- FreeRTOS
- queues/mutexes
- cocotb
- Yosys
- timing fundamentals

Build:

```text
RTOS UART application
+
RTL FIFO
+
cocotb verification
+
Yosys synthesis
```

---

## Phase 3 — AXI + DSP + Embedded Linux

**Duration guideline:** 3–4 months

Study:

- AXI-Lite
- AXI-Stream
- fixed-point DSP
- Linux C/C++
- `mmap`
- UIO
- device tree

Build:

```text
FPGA FIR
 ↓
AXI-Stream
 ↓
AXI DMA
 ↓
Linux C++ App
```

---

## Phase 4 — Qt/QML + Driver + Timing Closure

**Duration guideline:** 2–3 months

Study:

- Qt C++
- QML
- Linux platform driver
- DMA
- cache coherency
- XDC/SDC
- timing closure

Build:

```text
Qt/QML HMI
 ↓
C++ Backend
 ↓
Linux Driver
 ↓
FPGA DSP
```

---

## Phase 5 — Formal + ASIC + RISC-V

**Duration guideline:** 4–6 months

Study:

- SVA
- SymbiYosys
- RISC-V
- pipeline
- cache
- bus
- OpenROAD

Build:

```text
RISC-V Core
 ↓
Formal + Simulation
 ↓
FPGA
 ↓
Yosys
 ↓
OpenROAD
 ↓
GDS
```

---

# 15. Suggested 18-Month Schedule

## Months 1–2

Embedded C:

- C fundamentals
- pointers
- memory
- bitwise
- struct
- volatile/static/const

RTL:

- HDLBits first half
- combinational logic
- counters
- FSM

Projects:

- C register simulator
- RTL UART TX

---

## Months 3–4

Bare metal:

- GPIO
- timer
- UART
- interrupt

RTL:

- UART RX
- SPI
- FIFO

Verification:

- cocotb basics

Project:

- UART protocol system

---

## Months 5–6

RTOS:

- FreeRTOS
- tasks
- queues
- semaphore
- mutex

RTL:

- AXI-Lite
- AXI-Stream

Synthesis:

- Yosys
- Vivado synthesis

Project:

- AXI-Lite register peripheral

---

## Months 7–8

DSP:

- fixed point
- FIR
- IIR
- FFT basics

Verification:

- NumPy reference model
- cocotb DSP verification

Project:

- streaming FIR accelerator

---

## Months 9–10

Linux/PetaLinux:

- processes
- threads
- mmap
- UIO
- device tree
- C++ service

Project:

```text
Linux app
 ↕
UIO
 ↕
AXI-Lite FPGA
```

---

## Months 11–12

DMA:

- AXI DMA
- Linux DMA concepts
- memory coherency

Qt/QML:

- UI
- signals/slots
- C++ backend
- worker thread

Project:

- FPGA data acquisition HMI

---

## Months 13–14

Timing:

- STA
- constraints
- setup/hold
- timing closure

Formal:

- assertions
- FIFO proof
- AXI properties

---

## Months 15–16

RISC-V:

- ISA
- assembly
- pipeline
- hazards
- CSR

Project:

- RV32I core or modify an existing small core

---

## Months 17–18

ASIC:

- Yosys
- OpenSTA
- OpenROAD
- floorplan
- placement
- CTS
- routing

Capstone:

```text
RISC-V / FPGA DSP SoC
 +
verification
 +
timing
 +
software
```

---

# 16. Weekly Study Pattern

A productive week could be:

| Day | Focus |
|---|---|
| Mon | theory + documentation |
| Tue | implementation |
| Wed | implementation |
| Thu | verification/debug |
| Fri | synthesis/performance |
| Sat | project integration |
| Sun | notes/refactor/review |

Suggested ratio:

```text
20% Reading
50% Building
20% Debugging/Verification
10% Documentation
```

For hardware engineering, debugging failed designs is part of the learning—not a waste of study time.

---

# 17. Portfolio Structure

Do not store everything in one giant repository.

Example:

```text
embedded-labs/
├── 01_c_fundamentals
├── 02_baremetal_gpio
├── 03_uart_driver
├── 04_freertos
└── 05_dma

rtl-labs/
├── 01_hdlbits
├── 02_uart
├── 03_fifo
├── 04_axi_lite
├── 05_axi_stream
├── 06_fir
└── 07_async_fifo

verification/
├── cocotb_fifo
├── cocotb_axi
├── formal_fifo
└── uvm_apb

embedded-linux/
├── uio
├── platform_driver
├── dma
└── qt_hmi

riscv/
├── alu
├── single_cycle
├── pipeline
├── csr
└── soc

asic/
├── yosys
├── opensta
└── openroad
```

Every serious project should contain:

```text
README.md
docs/
rtl/
src/
tb/
scripts/
constraints/
Makefile or CMakeLists.txt
tests/
```

---

# 18. Documentation You Should Learn to Produce

Engineering capability is not only code.

Create:

## Architecture document

- objectives
- block diagram
- interfaces
- clocks
- resets
- memory map

## Register specification

- offset
- field
- access
- reset value
- behavior

## Verification plan

- features
- test cases
- assertions
- coverage

## Timing document

- clocks
- asynchronous domains
- exceptions
- I/O timing

## Firmware design

- tasks
- priorities
- queues
- states

## Linux integration guide

- device tree
- driver
- device nodes
- service startup

## Production test specification

- test setup
- limits
- pass/fail
- logs

---

# 19. Recommended Capstone Projects

## Capstone A — Embedded Control Product

```text
STM32
├─ FreeRTOS
├─ UART
├─ SPI sensor
├─ ADC DMA
├─ PID
└─ bootloader
```

Demonstrates:

- Embedded C
- RTOS
- memory
- DMA
- production firmware

---

## Capstone B — FPGA DSP Accelerator

```text
ADC samples
 ↓
AXI-Stream
 ↓
FIR
 ↓
FFT
 ↓
AXI DMA
 ↓
DDR
```

Demonstrates:

- RTL
- DSP
- AXI
- cocotb
- timing

---

## Capstone C — Embedded Linux Instrument

```text
FPGA
 ↓
DMA
 ↓
Linux Driver
 ↓
C++ Service
 ↓
Qt/QML
```

Demonstrates:

- PetaLinux
- Linux memory
- drivers
- Qt
- production architecture

---

## Capstone D — RISC-V SoC

```text
RISC-V CPU
├─ UART
├─ Timer
├─ GPIO
├─ SRAM
├─ Bus
└─ Custom DSP Unit
```

Verification:

```text
cocotb
+
formal
+
riscv-tests
```

Implementation:

```text
FPGA
+
OpenROAD ASIC flow
```

This is the strongest single portfolio project spanning most of the roadmap.

---

# 20. What “Advanced” Actually Means

You are not advanced because you have used many tools.

Advanced means you can reason across layers.

Example failure:

> DMA data is corrupted intermittently.

A beginner may suspect the FPGA arithmetic.

An advanced engineer considers:

```text
RTL?
CDC?
AXI backpressure?
DMA descriptor?
buffer lifetime?
cache coherency?
memory barrier?
alignment?
interrupt race?
driver ownership?
```

Another example:

> FPGA cannot reach 250 MHz.

An advanced engineer asks:

```text
logic depth?
DSP inference?
fanout?
routing?
pipeline boundaries?
clock uncertainty?
false path accidentally missing?
CDC incorrectly timed?
BRAM output registered?
placement?
```

The purpose of this roadmap is to develop that cross-layer reasoning.

---

# 21. Final Dependency Map

```text
C Fundamentals
     │
     ├───────────────┐
     ▼               ▼
Bare Metal       Linux C/C++
     │               │
     ▼               ▼
RTOS            PetaLinux
     │               │
Memory/Cache     MMU/DMA/IOMMU
     │               │
     └──────┬────────┘
            ▼
       System Software
            │
            ▼
          Qt/QML


Digital Logic
     │
     ▼
SystemVerilog
     │
     ├───────────┐
     ▼           ▼
FPGA RTL      Verification
     │           │
     ▼           ▼
AXI/DSP      cocotb/UVM
     │           │
     └─────┬─────┘
           ▼
       Synthesis
           │
           ▼
          STA
           │
           ▼
      Timing Closure
           │
           ▼
         ASIC


C + Assembly + RTL
        │
        ▼
      RISC-V
        │
        ▼
      Pipeline
        │
        ▼
   CSR/Interrupt
        │
        ▼
    Cache/MMU
        │
        ▼
     SoC Bus
        │
        ▼
   RISC-V SoC
        │
        ▼
 Custom Accelerator
        │
        ▼
 FPGA / ASIC
```

---

# 22. Minimum Core Resource Set

If the full resource list feels overwhelming, start with these.

### C / Embedded

1. cppreference — https://en.cppreference.com/w/c
2. Arm CMSIS — https://arm-software.github.io/CMSIS_6/latest/Core/index.html
3. Memfault Interrupt — https://interrupt.memfault.com/
4. EWskills — https://www.ewskills.com/

### RTOS

5. FreeRTOS — https://www.freertos.org/
6. Zephyr — https://docs.zephyrproject.org/latest/

### Linux/PetaLinux

7. Linux Kernel Docs — https://docs.kernel.org/
8. AMD PetaLinux UG1144 — https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Introduction
9. Yocto — https://docs.yoctoproject.org/

### Qt

10. Qt Docs — https://doc.qt.io/
11. QML/C++ integration — https://doc.qt.io/qt-6/qtqml-cppintegration-overview.html

### RTL

12. HDLBits — https://hdlbits.01xz.net/wiki/Main_Page
13. ChipVerify — https://www.chipverify.com/
14. EcrioniX — https://ecrionix.org/
15. LeetSilicon — https://lab.leetsilicon.com/

### Verification

16. cocotb — https://docs.cocotb.org/en/stable/
17. SymbiYosys — https://symbiyosys.readthedocs.io/
18. VLSI Verify — https://vlsiverify.com/

### Synthesis/ASIC

19. Yosys — https://yosyshq.readthedocs.io/
20. OpenSTA — https://opensta.readthedocs.io/
21. OpenROAD — https://openroad.readthedocs.io/
22. AMD UG903 — https://docs.amd.com/r/en-US/ug903-vivado-using-constraints/Timing-Constraints

### RISC-V

23. RISC-V ISA — https://docs.riscv.org/reference/isa/
24. Ibex — https://github.com/lowRISC/ibex
25. CVA6 — https://docs.openhwgroup.org/projects/cva6-user-manual/
26. Ripes — https://github.com/mortbopet/Ripes
27. CV-X-IF — https://docs.openhwgroup.org/projects/openhw-group-core-v-xif/en/latest/

---

# 23. Recommended First 90 Days

If starting today, avoid trying to install every EDA tool immediately.

## Days 1–30

### C

- syntax
- pointers
- arrays
- struct
- bitwise
- volatile
- static
- const

### Digital

- Boolean logic
- combinational RTL
- sequential RTL

### Practice

- EWskills Embedded C
- Exercism C
- HDLBits

### Deliverables

- ring buffer in C
- register-map simulator
- 20–30 HDLBits exercises
- parameterized counter
- UART TX

---

## Days 31–60

### Embedded

- Cortex-M
- GPIO
- timer
- UART
- interrupt

### RTL

- FSM
- UART RX
- FIFO

### Verification

- cocotb
- waveform debugging

### Tools

- GCC
- GDB
- CMake
- Verilator
- GTKWave

### Deliverables

- UART bare-metal driver
- RTL UART
- cocotb UART test
- synchronous FIFO

---

## Days 61–90

### Embedded

- FreeRTOS
- tasks
- queues
- mutex
- ISR signaling

### RTL

- AXI-Lite
- AXI-Stream basics

### Synthesis

- Yosys
- Vivado reports
- setup/hold fundamentals

### Deliverables

- RTOS sensor simulation
- AXI-Lite register block
- cocotb AXI-Lite test
- first synthesis/timing report

After 90 days, you will have enough foundation to choose whether to temporarily emphasize:

```text
Embedded / RTOS
or
FPGA / RTL
or
Embedded Linux
```

without losing the long-term path toward complete SoC work.

---

# 24. Reference Notes and Current Tool Documentation

The following official/current references are especially important because tool behavior and versions change over time.

### AMD PetaLinux

PetaLinux 2026.1 describes the Yocto/eSDK-based flow and provides C/C++ custom application creation and build workflows:

- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Introduction
- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Adding-Custom-Applications
- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Building-User-Applications
- https://docs.amd.com/r/en-US/ug1144-petalinux-tools-reference-guide/Setting-Up-the-System-Devicetree

### Qt

- https://doc.qt.io/qt-6/qtqml-cppintegration-overview.html
- https://doc.qt.io/qt-6.8/embedded-linux.html

### cocotb

- https://docs.cocotb.org/en/stable/

### Timing Constraints

AMD Vivado UG903:

- https://docs.amd.com/r/en-US/ug903-vivado-using-constraints/Timing-Constraints
- https://docs.amd.com/r/en-US/ug903-vivado-using-constraints/Recommended-Constraints-Sequence
- https://docs.amd.com/r/en-US/ug903-vivado-using-constraints/Asynchronous-Clock-Domain-Crossings

### Linux drivers and DMA

- platform drivers: https://docs.kernel.org/driver-api/driver-model/platform.html
- DMA API: https://docs.kernel.org/core-api/dma-api.html
- DMA guide: https://docs.kernel.org/6.9/core-api/dma-api-howto.html

### Formal

- https://symbiyosys.readthedocs.io/

### OpenROAD

- https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
- https://openroad.readthedocs.io/

### RISC-V

- https://docs.riscv.org/reference/isa/
- https://docs.openhwgroup.org/projects/openhw-group-core-v-xif/en/latest/intro.html

---

# 25. Final Engineering Target

The ultimate target of this roadmap is not:

> “I know C, Verilog, Linux, and Qt.”

It is:

> “I can architect, implement, verify, optimize, debug, and productionize a heterogeneous embedded/FPGA/SoC system.”

A mature final skill stack looks like:

```text
System Architecture
        │
        ├─ Embedded C/C++
        │     ├─ Bare Metal
        │     └─ RTOS
        │
        ├─ Embedded Linux
        │     ├─ PetaLinux/Yocto
        │     ├─ Driver
        │     ├─ DMA/Memory
        │     └─ Qt/QML
        │
        ├─ FPGA
        │     ├─ RTL
        │     ├─ AXI
        │     ├─ DSP
        │     └─ Timing
        │
        ├─ Verification
        │     ├─ cocotb
        │     ├─ SystemVerilog/UVM
        │     └─ Formal
        │
        └─ ASIC / SoC
              ├─ RISC-V
              ├─ Cache/MMU
              ├─ Interconnect
              ├─ Synthesis/STA
              └─ RTL-to-GDSII
```

This is a multi-year skill set in professional practice, but you do not need to master every branch before building useful systems. Build one vertical slice at a time, then deepen each layer.


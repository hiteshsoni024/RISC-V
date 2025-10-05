# Single-Cycle 32-bit RISC-V Processor in Verilog

## 1. Overview

This repository contains the Verilog source code for a 32-bit, single-cycle RISC-V processor. This project was developed for educational purposes to demonstrate the fundamental principles of computer architecture. The processor is designed from the ground up, component by component, and implements a significant subset of the RV32I base integer instruction set.

The entire design is synthesizable and can be tested using standard Verilog simulators like Xilinx Vivado's XSim.

---

## 2. Architecture

The processor utilizes a classic single-cycle datapath. Every instruction is fully executed in a single clock cycle, passing through five conceptual stages:

1.  **Instruction Fetch (IF)**
2.  **Instruction Decode (ID)**
3.  **Execute (EX)**
4.  **Memory Access (MEM)**
5.  **Write-Back (WB)**

### Datapath Block Diagram

---

## 3. Features

-   **Architecture**: 32-bit Single-Cycle
-   **ISA**: RV32I (Subset)
-   **Supported Instructions**:
    -   **R-Type**: `add`, `sub`, `and`, `or`, `xor`
    -   **I-Type (Arithmetic)**: `addi`
    -   **I-Type (Load)**: `lw`
    -   **S-Type (Store)**: `sw`
    -   **B-Type (Branch)**: `beq`, `bne`

---

## 4. Modules Description

The project follows a modular design, with each major hardware block implemented in a separate Verilog file.

| File Name | Description |
| :--- | :--- |
| `riscv_core.v` | The top-level module that integrates all components. |
| `control_unit.v`| The main control unit; decodes the opcode to generate primary control signals. |
| `alu_control_unit.v`| Generates the final 3-bit ALU control signal based on instruction `funct` fields and `ALUOp`. |
| `data_memory.v` | The data memory (RAM) used by `lw` and `sw` instructions. |
| `InstructionMemo.v` | The instruction memory (ROM) that stores the program's machine code. |
| `Immediate_generator.v`| Extracts and sign-extends immediate values from various instruction formats (I, S, B). |
| `RB32x32.v` | The 32x32 Register File. |
| `ALU32.v` | The 32-bit Arithmetic Logic Unit. |
| `PC.v` | The Program Counter register. |
| `PC_incrementor.v`| A simple adder to calculate PC + 4. |
| `mux2to1.v` | A generic 2-to-1 multiplexer. |

---

## 5. How to Run Simulation

### Prerequisites
-   Xilinx Vivado or another Verilog simulator.

### Test Program
The `InstructionMemo.v` module is pre-loaded with a comprehensive test program to verify data dependencies and branch logic.

```assembly
// Program: Data Dependency and Branch (Not Taken) Test
// PC=0:
addi x5, x0, 40      // x5 = 40
// PC=4:
sw   x5, 0(x0)       // Memory[0] = 40
// PC=8:
addi x6, x0, 12      // x6 = 12
// PC=12:
lw   x7, 0(x0)       // x7 = Memory[0] = 40
// PC=16:
sub  x8, x7, x6      // x8 = 40 - 12 = 28
// PC=20:
beq  x8, x0, 8       // Branch if x8==0. Condition is FALSE, so branch is NOT taken.
// PC=24:
add  x9, x8, x6      // This will execute. x9 = 28 + 12 = 40
// PC=28:
addi x10, x0, 999    // This will also execute. x10 = 999
```

### Simulation Output
Running the `tb_data_dependency_test.v` testbench produces the following console output, which verifies the correct step-by-step execution of the program.

```
--- Data Dependency Test Start ---
--- Reset Released. Executing Program... ---

Cycle 1 (PC=         4): addi x5, x0, 40
 > Value in x5 =         40

Cycle 2 (PC=         8): sw x5, 0(x0)
 > Value of x5 (40) stored in Data Memory at address 0.

Cycle 3 (PC=        12): addi x6, x0, 12
 > Value in x6 =         12

Cycle 4 (PC=        16): lw x7, 0(x0)
 > Value loaded into x7 =         40

Cycle 5 (PC=        20): sub x8, x7, x6
 > Value in x8 =         28

Cycle 6 (PC=        24): beq x8, x0, 8. Branch NOT taken.
 > x8 (28) is not equal to x0 (0).

Cycle 7 (PC=        28): add x9, x8, x6
 > Value in x9 =         40

Cycle 8 (PC=        32): addi x10, x0, 999
 > Value in x10 =        999

--- Program Finished ---
```

---


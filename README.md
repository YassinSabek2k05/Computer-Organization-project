# Custom 8-Bit CPU: Logisim Microarchitecture

### *Computer Organization & Systems Programming (CSEN402) - Milestone 2*

This repository contains the implementation of a custom Basic Computer designed to execute a specific iterative algorithm. Built using **Logisim**, the project focuses on the design of a **Hardwired Control Unit** and a specialized **Instruction Set Architecture (ISA)** to handle arithmetic, logic, and control flow.

---

## Project Objective

The goal was to modify a basic computer to execute a specific high-level loop efficiently:

```c
int A=16, B=2, C=0, i=-3; 
for(i=0; i<3; i++) {
    C += A & B;
    A += A * B;
}

```

This required implementing a control unit capable of managing timing signals () for a specific subset of instructions, including a custom **Multiply (MUL)** operation.

---

## Hardware Specifications

* **Word Size:** 16-bit instructions.
* **Address Space:** 7-bit Address Field (Bits 0-6).
* **Opcode:** 3-bit Opcode (Bits 12-14).
* **Control Unit:** Hardwired logic using a Sequence Counter (SC), 3-to-8 Decoders, and logic gates.
* **Synchronous Design:** Implemented Flip-Flops on `CLR` signals for registers to ensure synchronous clearing, overriding Logisim's default asynchronous behavior.

---

## Instruction Set & RTL

The following instructions were implemented to support the program requirements. All instructions transition from Fetch/Decode (T0) directly to Execution (T1) due to the removal of indirect memory referencing.

| Opcode | Mnemonic | Calculation | RTL Micro-operations |
| --- | --- | --- | --- |
| `000` | **AND** | AC ← AC ∧ M[addr] | DR ← M[addr], AC ← AC ∧ DR |
| `001` | **ADD** | AC ← AC + M[addr] | DR ← M[addr], AC ← AC + DR |
| `010` | **LDA** | AC ← M[addr] | DR ← M[addr], AC ← DR |
| `011` | **STA** | M[addr] ← AC | M[addr] ← AC |
| `100` | **BUN** | PC ← addr | PC ← addr |
| `101` | **MUL** | AC ← AC × M[addr] | DR ← M[addr], AC ← AC × DR |
| `110` | **ISZ** | M[addr] ← M[addr] + 1 | DR ← M[addr], DR ← DR + 1, M[addr] ← DR, if DR = 0 then PC ← PC + 1 |

---

## Program Logic (Assembly)

The CPU is pre-loaded with the following machine code to solve the project challenge:

```assembly
ORG 0
LOP, LDA A   / Load A into Accumulator
     AND B   / AC = A & B
     ADD C   / AC = (A & B) + C
     STA C   / Update C
     LDA A   / Reload A
     MUL B   / AC = A * B
     ADD A   / AC = (A * B) + A
     STA A   / Update A
     ISZ i   / Increment counter, skip jump if zero
     BUN LOP / Repeat loop
     HLT     / Terminate

```


### Key Implementation Detail: The MUL Instruction

For this milestone, I repurposed the `BSA` opcode to implement **`MUL`**. This required extending the ALU logic to handle multiplication and ensuring the Bus Selection signals () correctly routed the Data Register to the ALU during .

---

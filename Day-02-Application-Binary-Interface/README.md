
# Day 02 – Application Binary Interface (ABI) and RISC-V Assembly Programming

## 1. Objective

To understand the Application Binary Interface (ABI), RISC-V register architecture, memory organization, endianness, instruction formats and assembly language programming.

The practical session focused on understanding how a C program interacts with RISC-V assembly language, how function arguments are passed through registers, and how assembly instructions perform arithmetic operations.

---

## 2. Understanding Software and Hardware Interfaces

Software communicates with the underlying hardware through different interfaces and architectural layers.

### 2.1 Application Programming Interface (API)

An API (Application Programming Interface) provides an interface through which applications access functions and libraries.

**Examples:**
- `stdio.h` – Standard input/output functions in C.
- `printf()` – Displays formatted output.
- `scanf()` – Reads formatted input.

### 2.2 Application Binary Interface (ABI)

ABI (Application Binary Interface) defines how compiled programs interact at the binary level.

It specifies:
- Register usage.
- Function calling conventions.
- Argument passing.
- Return value handling.
- Stack management.
- Data type representation.

### 2.3 Instruction Set Architecture (ISA)

ISA defines the instructions supported by a processor.

RISC-V is an open and modular Instruction Set Architecture that defines instructions for arithmetic, logical operations, memory access and control flow.

### 2.4 Register Transfer Level (RTL)

RTL describes the digital hardware implementation of a processor using hardware description languages such as Verilog and SystemVerilog.

### Software-to-Hardware Relationship

Application → API → ABI → ISA → RTL → Hardware

---

## 3. RISC-V Register Architecture

RISC-V provides 32 general-purpose integer registers, numbered from `x0` to `x31`.

Each register is identified using a 5-bit register address in the instruction encoding.

### 3.1 Register Width

The register width depends on the RISC-V architecture:

| Architecture | Register Width |
|---|---|
| RV32I | 32 bits |
| RV64I | 64 bits |

### 3.2 RISC-V Register and ABI Naming Convention

| Register | ABI Name | Purpose |
|---|---|---|
| x0 | zero | Constant zero |
| x1 | ra | Return address |
| x2 | sp | Stack pointer |
| x3 | gp | Global pointer |
| x4 | tp | Thread pointer |
| x5 | t0 | Temporary register |
| x6 | t1 | Temporary register |
| x7 | t2 | Temporary register |
| x8 | s0 / fp | Saved register / Frame pointer |
| x9 | s1 | Saved register |
| x10 | a0 | Argument / Return value |
| x11 | a1 | Argument / Return value |
| x12 | a2 | Function argument |
| x13 | a3 | Function argument |
| x14 | a4 | Function argument |
| x15 | a5 | Function argument |
| x16 | a6 | Function argument |
| x17 | a7 | Function argument |
| x18–x27 | s2–s11 | Saved registers |
| x28–x31 | t3–t6 | Temporary registers |

### Important Register Conventions

- `x0 (zero)` always contains zero.
- `a0–a7` are used to pass function arguments.
- `a0` and `a1` are also used for returning values.
- `ra` stores the return address.
- `sp` manages the stack.

### 3.3 Caller-Saved and Callee-Saved Registers

**Caller-Saved Registers:**

The calling function must preserve the values if they are needed after a function call.

Examples: `ra`, `t0–t6`, `a0–a7`.

**Callee-Saved Registers:**

The called function must restore these registers to their original values before returning.

Examples: `s0–s11`.

---

## 4. Memory Organization and Endianness

Memory organization describes how data is stored and accessed in the system.

### 4.1 Data Representation

| Data Unit | Size |
|---|---|
| Bit | 1 bit |
| Byte | 8 bits |
| Halfword | 16 bits |
| Word | 32 bits |
| Doubleword | 64 bits |

Important points:
- Memory is byte-addressable.
- Each memory location has a unique address.
- A doubleword occupies 8 consecutive bytes.
- RV64I supports 64-bit doubleword operations.

### 4.2 Little Endian

In Little Endian representation, the Least Significant Byte (LSB) is stored at the lowest memory address.

### 4.3 Big Endian

In Big Endian representation, the Most Significant Byte (MSB) is stored at the lowest memory address.

**Example:**

Consider the 64-bit hexadecimal value:

`0x123456789ABCDEF0`

| Address | Little Endian | Big Endian |
|---|---|---|
| 0x1000 | F0 | 12 |
| 0x1001 | DE | 34 |
| 0x1002 | BC | 56 |
| 0x1003 | 9A | 78 |
| 0x1004 | 78 | 9A |
| 0x1005 | 56 | BC |
| 0x1006 | 34 | DE |
| 0x1007 | 12 | F0 |

---

## 5. RISC-V Load and Store Instructions

Load and store instructions are used to transfer data between memory and registers.

### 5.1 Load Doubleword (LD)

The `ld` instruction loads a 64-bit value from memory into a destination register.

**Syntax:**

```asm
ld rd, offset(rs1)
```

**Example:**

```asm
ld x8, 16(x23)
```

Explanation:
- `x8` – Destination register.
- `x23` – Base address register.
- `16` – Immediate offset.

Effective address:

`Address = Contents of x23 + 16`

The processor loads 8 bytes from the calculated memory address into `x8`.

### 5.2 Store Doubleword (SD)

The `sd` instruction stores a 64-bit value from a register into memory.

**Syntax:**

```asm
sd rs2, offset(rs1)
```

**Example:**

```asm
sd x8, 8(x23)
```

Explanation:
- `x8` – Source register.
- `x23` – Base address register.
- `8` – Immediate offset.

Effective address:

`Address = Contents of x23 + 8`

---

## 6. RISC-V Instruction Formats

RISC-V instructions use different formats depending on the operation.

The standard RV64I base instructions use 32-bit instruction encoding.

### 6.1 R-Type Instruction

Used for register-to-register operations.

**Example:**

```asm
add x8, x24, x8
```

Operation:

`x8 = x24 + x8`

Instruction fields:

| Field | Bit Positions |
|---|---|
| funct7 | [31:25] |
| rs2 | [24:20] |
| rs1 | [19:15] |
| funct3 | [14:12] |
| rd | [11:7] |
| opcode | [6:0] |

### 6.2 I-Type Instruction

Used for immediate operations and load instructions.

**Example:**

```asm
ld x8, 16(x23)
```

Instruction fields:

| Field | Bit Positions |
|---|---|
| Immediate | [31:20] |
| rs1 | [19:15] |
| funct3 | [14:12] |
| rd | [11:7] |
| opcode | [6:0] |

### 6.3 S-Type Instruction

Used for store operations.

**Example:**

```asm
sd x8, 8(x23)
```

Instruction fields:

| Field | Bit Positions |
|---|---|
| Immediate [11:5] | [31:25] |
| rs2 | [24:20] |
| rs1 | [19:15] |
| funct3 | [14:12] |
| Immediate [4:0] | [11:7] |
| opcode | [6:0] |

---

## 7. Practical Implementation – Sum of Numbers Using C and RISC-V Assembly

### 7.1 Objective

To understand how a C program calls an external RISC-V assembly function and how the sum of numbers is calculated using assembly instructions.

### 7.2 C Program – `1to9_custom.c`

```c
#include <stdio.h>

extern int load(int x, int y);

int main()
{
    int result = 0;
    int count = 9;

    result = load(0, count + 1);

    printf("Sum of numbers from 1 to %d is %d\n",
           count, result);

    return 0;
}
```

### 7.3 RISC-V Assembly Program – `load.S`

```asm
.section .text
.global load
.type load, @function

load:
    add  a4, a0, zero
    add  a2, a0, a1
    add  a3, a0, zero

loop:
    add  a4, a3, a4
    addi a3, a3, 1
    blt  a3, a2, loop

    add  a0, a4, zero
    ret
```

### 7.4 Explanation of Assembly Instructions

| Instruction | Description |
|---|---|
| `add` | Adds two register values |
| `addi` | Adds an immediate value |
| `blt` | Branches if one register is less than another |
| `ret` | Returns from a function |
| `zero` | Constant zero register |
| `a0` | First argument and return value |
| `a1` | Second argument |
| `a2–a4` | Registers used in the calculation |

### 7.5 Program Execution Flow

1. The C program initializes `result = 0`.
2. The variable `count` is initialized to 9.
3. The function `load(0, count + 1)` is called.
4. Arguments are passed through registers `a0` and `a1`.
5. The assembly function initializes the working registers.
6. The loop adds the numbers from 0 to 9.
7. The `blt` instruction checks the loop condition.
8. The final result is transferred to `a0`.
9. The `ret` instruction returns control to the C program.
10. The C program displays the result using `printf()`.

### 7.6 Expected Output

```text
Sum of numbers from 1 to 9 is 45
```

---

## 8. Compilation and Assembly Code Analysis

The RISC-V GCC cross-compiler is used to compile C and assembly programs for the RISC-V architecture.

### Compilation Command

```bash
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o 1to9_custom.elf 1to9_custom.c load.S
```

### Execution Using Spike Simulator

```bash
spike pk 1to9_custom.elf
```

### Viewing Disassembled Instructions

```bash
riscv64-unknown-elf-objdump -d 1to9_custom.elf | less
```

### Understanding the Disassembly

The object dump utility displays the machine instructions generated by the compiler.

It helps to understand:
- How C code is translated into machine instructions.
- How function calls are implemented.
- How registers are used during execution.
- How arithmetic and branch instructions are represented.
- How assembly instructions correspond to machine code.

---

## 9. Tools and Technologies Used

| Tool / Technology | Purpose |
|---|---|
| Ubuntu Linux | Development environment |
| RISC-V GCC | Cross-compilation |
| Spike Simulator | RISC-V instruction simulation |
| GNU Objdump | Disassembly analysis |
| C Programming | Application development |
| RISC-V Assembly | Low-level programming |
| Linux Terminal | Executing commands |

---

## 10. Key Learning Outcomes

- Understood the difference between API and ABI.
- Learned the relationship between software interfaces and hardware architecture.
- Studied the 32 general-purpose RISC-V registers.
- Understood register naming conventions and their purposes.
- Learned caller-saved and callee-saved register conventions.
- Understood memory organization and byte-addressable memory.
- Studied Little Endian and Big Endian representation.
- Learned RISC-V load and store instructions.
- Understood R-Type, I-Type and S-Type instruction formats.
- Practiced understanding C and RISC-V assembly interaction.
- Learned how function arguments are passed through ABI registers.
- Understood how assembly instructions implement loops and arithmetic operations.
- Explored compilation and disassembly using RISC-V development tools.

---

## 11. Conclusion

Day 02 provided a deeper understanding of the Application Binary Interface, RISC-V register architecture, memory organization and instruction formats.

The practical exercise demonstrated how a C program interacts with an assembly function through ABI-defined registers and calling conventions.

By studying RISC-V assembly instructions, compilation and disassembly, I gained a better understanding of how high-level C programs are translated into low-level processor instructions.

This session helped strengthen my understanding of the connection between software, assembly language and processor architecture.


# Day 1 – Introduction to RISC-V ISA and GNU Compiler Toolchain

## 1. Objective

To understand the fundamentals of RISC-V Instruction Set Architecture (ISA), GNU compiler toolchain, software-to-hardware execution flow, application binary interface (ABI), integer representation and execution of C programs using the RISC-V Spike simulator.

The session included practical implementation of C programs, native compilation, RISC-V cross-compilation, assembly code analysis, optimization and simulation.

## 2. Introduction to RISC-V Architecture

RISC-V (Reduced Instruction Set Computer – Five) is an open-standard Instruction Set Architecture (ISA). It defines the instructions that a processor can understand and execute.

An ISA acts as an interface between software and hardware. It provides a common specification followed by software developers and hardware designers.

### 2.1 Software-to-Hardware Execution Flow

A computer system consists of three major layers:

**1. Application Software**
- Applications are written using high-level programming languages such as C and C++.
- These programs perform user-defined operations.

**2. System Software**
- Includes the operating system, compiler, assembler and other system utilities.
- Manages memory, I/O operations and system resources.

**3. Hardware**
- The processor executes machine instructions.
- Performs arithmetic, logical and control operations.

### Working Principle

1. Application programs are written in high-level languages.
2. The compiler translates source code into assembly instructions.
3. The assembler converts assembly instructions into machine code.
4. The linker combines required object files and libraries to generate an executable.
5. The processor fetches, decodes and executes machine instructions.

The GNU toolchain performs preprocessing, compilation, assembling and linking to generate an executable in ELF format.

The ISA defines the instructions understood by the processor and connects software with hardware.

## 3. RISC-V Instruction Set Architecture

RISC-V follows a modular instruction-set architecture consisting of a base integer instruction set and optional extensions.

### 3.1 RISC-V Extensions

| Extension | Description |
|---|---|
| RV64I | Base 64-bit integer instruction set |
| RV64M | Multiplication and division extension |
| RV64F | Single-precision floating-point extension |
| RV64D | Double-precision floating-point extension |
| RV64IM | Integer and multiplication/division extensions |
| RV64IMFD | Integer, multiplication/division and floating-point extensions |

### 3.2 Pseudo Instructions

Pseudo instructions are assembly-language shortcuts provided by the assembler. They make assembly programs easier to read and write.

| Instruction | Description |
|---|---|
| mv | Moves data from one register to another |
| li | Loads a constant value into a register |
| ret | Returns control to the calling function |

Examples:
- `mv` can be translated into an `addi` instruction.
- `li` may expand into one or more instructions depending on the constant.
- `ret` is an alias for `jalr x0, 0(ra)`.

### 3.3 Base Integer Instructions – RV64I

RV64I provides arithmetic, logical, data-transfer and control-flow operations.

| Instruction | Description |
|---|---|
| ADDI | Adds an immediate value to a register |
| LUI | Loads an upper immediate value into a register |
| AUIPC | Adds an upper immediate value to the program counter |
| JAL | Jump and Link |
| JALR | Performs an indirect jump and stores the return address |
| LD | Loads a 64-bit doubleword from memory |
| SD | Stores a 64-bit value into memory |
| LW | Loads a 32-bit word and sign-extends it to 64 bits |
| ADDW | Adds two 32-bit values and sign-extends the result |

### 3.4 Multiplication Extension – RV64M

The M extension adds multiplication and division operations to the base integer instruction set.

| Instruction | Description |
|---|---|
| MULW | Multiplies two 32-bit values |
| DIVW | Performs signed division on 32-bit values |

Combining the base integer and multiplication/division extensions:

`RV64I + RV64M = RV64IM`

### 3.5 Floating-Point Extensions

**RV64F – Single-Precision Floating Point**

Supports 32-bit floating-point operations.

| Instruction | Description |
|---|---|
| FLW | Loads a 32-bit floating-point value |
| FADD.S | Performs single-precision addition |
| FMUL.S | Performs single-precision multiplication |
| FDIV.S | Performs single-precision division |
| FMV.X.W | Moves the bit representation of a single-precision value into an integer register |

**RV64D – Double-Precision Floating Point**

Supports 64-bit floating-point operations.

| Instruction | Description |
|---|---|
| FLD | Loads a 64-bit double-precision floating-point value |
| FMV.X.D | Moves the bit representation of a double-precision value into an integer register on RV64 |

Combining the integer, multiplication/division and floating-point extensions:

`RV64I + M + F + D = RV64IMFD`

## 4. Application Binary Interface (ABI)

The Application Binary Interface (ABI) defines how software interacts with the processor at the binary level.

It specifies:
- Register usage.
- Function calling conventions.
- Stack management.
- Data representation.

### Important RISC-V ABI Registers

| ABI Name | Register | Purpose |
|---|---|---|
| a0 | x10 | Function argument and return value |
| sp | x2 | Stack pointer |
| ra | x1 | Return address |
| s0 | x8 | Saved register / frame pointer |

### 4.1 Memory Allocation and Stack Pointer

The stack is a memory region used for storing temporary data, function-call information, local variables and saved registers.

The stack pointer (`sp`) holds the address of the current top of the stack.

In RISC-V, the stack grows towards lower memory addresses.

Example:

```assembly
addi sp, sp, -32
```

This instruction decreases the stack pointer by 32 bytes, allocating 32 bytes of stack space.

To restore the stack pointer:

```assembly
addi sp, sp, 32
```

This releases the allocated 32 bytes.

## 5. GNU Compiler Toolchain

The GNU compiler toolchain is used to compile and execute programs targeting the RISC-V architecture.

### Tools Used

| Tool | Purpose |
|---|---|
| GCC | Compiles C programs |
| RISC-V GCC | Cross-compiles programs for RISC-V |
| GNU ObjDump | Displays assembly instructions |
| Spike | RISC-V ISA simulator |
| Proxy Kernel (pk) | Provides an execution environment |
| Ubuntu Linux | Operating system |
| Leafpad | Text editor |

## 6. Practical Implementation

### Experiment 1 – Sum of First N Integers

**Objective:** To calculate the sum of integers from 1 to N using a C program.

**File:** `sum1ton.c`

### Source Code

```c
#include <stdio.h>

int main()
{
    int i, sum = 0, n = 5;

    for(i = 0; i <= n; ++i)
    {
        sum += i;
    }

    printf("Sum is: %d\n", sum);

    return 0;
}
```

### Step 1: Create and Execute the C Program

```bash
leafpad sum1ton.c
gcc sum1ton.c
./a.out
```

**Output:**

```text
Sum is: 15
```

### Step 2: RISC-V Cross Compilation

Compile the program for the RV64I architecture using O1 optimization.

```bash
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton_O1.elf sum1ton.c
```

**Command Explanation:**

| Option | Description |
|---|---|
| riscv64-unknown-elf-gcc | RISC-V cross-compiler |
| -O1 | Enables level 1 optimization |
| -mabi=lp64 | Specifies LP64 ABI |
| -march=rv64i | Targets RV64I architecture |
| -o | Specifies output filename |
| sum1ton.c | Input C source file |

Compile using Ofast optimization:

```bash
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum1ton_Ofast.elf sum1ton.c
```

`-Ofast` enables aggressive optimizations beyond the normal `-O3` level.

### Step 3: Assembly Code Analysis

To view the generated RISC-V assembly instructions:

```bash
riscv64-unknown-elf-objdump -d sum1ton_O1.elf | less
```

To inspect the Ofast version:

```bash
riscv64-unknown-elf-objdump -d sum1ton_Ofast.elf | less
```

Press `q` to exit the less viewer.

The disassembly displays instruction addresses, machine instructions and corresponding assembly mnemonics.

### Step 4: Verifying the Number of Instructions

For the RV64I target, standard instructions are 32 bits (4 bytes) long.

Calculation:

```text
Number of instructions =
(Last instruction address - First instruction address) / 4 + 1
```

The `+1` includes the instruction at the starting address.

This helps understand how many machine instructions are generated by the compiler.

### Step 5: Execute Using Spike Simulator

Normal execution:

```bash
spike pk sum1ton_O1.elf
```

Debugging mode:

```bash
spike -d pk sum1ton_O1.elf
```

The Spike simulator executes the compiled RISC-V program and displays its output.

**Expected Output:**

```text
Sum is: 15
```

### Step 6: Debugging and Register Inspection

Commands explored:

```text
until pc 0 100b0
reg 0 a0
reg 0 a1
```

Command explanations:

| Command | Purpose |
|---|---|
| until pc 0 100b0 | Runs until the specified program counter address |
| reg 0 a0 | Displays the value of register a0 |
| reg 0 a1 | Displays the value of register a1 |

Learned to:
- Control execution using the Program Counter (PC).
- Inspect register contents.
- Observe instruction execution.
- Understand register operations.

### Experiment 1 – Observation

The C program produced the same output (15) using native GCC and the RISC-V toolchain.

The generated assembly instructions were inspected using GNU ObjDump, and the program was executed using Spike.

## 7. Integer Number Representation

Studied signed and unsigned integer representation in a 64-bit RISC-V architecture.

### 7.1 Basic Data Units

| Unit | Size |
|---|---|
| Bit | 1 bit |
| Byte | 8 bits |
| Halfword | 16 bits |
| Word | 32 bits |
| Doubleword | 64 bits |

- LSB – Least Significant Bit.
- MSB – Most Significant Bit.
- 1 Byte = 8 Bits.
- 1 Word = 4 Bytes.
- 1 Doubleword = 8 Bytes.

Total number of possible patterns for n bits:

`2^n`

### 7.2 Unsigned Integer Representation

Unsigned integers represent only non-negative numbers.

For an n-bit unsigned number:

`Range = 0 to 2^n - 1`

For a 64-bit unsigned integer:

`Range = 0 to 2^64 - 1`

Maximum unsigned 64-bit value:

```text
18446744073709551615
```

If an unsigned operation exceeds its representable range, the result wraps around modulo 2^64 for 64-bit arithmetic.

### 7.3 Signed Integer Representation – 2's Complement

Signed integers represent both positive and negative numbers using 2's complement representation.

- MSB = 0 indicates a non-negative number.
- MSB = 1 indicates a negative number.

For an n-bit signed number:

`Range = -2^(n-1) to 2^(n-1) - 1`

For a 64-bit signed integer:

```text
Minimum = -9223372036854775808
Maximum =  9223372036854775807
```

**Steps to represent a negative number using 2's complement:**

1. Convert the positive number into binary.
2. Invert all bits (1's complement).
3. Add 1 to obtain the 2's complement representation.

### 7.4 Practical Experiment – Maximum and Minimum Integer Values

**Program 2: Finding Maximum Unsigned Integer**

File: `unsignedHighest.c`

```c
#include <stdio.h>
#include <math.h>

int main()
{
    unsigned long long int max =
        (unsigned long long int)(pow(2,64) - 1);

    printf("Highest Number is %llu\n", max);

    return 0;
}
```

RISC-V compilation:

```bash
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o unsignedHighest.o unsignedHighest.c
```

Spike execution:

```bash
spike pk unsignedHighest.o
```

Expected mathematical maximum:

```text
18446744073709551615
```

**Program 3: Finding Maximum and Minimum Signed Integer**

File: `signedHighest.c`

RISC-V compilation:

```bash
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o signedHighest.o signedHighest.c
```

Spike execution:

```bash
spike pk signedHighest.o
```

Observed Output:

```text
Highest Number is 2147483647
Lowest Number is -2147483648
```

The observed values correspond to the 32-bit signed integer range.

## 8. RISC-V Assembly Instructions Studied

| Instruction | Description |
|---|---|
| ADDI | Add Immediate |
| LUI | Load Upper Immediate |
| AUIPC | Add Upper Immediate to PC |
| LD | Load Doubleword |
| SD | Store Doubleword |
| LW | Load Word |
| ADDW | Add Word |
| JAL | Jump and Link |
| JALR | Jump and Link Register |
| RET | Return from function |
| LI | Load Immediate |
| MV | Move Register |

These instructions were studied through the generated assembly code and compiler output.

## 9. Execution and Debugging Using Spike

Spike is a RISC-V ISA simulator used to execute and debug RISC-V programs.

### Normal Execution

```bash
spike pk sum1ton_O1.elf
```

### Debugging Mode

```bash
spike -d pk sum1ton_O1.elf
```

Debugging helps to:
- Control execution using the Program Counter.
- Inspect register values.
- Observe instruction execution.
- Understand how data is processed inside the processor.

The Proxy Kernel (`pk`) provides a basic execution environment for the program.

## 10. Practical Work Summary

| Sl. No. | Experiment | Status |
|---|---|---|
| 1 | C program compilation and execution | Completed |
| 2 | Addition of first five numbers | Completed |
| 3 | RISC-V cross-compilation | Completed |
| 4 | O1 and Ofast optimization | Practiced |
| 5 | Assembly code generation and analysis | Completed |
| 6 | Instruction address verification | Practiced |
| 7 | Execution using Spike simulator | Completed |
| 8 | Spike debugging and register inspection | Practiced |
| 9 | Unsigned integer representation | Completed |
| 10 | Signed integer representation | Completed |
| 11 | ABI and register understanding | Studied |
| 12 | Stack pointer and memory allocation | Studied |

## 11. Key Learning Outcomes

Through Day 1 of the RISC-V workshop, I learned:

1. **RISC-V ISA Fundamentals:** Understood how the instruction set acts as an interface between software and hardware.

2. **Software-to-Hardware Flow:** Learned how application programs are translated into machine instructions and executed by the processor.

3. **Instruction Classification:** Explored pseudo instructions, RV64I, RV64M, RV64F and RV64D extensions.

4. **ABI and Registers:** Understood the purpose of registers such as a0, sp, ra and s0, along with their role in function calls and program execution.

5. **GNU Compiler Toolchain:** Practiced compiling C programs using native GCC and RISC-V GCC.

6. **Compiler Optimization:** Compared O1 and Ofast compilation and explored their impact on generated assembly instructions.

7. **Assembly Analysis:** Used GNU ObjDump to inspect RISC-V machine instructions.

8. **Spike Simulation:** Executed RISC-V programs using the Spike simulator and explored debugging commands.

9. **Register Inspection:** Learned to observe Program Counter values and inspect register contents.

10. **Integer Representation:** Understood signed and unsigned integer representation, 64-bit integer ranges and 2's complement representation.

11. **Memory Management:** Learned the basic concepts of ABI, stack allocation and stack pointer operations.

## 12. Conclusion

Day 1 provided a foundation in RISC-V architecture and its GNU compiler toolchain.

Through practical experiments, I learned how to write and compile C programs, perform RISC-V cross-compilation, generate and analyze assembly instructions, compare compiler optimization levels and execute programs using the Spike simulator.

I also gained an understanding of RISC-V instruction extensions, ABI, register usage, stack management and signed and unsigned integer representation.

This session provided the fundamental knowledge required for further learning in RISC-V processor architecture and hardware design.

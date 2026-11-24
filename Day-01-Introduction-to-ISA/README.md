
# Day 1 – Introduction to RISC-V Architecture and GNU Compiler Toolchain

## 1. Introduction

RISC-V is an open-source Instruction Set Architecture (ISA) based on Reduced Instruction Set Computer (RISC) principles. It provides a simple, modular and extensible architecture for processor design.

The first day focused on understanding the software-to-hardware execution flow using C programming, RISC-V assembly language, GNU compiler toolchain, object file generation, assembly-level code inspection and Spike simulation.

The practical session involved compiling C and assembly programs, executing RISC-V binaries using the Spike simulator, understanding the generated machine instructions and exploring the RISC-V workshop collateral.

---

## 2. Understanding the RISC-V GNU Compiler Toolchain

The GNU Compiler Toolchain provides the necessary tools to compile, assemble, link and inspect programs developed for RISC-V processors.

### 2.1 Important Tools

| Tool | Purpose |
|---|---|
| RISC-V GCC | Compiles C programs for RISC-V |
| GNU Assembler | Converts assembly code into object files |
| GNU Linker | Links object files and libraries |
| GNU Objdump | Displays disassembled machine instructions |
| Spike | RISC-V instruction set simulator |
| Proxy Kernel (pk) | Provides an execution environment for applications |

### 2.2 Compilation Flow

The basic RISC-V compilation flow is:

1. Write the C program.
2. Write the assembly program.
3. Compile the C and assembly source files.
4. Generate the executable ELF file.
5. Execute the program using Spike.
6. Inspect the generated machine instructions using Objdump.

---

## 3. Practical Implementation – Sum of Numbers Using C and RISC-V Assembly

### 3.1 Objective

To implement a program that calculates the sum of numbers using a C program and an external RISC-V assembly function.

### 3.2 C Program – `1to9_custom.c`

The C program calls an external assembly function named `load()` to calculate the sum.

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

**Program Explanation:**

- `stdio.h` is included for the `printf()` function.
- `load()` is declared as an external assembly function.
- `count` is initialized to 9.
- The assembly function is called with arguments 0 and 10.
- The returned result is displayed using `printf()`.

**Screenshot – C Program**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_08_37_48" src="https://github.com/user-attachments/assets/7c7155de-e1e7-47ca-92ba-c1b09a1d58aa" />

### 3.3 RISC-V Assembly Program – `load.S`

The assembly program implements the addition operation using RISC-V instructions.

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

**Instruction Explanation:**

| Instruction | Purpose |
|---|---|
| `add` | Adds two register values |
| `addi` | Adds an immediate value |
| `blt` | Branches if one register is less than another |
| `ret` | Returns from the function |
| `zero` | Constant zero register |
| `a0`, `a1` | Function argument registers |

**Screenshot – Assembly Program**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_08_45_58" src="https://github.com/user-attachments/assets/b4d4f79d-cec3-44eb-bc2f-54242286821f" />

---

## 4. Compilation Using RISC-V GCC

The RISC-V GCC cross-compiler is used to compile the C and assembly programs into an executable ELF file.

### 4.1 Compilation Command

```bash
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o 1to9_custom.elf 1to9_custom.c load.S
```

### 4.2 Execution Using Spike Simulator

```bash
spike pk 1to9_custom.elf
```

### 4.3 Expected Output

```text
Sum of numbers from 1 to 9 is 45
```

**Screenshot – Compilation and Execution**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_01_49" src="https://github.com/user-attachments/assets/67aa316f-cbe8-4436-b774-66354c0bfdf9" />

---

## 5. Understanding Object Files and Disassembly

After compilation, the generated ELF file contains machine instructions that can be inspected using GNU Objdump.

### 5.1 Viewing Disassembled Instructions

```bash
riscv64-unknown-elf-objdump -d 1to9_custom.elf | less
```

### 5.2 Understanding Disassembly

Disassembly helps to understand how high-level C instructions are translated into low-level RISC-V machine instructions.

It provides information about:

- Instruction addresses.
- Machine code representations.
- Assembly instructions.
- Register usage.
- Function calls and return instructions.
- Branch and arithmetic operations.

**Screenshot – RISC-V Disassembly**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_03_44" src="https://github.com/user-attachments/assets/3c7ab9fb-3baf-422a-a309-6ae5684df5f4" />

---

## 6. Exploring RISC-V Workshop Collateral

The RISC-V workshop collateral directory contains the required source files, scripts, testbench files and toolchain resources used during the practical session.

### 6.1 Exploring the Directory

```bash
cd ~/riscv_workshop_collaterals
ls -ltr
```

### 6.2 Exploring the Labs Directory

```bash
cd labs
ls -ltr
```

The labs directory contains files such as:

| File | Description |
|---|---|
| `testbench.v` | Verilog testbench |
| `syscalls.c` | System call implementation |
| `start.S` | Startup assembly code |
| `rv32im.sh` | RISC-V execution script |
| `riscv.ld` | Linker script |
| `picorv32.v` | PicoRV32 processor source |
| `hello.c` | C program |
| `1to9_custom.c` | Custom C program |
| `load.S` | Assembly function |

**Screenshot – Workshop Directory Structure**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_11_33" src="https://github.com/user-attachments/assets/962fb064-6469-4049-8038-b98ce54a6759" />

---

## 7. Understanding the RISC-V Execution Script

The `rv32im.sh` script contains the commands required to compile the source files and generate the firmware image.

### 7.1 Compilation and Linking Flow

The script performs the following operations:

1. Compiles the C source file.
2. Compiles the assembly source file.
3. Compiles the system call implementation.
4. Links the object files with the linker script.
5. Generates the ELF executable.
6. Converts the ELF file into a Verilog-compatible firmware image.
7. Generates the testbench simulation executable.

### 7.2 Execution Script

The script uses commands similar to the following:

```bash
riscv64-unknown-elf-gcc -c -mabi=ilp32 -march=rv32im -o 1to9_custom.o 1to9_custom.c

riscv64-unknown-elf-gcc -c -mabi=ilp32 -march=rv32im -o load.o load.S

riscv64-unknown-elf-gcc -c -mabi=ilp32 -march=rv32im -o syscalls.o syscalls.c

riscv64-unknown-elf-gcc -mabi=ilp32 -march=rv32im -Wl,--gc-sections -o firmware.elf 1to9_custom.o load.o syscalls.o -T riscv.ld -lstdc++

riscv64-unknown-elf-objcopy -O verilog firmware.elf firmware.tmp

python3 hex8tohex32.py firmware.hex > firmware32.hex

iverilog -o testbench.vvp testbench.v picorv32.v

vvp -N testbench.vvp
```

**Screenshot – RISC-V Script**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_14_57" src="https://github.com/user-attachments/assets/5ee95e8c-7f52-481d-b7b1-0ae839f6649a" />
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_17_21" src="https://github.com/user-attachments/assets/62ba26cd-aace-47ed-bccd-06227bb0ff43" />

---

## 8. Modifying and Running the C Program

The C program was modified to change the number of terms used in the summation.

### 8.1 Modified C Program

The count variable was changed from 9 to 3.

```c
#include <stdio.h>

extern int load(int x, int y);

int main() {
    int result = 0;
    int count = 3;

    result = load(0, count + 1);

    printf("Sum of number from 1 to %d is %d\n",
           count, result);
}
```

### 8.2 Execution

```bash
chmod 777 rv32im.sh
./rv32im.sh
```

### 8.3 Output

```text
Sum of number from 1 to 3 is 6
TRAP
```

**Screenshot – Modified Program Execution**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_25_19" src="https://github.com/user-attachments/assets/f024e874-8004-4f70-996a-117d286aa35f" />
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_25_58" src="https://github.com/user-attachments/assets/2aa337dc-bd4d-4777-a166-932807ace504" />

---

## 9. Firmware Generation and Inspection

The firmware image is generated from the compiled ELF file and is used by the processor simulation environment.

### 9.1 Firmware Files

The following files are generated during the build process:

- `firmware.elf`
- `firmware.hex`
- `firmware32.hex`
- `testbench.vvp`

### 9.2 Inspecting Firmware Hexadecimal Data

```bash
vim firmware.hex
```

The hexadecimal file contains the machine-code representation of the compiled program.

**Screenshot – Firmware Hexadecimal Output**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_27_02" src="https://github.com/user-attachments/assets/110e51e1-9a21-4e87-9275-31d73425e76f" />
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_27_48" src="https://github.com/user-attachments/assets/d3d7b268-b9c9-482a-9cca-914aba2a03ba" />

---

## 10. Verilog Testbench and Processor Simulation

A Verilog testbench is used to provide the simulation environment for the PicoRV32 processor.

### 10.1 Testbench – `testbench.v`

The testbench instantiates the PicoRV32 processor and connects the required clock, reset and memory interface signals.

**Screenshot – Verilog Testbench**
<img width="955" height="1030" alt="VirtualBox_Risc-v_24_11_2026_09_28_52" src="https://github.com/user-attachments/assets/857c3202-d106-474f-b99f-a1af1fc5e394" />

### 10.2 Running the Simulation

```bash
iverilog -o testbench.vvp testbench.v picorv32.v
vvp -N testbench.vvp
```

The simulation executable runs the testbench and displays the processor execution output.

---

## 11. Tools and Technologies Used

| Tool / Technology | Purpose |
|---|---|
| Ubuntu Linux | Development environment |
| RISC-V GCC | Cross-compilation |
| GNU Assembler | Assembly compilation |
| GNU Objdump | Disassembly analysis |
| Spike Simulator | RISC-V instruction simulation |
| Proxy Kernel | Application execution environment |
| Icarus Verilog | Verilog compilation |
| VVP | Simulation execution |
| PicoRV32 | RISC-V processor core |
| C Programming | Application development |
| RISC-V Assembly | Low-level programming |

---

## 12. Key Learning Outcomes

- Understood the fundamentals of RISC-V architecture.
- Learned about the GNU RISC-V compiler toolchain.
- Understood the C and assembly programming interface.
- Practiced writing and compiling C programs.
- Learned how to write RISC-V assembly functions.
- Understood function arguments and return values using registers.
- Practiced cross-compilation using RISC-V GCC.
- Learned to generate and execute ELF files.
- Executed RISC-V programs using the Spike simulator.
- Inspected machine instructions using GNU Objdump.
- Explored RISC-V workshop collateral and lab files.
- Understood firmware generation and hexadecimal file formats.
- Explored Verilog testbench-based processor simulation.

---

## 13. Conclusion

Day 1 provided a practical introduction to RISC-V architecture and the GNU compiler toolchain.

The exercises demonstrated how C programs and assembly functions can be compiled into RISC-V executable files and executed using simulation tools.

The session also introduced the RISC-V workshop environment, firmware generation, instruction-level analysis and processor simulation using Verilog.

This practical experience helped develop a basic understanding of the software-to-hardware execution flow and the tools used in RISC-V processor development.

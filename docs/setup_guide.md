# RISC-V Toolchain and Spike Setup Guide

This document describes the installation and configuration of the RISC-V GNU toolchain, Spike RISC-V ISA simulator, and RISC-V Proxy Kernel (PK) on Ubuntu.

It also demonstrates how to compile a C program targeting the RISC-V architecture and execute it using Spike.

## 1. Overview

The objective of this setup is to establish a cross-compilation and simulation environment for RISC-V software development.

The workflow consists of the following components:

| Component | Purpose |
|---|---|
| RISC-V GCC | Cross-compiles C programs into RISC-V executables |
| GNU Binutils | Provides tools for inspecting RISC-V binaries |
| Spike | RISC-V Instruction Set Architecture (ISA) simulator |
| Proxy Kernel (PK) | Provides basic execution environment and system-call support |
| Ubuntu | Host development environment |

### Execution Workflow

```text
       C Source Code
        (hello.c)
            |
            v
     RISC-V GCC Compiler
            |
            v
     RISC-V ELF Executable
         (hello)
            |
            v
      Proxy Kernel (PK)
            |
            v
     Spike ISA Simulator
            |
            v
       Program Output
```

## 2. Environment

The setup was performed using:

- Operating System: Ubuntu Linux
- Architecture: x86-64 host
- Target Architecture: RISC-V 64-bit
- Compiler: `riscv64-unknown-elf-gcc`
- ISA Simulator: Spike
- ABI: LP64D
- Target ISA: RV64GC

The compiler and simulator target RISC-V, even though the development environment runs on an x86-64 machine.

## 3. Install the RISC-V GNU Toolchain

Update the Ubuntu package lists:

```bash
sudo apt update
```

Install the RISC-V cross-compiler and GNU Binutils:

```bash
sudo apt install gcc-riscv64-unknown-elf \
binutils-riscv64-unknown-elf -y
```

Verify the installation:

```bash
riscv64-unknown-elf-gcc --version
```

```bash
riscv64-unknown-elf-objdump --version
```

The compiler and disassembler should display their respective version information.

The following tools are now available:

- `riscv64-unknown-elf-gcc`: RISC-V cross-compiler.
- `riscv64-unknown-elf-objdump`: Binary disassembler.
- `riscv64-unknown-elf-readelf`: ELF file inspection utility.

## 4. Install Spike Dependencies

Spike is built from its source repository.

Install the required development dependencies:

```bash
sudo apt install -y \
    git \
    build-essential \
    autoconf \
    automake \
    autotools-dev \
    curl \
    libmpc-dev \
    libmpfr-dev \
    libgmp-dev \
    gawk \
    bison \
    flex \
    texinfo \
    gperf \
    libtool \
    patchutils \
    bc \
    zlib1g-dev \
    device-tree-compiler \
    libboost-regex-dev \
    libboost-system-dev \
    libboost-dev \
    pkg-config
```

## 5. Build and Install Spike

### 5.1 Create the workspace

```bash
mkdir -p ~/riscv-tools
cd ~/riscv-tools
```

### 5.2 Clone the Spike repository

Clone the official repository with its submodules:

```bash
git clone --recursive \
https://github.com/riscv-software-src/riscv-isa-sim.git
```

The recursive clone is important because Spike depends on external source components, including Berkeley SoftFloat.

### 5.3 Configure the installation directory

Set the installation prefix:

```bash
export RISCV="$HOME/riscv-tools/install"
```

Create the installation directory:

```bash
mkdir -p "$RISCV"
```

### 5.4 Configure Spike

Navigate into the source directory:

```bash
cd ~/riscv-tools/riscv-isa-sim
```

Create and enter the build directory:

```bash
mkdir -p build
cd build
```

Configure the build:

```bash
../configure --prefix="$RISCV"
```

### 5.5 Build and install

Compile Spike using available CPU cores:

```bash
make -j$(nproc)
```

Install the compiled binaries:

```bash
make install
```

### 5.6 Configure PATH

Add Spike to the system PATH:

```bash
echo 'export PATH="$HOME/riscv-tools/install/bin:$PATH"' >> ~/.bashrc
```

Apply the updated configuration:

```bash
source ~/.bashrc
```

Verify the installation:

```bash
which spike
```

Expected path:

```text
/home/<username>/riscv-tools/install/bin/spike
```

Check the available options:

```bash
spike --help
```

## 6. Install the RISC-V Proxy Kernel

The Proxy Kernel provides basic runtime services required by programs that use system calls such as `printf()`.

### 6.1 Clone the repository

Navigate to the RISC-V workspace:

```bash
cd ~/riscv-tools
```

Clone the Proxy Kernel repository:

```bash
git clone https://github.com/riscv-software-src/riscv-pk.git
```

### 6.2 Configure the build

Navigate into the source directory:

```bash
cd ~/riscv-tools/riscv-pk
```

Create the build directory:

```bash
mkdir -p build
cd build
```

Configure PK:

```bash
../configure \
    --prefix="$HOME/riscv-tools/install" \
    --host=riscv64-unknown-elf
```

### 6.3 Build and install

```bash
make -j$(nproc)
```

```bash
make install
```

Verify that the Proxy Kernel executable exists:

```bash
ls ~/riscv-tools/install/riscv64-unknown-elf/bin/pk
```

The command should display the installed PK executable path.

## 7. Create a Test Program

Create a directory for the test application:

```bash
mkdir -p ~/riscv-tools/hello
cd ~/riscv-tools/hello
```

Create the C source file:

```bash
nano hello.c
```

Add the following program:

```c
#include <stdio.h>

int main() {
    printf("Hello RISC-V!\n");
    printf("Running C code on Spike.\n");

    return 0;
}
```

Save the file.

## 8. Compile the RISC-V Program

Compile the program using the RISC-V cross-compiler:

```bash
riscv64-unknown-elf-gcc \
    -march=rv64gc \
    -mabi=lp64d \
    hello.c \
    -o hello
```

### Compiler Options

| Option | Description |
|---|---|
| `-march=rv64gc` | Targets RV64 with G and C extensions |
| `-mabi=lp64d` | Uses the 64-bit ABI with double-precision floating-point support |
| `hello.c` | Input C source file |
| `-o hello` | Generates an executable named `hello` |

### Verify the executable

```bash
file hello
```

Expected output should identify the file as a 64-bit RISC-V ELF executable.

### Inspect the ELF metadata

```bash
riscv64-unknown-elf-readelf -h hello
```

This displays information such as:

- ELF class
- Target machine architecture
- Entry point address
- ELF file type

### Inspect the generated assembly

```bash
riscv64-unknown-elf-objdump -d hello
```

This displays the RISC-V instructions generated by the compiler.

Examples of instructions include:

- `addi`
- `auipc`
- `jal`
- `ret`

## 9. Execute the Program Using Spike

Navigate to the application directory:

```bash
cd ~/riscv-tools/hello
```

Execute the program using Spike and the installed Proxy Kernel:

```bash
spike \
    ~/riscv-tools/install/riscv64-unknown-elf/bin/pk \
    ./hello
```

Spike uses its default ISA configuration for this successful execution.

### Expected Output

```text
Hello RISC-V!
Running C code on Spike.
```

Successful execution confirms that:

1. The RISC-V cross-compiler generated a valid executable.
2. The executable targets the RISC-V architecture.
3. The Proxy Kernel was able to load and execute the application.
4. Spike successfully simulated the RISC-V instructions.

## 10. Troubleshooting

During the setup process, the following issues were encountered and resolved.

### Issue 1: Missing Berkeley SoftFloat files

**Error:**

```text
No rule to make target:
berkeley-softfloat-3/source/include/softfloat.h
```

**Cause:**

The required Git submodule was missing or its local metadata was corrupted.

**Resolution:**

Clone Spike again with all submodules:

```bash
git clone --recursive \
https://github.com/riscv-software-src/riscv-isa-sim.git
```

Verify the SoftFloat header:

```bash
ls berkeley-softfloat-3/source/include/softfloat.h
```

### Issue 2: Spike command not found

**Error:**

```text
spike: command not found
```

**Resolution:**

Add the Spike installation directory to PATH:

```bash
export PATH="$HOME/riscv-tools/install/bin:$PATH"
```

### Issue 3: Illegal instruction exception

**Observed error:**

```text
exception trap_illegal_instruction
```

An initial execution using an explicitly specified ISA:

```bash
spike --isa=rv64gc ...
```

resulted in an illegal-instruction exception in this environment.

Execution succeeded using Spike's default ISA configuration:

```bash
spike \
    ~/riscv-tools/install/riscv64-unknown-elf/bin/pk \
    ./hello
```

The precise cause of the initial exception was not conclusively established.

**Note:** ISA compatibility should be checked when using different compiler, Proxy Kernel and simulator configurations.

### Issue 4: Output redirected to a log file

When using:

```bash
> spike.log 2>&1
```

the program output and error messages are redirected to `spike.log` instead of being displayed in the terminal.

Inspect the log using:

```bash
tail -50 spike.log
```

For initial debugging, running Spike without output redirection is recommended.

## 11. Conclusion

The RISC-V development and simulation environment has been successfully established on Ubuntu.

A C program was cross-compiled into a 64-bit RISC-V ELF executable and executed using the Spike ISA simulator with the RISC-V Proxy Kernel.

This setup provides the foundation for further RISC-V development, including:

- Understanding RISC-V instruction encoding and execution.
- Developing a C++ Instruction Set Simulator (ISS).
- Implementing an RV32I processor in Verilog.
- Integrating Verilog RTL with Verilator.
- Developing a lockstep verification framework.
- Building automated instruction generation and regression testing.

The successful execution of the test application marks the completion of the initial RISC-V toolchain and simulator setup.

---

**Project:** Veri-RISC  
**Environment:** Ubuntu Linux  
**Target Architecture:** RISC-V RV64GC  
**Compiler:** riscv64-unknown-elf-gcc  
**Simulator:** Spike  
**Runtime:** RISC-V Proxy Kernel (PK)

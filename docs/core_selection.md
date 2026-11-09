# RISC-V Core Selection and Simulation Results

## 1. Objective

The objective of this stage was to evaluate open-source RISC-V processor implementations, establish a simulation environment, and select a suitable processor core for the RISC-V verification project.

Two open-source RISC-V cores were considered:

- PicoRV32
- Ibex

PicoRV32 was selected for hands-on verification, while Ibex was selected for architectural and documentation study.

## 2. Core Selection

| Parameter | PicoRV32 | Ibex |
|---|---|---|
| Architecture | 32-bit RISC-V | 32-bit RISC-V |
| RTL language | Verilog | SystemVerilog |
| Project approach | Hands-on verification | Documentation study |
| Integration complexity | Relatively simple | More extensive |
| Decision | Selected | Not selected for implementation |

### Reason for selecting PicoRV32

PicoRV32 was selected because:

- It provides a relatively simple integration environment.
- It has an existing testbench and simulation infrastructure.
- It supports Verilator-based testing.
- It is suitable for developing an independent verification environment.

Ibex will be studied to understand a more extensive, production-oriented RISC-V core and its verification methodology.

## 3. Development Environment

The following tools were installed and configured:

| Tool | Purpose |
|---|---|
| RISC-V GCC | Compiling RISC-V programs |
| Spike | RISC-V instruction set simulator |
| RISC-V Proxy Kernel (PK) | Running compiled programs on Spike |
| Verilator | RTL linting and simulation |
| Icarus Verilog | Verilog simulation |
| Ubuntu Linux | Development environment |

## 4. Repository Setup

The following repositories were cloned:

**PicoRV32:**
https://github.com/YosysHQ/picorv32

**Ibex:**
https://github.com/lowRISC/ibex

PicoRV32 was used for simulation experiments. Ibex was retained for architectural and verification documentation study.

## 5. Verilator Lint

The following command was executed from the PicoRV32 repository:

```bash
verilator --lint-only -Wall picorv32.v
```

**Result:** Lint command executed successfully.

This step was used to check the RTL for potential warnings and issues using Verilator.

## 6. PicoRV32 Simulation

### 6.1 Simple testbench

Command:

```bash
make test_ez
```

The simple PicoRV32 testbench was executed successfully.

Observed simulation activity included:

- Instruction fetch operations.
- Memory read operations.
- Memory write operations.
- Processor execution.

The simulation terminated normally through the testbench `$finish` statement.

### 6.2 Standard testbench

Command:

```bash
make test
```

**Result: ALL TESTS PASSED.**

The standard PicoRV32 test suite completed successfully.

### 6.3 Verilator-based testbench

Command:

```bash
make test_verilator TOOLCHAIN_PREFIX=riscv64-unknown-elf-
```

**Result: ALL TESTS PASSED.**

The Verilator-based testbench executed successfully.

This established a working RTL simulation flow using Verilator and its C++ testbench infrastructure.

## 7. Results Summary

| Experiment | Result |
|---|---|
| RISC-V GCC installation | Successful |
| Spike installation and execution | Successful |
| PicoRV32 repository setup | Successful |
| Ibex repository setup | Successful |
| Verilator lint | Successful |
| PicoRV32 simple simulation | Passed |
| PicoRV32 standard simulation | All tests passed |
| PicoRV32 Verilator simulation | All tests passed |

## 8. Conclusion

The initial RISC-V development and simulation environment has been successfully established.

PicoRV32 was selected as the primary processor for hands-on verification development. Its existing simulation infrastructure provides a starting point for building an independent verification environment.

Ibex will serve as a reference for studying processor architecture and advanced verification methodologies.

## 9. Next Steps

The planned next stages are:

1. Study the PicoRV32 RTL architecture.
2. Understand the RV32I instruction execution flow.
3. Develop an independent C++ golden reference model.
4. Create instruction-level comparison between the reference model and RTL.
5. Develop a lockstep verification environment.
6. Introduce directed and randomized instruction testing.
7. Add assertions, functional coverage, and regression testing.

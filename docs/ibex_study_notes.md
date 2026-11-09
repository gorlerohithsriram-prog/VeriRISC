# Ibex Architecture and Verification Study

## 1. Introduction
- What is Ibex?
```text
Ibex is a production-quality open source 32-bit RISC-V CPU core written in SystemVerilog.
The CPU core is heavily parametrizable and well suited for embedded control applications.
Ibex is being extensively verified and has seen multiple tape-outs.
```
- Processor architecture:
- Supported RISC-V extensions:
```text
RISC-V Bit-Manipulation Extension, version 1.0.0 (Zba, Zbb, Zbc, Zbs sub-extensions)

RISC-V Scalar Cryptography Extension, version 1.0.1 (Zbkb, Zbkx sub-extensions only)
```

## 2. Architecture
- Pipeline:
```text
Ibex has a 2-stage pipeline, the 2 stages are:

Instruction Fetch (IF)
Fetches instructions from memory via a prefetch buffer,
capable of fetching 1 instruction per cycle if the instruction side memory system allows. See Instruction Fetch for details.

Instruction Decode and Execute (ID/EX)
Decodes fetched instruction and immediately executes it,
Register read and write all occurs in this stage.
Multi-cycle instructions will stall this stage until they are complete See Instruction Decode and Execute for details.
```
- Functional units:
- Instruction execution:
## 3. Repository and Build
- RTL location: [GitHub](https://github.com/lowRISC/ibex/tree/master/rtl)
- Important directories:
- Build system:
- Simulation infrastructure:

## 4. Verification Methodology
- Verification framework:
- Reference model:
- Random instruction testing:
- Assertions:
- Functional coverage:
- Regression testing:

## 5. Comparison with PicoRV32

| Parameter | PicoRV32 | Ibex |
|---|---|---|
| RTL language | Verilog | SystemVerilog |
| Integration complexity | Relatively simple | More extensive |
| Verification infrastructure | | |
| Selected usage in this project | Hands-on | Documentation study |

## 6. Final Decision

PicoRV32 was selected for hands-on verification because:
-Relatively simple, Has existing testbench and simulation infrastructure.
Ibex was studied to understand a more extensive RISC-V processor architecture and verification methodology.

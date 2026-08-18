# AXI-4Lite BRAM Interface and RTL-Based FPGA Data Processor

## Project Overview
This repository contains a Self-Project focused on interfacing a Block RAM (BRAM) with a Xilinx AXI BRAM Controller IP to create an AXI-BRAM setup. 
Custom RTL-based AXI-4 Lite Masters were developed to read, write, and process data on the FPGA.

The project is divided into three major milestones:
1. **Basic AXI-4 Lite Testbench:** A master testbench that mimics an AXI-4 Lite interface to perform single-address Write and Read-back transactions on the BRAM.
2. **Sequential AXI Reader Logic:** A custom RTL module (`axi_reader_logic`) designed to act as an AXI Master and traverse/read initialized data sequentially from the BRAM.
3. **Hardware Data Processor (Signed Dadda Multiplier):** An AXI processor module (`axi_processor_q3b`) that fetches 32-bit words (containing two 16-bit operands) from the first half of the BRAM, multiplies them using a Signed Dadda Multiplier, and stores the 32-bit product into the second half of the BRAM.

## Repository Structure
* `/src` - Contains the custom RTL Verilog modules (`axi_reader_logic.v` and `axi_processor_q3b.v`).
* `/tb` - Contains the behavioral simulation testbenches (`tb_axi_bram.v`).
* `/init` - Contains the `.coe` file used for BRAM initialization.
* `/docs` - Block designs, waveforms, and the main project report.

## Module Descriptions

### 1. Basic AXI Master Testbench (`tb_axi_bram`)
Generates AXI-4 Lite signals for memory-mapped communication:
* **Write Transaction:** Writes the 32-bit hex value `AAAABBBB` to address `00000000` using standard AXI handshake protocols (`AWVALID`, `AWREADY`, `WVALID`, `WREADY`).
* **Read Transaction:** Verifies the stored data by executing a read back from the identical address (`ARVALID`, `ARREADY`, `RVALID`, `RREADY`).

### 2. AXI Reader Logic (`axi_reader_logic_v1_0`)
A custom RTL master that automatically generates read addresses (`m_axi_araddr`) to sequentially traverse and fetch data from the initialized Block Memory. 
The outputs successfully matched the `.coe` initialization vectors (e.g., `12345678`, `dafecace`, etc.).

### 3. AXI Processor with Signed Multiplier (`axi_processor_q3b`)
This core master logic handles an FSM with the following pipeline:
* **Read:** Fetches two 16-bit operands (A and B) from memory addresses `0x00` to `0x3C`.
* **Calculate:** Multiplies A and B using an integrated 16x16 Signed Dadda Multiplier. [cite_start]Verified with both positive and negative bounds (e.g., `-6 * 5 = -30`).
* **Write:** Stores the resulting product to offset memory addresses starting at `0x40`.
Integrated with ILA (Integrated Logic Analyzer) ports for FPGA bit-file demonstrations to monitor state machines and multiplier outputs.

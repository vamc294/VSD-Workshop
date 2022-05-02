# RTL design using Verilog with SKY130 Technology<!-- omit in toc -->
![](assets/Verilog_flyer.png)


Table of Contents
- [1. Introduction](#1-introduction)
- [2. Day 1 - Introduction to Verilog RTL design and Synthesis](#2-day-1---introduction-to-verilog-rtl-design-and-synthesis)
  - [2.1. Introduction to Simulation](#21-introduction-to-simulation)
    - [2.1.1. Simulation results](#211-simulation-results)
  - [2.2. Introduction to Synthesis](#22-introduction-to-synthesis)
    - [2.2.1. Yosys synthesizer flow](#221-yosys-synthesizer-flow)
      - [2.2.1.1. Read RTL design](#2211-read-rtl-design)
      - [2.2.1.2. Generic synthesis](#2212-generic-synthesis)
      - [2.2.1.3. Read Sky130 cell library](#2213-read-sky130-cell-library)
      - [2.2.1.4. Generate netlist](#2214-generate-netlist)
      - [2.2.1.5. Show](#2215-show)
- [3. Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles](#3-day-2---timing-libs-hierarchical-vs-flat-synthesis-and-efficient-flop-coding-styles)
  - [3.1. Timing libs](#31-timing-libs)
    - [3.1.1. Sky130 Process Node](#311-sky130-process-node)
    - [3.1.2. Introduction to standard cell library](#312-introduction-to-standard-cell-library)
  - [3.2. Hierarchial synthesis vs Flat synthesis](#32-hierarchial-synthesis-vs-flat-synthesis)
    - [3.2.1. Hierarchial synthesis](#321-hierarchial-synthesis)
    - [3.2.2. Selective sub-module level synthesis](#322-selective-sub-module-level-synthesis)
    - [3.2.3. Flat synthesis](#323-flat-synthesis)
  - [3.3. Various Flop coding styles and optimization](#33-various-flop-coding-styles-and-optimization)
    - [3.3.1. Optimizations](#331-optimizations)
- [4. Day 3 - Combinational and Sequential optimizations](#4-day-3---combinational-and-sequential-optimizations)
  - [4.1. Logic optimizations](#41-logic-optimizations)
    - [4.1.1. Combinational Constant propogation](#411-combinational-constant-propogation)
    - [4.1.2. Boolean logic optimization](#412-boolean-logic-optimization)
    - [4.1.3. Sequential Constant propogation](#413-sequential-constant-propogation)
  - [4.2. Logic optimizations in Yosys](#42-logic-optimizations-in-yosys)
    - [4.2.1. Optimization design example 2](#421-optimization-design-example-2)
    - [4.2.2. Optimization design example 2](#422-optimization-design-example-2)


















# 1. Introduction
This is a 5-day workshop from VSD-IAT on RTL design and synthesis using open source silicon toolchains involving iVerilog, GTKWave, Yosys with Sky130 technology.  
This report is written as a part of final submission to summarize the 5-day journey through the workshop.

# 2. Day 1 - Introduction to Verilog RTL design and Synthesis
## 2.1. Introduction to Simulation
**RTL design**:
                Register Transfer Level (RTL) is a representation of the digital circuit at the abstract level.There are two elements in digital circuits: Sequential Circuit and Combinational Circuit , with the help of these two elements, a digital designer can implement any circuit.There are two commonly used variants of the RTL — namely: Verilog and VHDL, which a digital design engineer can represent their functionality of the design in a simple text entry language.
Before the RTL invention, engineers designed a complete functionality as a circuit — schematic entry, this made the design a hefty job with high level of errors.

**Simulation**: 
                RTL design is checked for adherence to its specifications by simulating the design. Simulator is the tool used for simulating the design. This helps finding and fixing bugs in the RTL design in the early stages of design development. Here in this workshop we used iverilog to achieve this.
                
iVerilog  [Icarus Verilog](http://iverilog.icarus.com/) is an open source toolchain for simulation and synthesis. But here we use it only for simulation purposes.

**Design**:
It is the actual verilog code or set of verilog codes( here in this context) which has the intended functionality to meet the requirement specifications.

**Test bench**:
Testbench is a setup to apply stimulus (test_vectors) [test_vectors] (https://en.wikipedia.org/wiki/Test_vector) to design to check its functionality. Testbench doesn't have any primary inputs and outputs.

**How Simulator works ?**:

 Simulator works for changes in the input signal.
 Upon change to input, output is evaluated. If no change to input, then there will be no change to output.
 
![](assets/simulatorworking.png)
The simulation output of iVerilog can be taken as a value change dump ('.vcd') file that could then be visualized in GTKWave.  
[GTKWave](http://gtkwave.sourceforge.net/) is an open source tool for visualizing the signal dumps in .vcd/.lxt formats.  




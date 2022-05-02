# RTL design using Verilog with SKY130 Technology<!-- omit in toc -->
![](assets/Verilog_flyer.png)


Table of Contents
- [1. Introduction](#1-introduction)
- [2. Day 1 - Introduction to Verilog RTL design and Synthesis](#2-day-1---introduction-to-verilog-rtl-design-and-synthesis)
  - [2.1. Introduction to Simulation](#21-introduction-to-simulation)
    - [2.1.1. Simulation results](#211-simulation-results)
  - [2.2. Introduction to Synthesis](#22-introduction-to-synthesis)
    - [2.2.1. Synthesis using yosys](#221-Synthesis-using-yosys)
      - [2.2.1.1. Read RTL design](#2211-read-rtl-design)
      - [2.2.1.2. Generic synthesis](#2212-generic-synthesis)
      - [2.2.1.3. Read Sky130 cell library](#2213-read-sky130-cell-library)
      - [2.2.1.4. Generate netlist](#2214-generate-netlist)
      - [2.2.1.5. Show](#2215-show)
      - [2.2.1.6. write verilog](#2216-write-verilog)
      - [2.2.1.7. Netlist](#2217-Netlist)

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
Testbench is a setup to apply stimulus [(test_vectors)](https://en.wikipedia.org/wiki/Test_vector/) to design to check its functionality. Testbench doesn't have any primary inputs and outputs.

**How Simulator works ?**:

 Simulator works for changes in the input signal.
 Upon change to input, output is evaluated. If no change to input, then there will be no change to output.
 
![](assets/simulator_working.png)

**iVerilog based Simulation flow**:
![](assets/iverilog_based.png)

- 1. We will apply design and testbench to iverilog.

- 2. Simulator looks for changes in input and dumps output changes into VCD file.

- 3. We use gtk wave to view vcd file.

[GTKWave](http://gtkwave.sourceforge.net/) is an open source tool for visualizing the signal dumps in .vcd/.lxt formats.  

# iverilog Set-up:
- open terminal and create a root directory to work-in
- Here are the commands to Get started

  ```
   mkdir vlsi
   cd vlsi
   mkdir vsdflow
   git clone  https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
   
   ```
- Now to Check the Content Enter

   ```
      cd vlsi
      cd vsdflow
      ls 
      cd sky130RTLDesignAndSynthesisWorkshop
      ls -ltr
      cd my_lib
      ls
      cd lib   :This file contain the  Sky130 Standard Cell library
      cd ..
      cd verilog_model :Content of verilog models
      
   ```

The images are as follows:
![](assets/git_clone.png)

![](assets/image_2.png)

![](assets/image_3.png)

![](assets/image_4.png)


#  Simulation Using iverilog. <br/>
-  1. Into iverilog simulator, RTL file and Test-bench files are passed. Then "a.out" file is generated <br/>

```
iverilog good_mux.v tb_good_mux.v

```
-  2.Now to get the .vcd file <br/>

```
./a.out

```
- 3.To view the waveform for logical verification run the .vcd file with gtkwave <br/>

``` 
 gtkwave tb_good_mux.vcd
 
 ```

The images and results are as follows:
![](assets/image_5.png)

![](assets/waveform_gtkwave.png)

# Introduction to Yosys

 **Synthesis**:</br>
There is a need of tool to convert RTL to netlist.
![](assets/Yosys_1.png)

- RTL to gate level translation will be performed by snthesis.
- Design is converted into gates and connections made between gates.
- This is given out as a file called netlist.

The working may look as follows:
![](assets/image_6.png)

**what is .lib ?**

- Collection of logical modules is a .lib file.
- It includes basic gates like and,or,not etc.
- Different flavors of some gates (2 i/p, 3 i/p, slow, fast etc)will be available. </br>

**Why different flavors of gate ?** </br>
Combinational delay in logic path determine maximum speed of operation of digital logic circuit.So we need cells that work fast to make T_comb small.
![](assets/image_7.jpg)

![](assets/image_8.jpg)

**Are faster cells sufficient ?** </br>

To ensure there are no hold issues at Dff_B, we need cells that work slow.
![](assets/image_9.jpg)

So from figure, we can observe  what DFF_A launched should be captured by DFF_B in next cycle. Else,we will lose data. So, we need hold time.
Hence we need cells that work fast to meet required performance and we need cells to work slow to meed hold.</br>
This collection forms .lib file.

**Faster cells vs Slower cells:** </br>
Load in digital circuit can be inferrred as capacitance.
- faster the charging/discharging of capacitance, lesser the delay of cell.
- To charge/discharge capacitor fast, we need transistors that are capable of sourcing more current.</br>

wider transistors ----> low delay ----> more area and power </br>
narrow transistors ----> more delay ----> less area and power </br>

Therefore, we can understand that faster cells come at the penalty of area and power.

**Selection of cells:**</br>
- Need to guide synthesizer to select flavor of cells that is optimum for implementation of logic circuit.
- More use of faster cells cause bad circuit in terms of power and area. It may also cause hold time violatons.
- More use of slower cells may cause sluggish circuit and may not meet performance needs.</br>
The guidance offered to synthesizer is known as **constraints**.

### 2.2.1. Synthesis using yosys

#### 2.2.1.1. Read RTL design

-  To invoke Yosys:
   ```
    cd verilog_files
    $ yosys
    
   ```
![](assets/image_10.png)
</br>


#### 2.2.1.2. Generic synthesis
 ```
 $  synth -top good_mux
 
 ```

![](assets/image_13.png)
</br>
![](assets/image_14.png)
</br>
synth[options]: This command runs the default synthesis script. This command does not operate on partly selected designs[].  


#### 2.2.1.3. Read Sky130 cell library
      
  ```
   $ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  
  ```
![](assets/image_11.png)
</br>  
  read_liberty[options]: This command reads cells from liberty file as modules into current design[].  
  
  **read_verilog**</br>
    </br>

  ![](assets/image_12.png)
</br>
  
  

#### 2.2.1.4. Generate netlist
  ```
  
  $  abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

   ```
     

   ![](assets/image_15.png)
</br>
   ![](assets/image_16.png)

  </br>

abc[options]: This pass uses the ABC tool [1] for technology mapping of yosys's internal gate library to a target architecture[].  
 
**Note**: When no target cell library is specified the Yosys standard cell library is loaded into abc before the abc script is executed.  


#### 2.2.1.5. Show

 ```
  $ show
  
  ```
</br>

  ![](assets/image_18.png)
  </br>
   ![](assets/image_17.png)

</br>
- To view the result as a grapviz use above command 
#### 2.2.1.6. write verilog

  ```
  $ write_verilog -noattr good_mux_netlist.v
  
  ```
   ![](assets/image_19.png)
</br>
- write_verilog      : Write the netlist to a file :

- -noattr            : By using this option no attributes are included in the output. </br>
- good_mux_netlist.v : File name to which we want to write the netlist.

#### 2.2.1.7. Netlist
    ```
     $ !gvim good_mux_netlist.v
    
    ```
![](assets/image_20.png)
</br>
- To view netlist we can use above command.<br/>


The netlist file will be as follows:
 ```
    /* Generated by Yosys 0.7 (git sha1 61f6811, gcc 6.2.0-11ubuntu1 -O2 -fdebug-prefix-map=/build/yosys-OIL3SR/yosys-0.7=. -fstack-protector-strong -fPIC -Os) */

  module good_mux(i0, i1, sel, y);
  wire _0_;
  wire _1_;
  wire _2_;
  wire _3_;
  wire _4_;
  wire _5_;
  input i0;
  input i1;
  input sel;
  output y;
  sky130_fd_sc_hd__clkinv_1 _6_ (
    .A(_0_),
    .Y(_4_)
  );
  sky130_fd_sc_hd__nand2_1 _7_ (
    .A(_1_),
    .B(_2_),
    .Y(_5_)
  );
  sky130_fd_sc_hd__o21ai_0 _8_ (
    .A1(_2_),
    .A2(_4_),
    .B1(_5_),
    .Y(_3_)
  );
  assign _0_ = i0;
  assign _1_ = i1;
  assign _2_ = sel;
  assign y = _3_;
 endmodule  
 
 ```
# 3. Day 2 - Timing libs, hierarchical vs flat synthesis and efficient flop coding styles
## 3.1. Timing libs
### 3.1.1. Sky130 Process Node
- The Standard cell library :
- The Library files We are Using Here are Sky130 Process Node:
- About Sky130 Process Node:The SKY130 is a mature 180nm-130nm hybrid technology originally developed internally by Cypress Semiconductor before being spun out into     SkyWater Technology and made accessible to general industry.  
 
    ```
    
    $  gvim Sky130_fd_scSky130_fd_sc_hd_tt_025C_1v80.lib  
 
   ```

### 3.1.2. Introduction to standard cell library
Nomenclature is as follows:
![](assets/Image_21.png)

- To search individual  Standard cells in library use the following Commands.

```

:syn off     //to turn off the syntax
:se nu     //To show numbers
:/cell     //search for "cell"
:g//       //list all

```


Here the example of standard cell library is as follows:

![](assets/image_22.png)

</br>
![](assets/image_23.png)

</br>
![](assets/image_24.png)
</br>


## 3.2. Hierarchial synthesis vs Flat synthesis
### 3.2.1. Hierarchial synthesis

In hierarchial synthesis the hierarchy of the RTL design is preserved through the final netlist. Let us look at the below RTL design where we have two sub modules, both instantiated by a top modules.  

![](assets/image_26.png)

we can see sub modules 1 and 2 here:
![](assets/image_25.png)



**Stacked nMos vs Stacked pMos:**
![](assets/image_29.png)

- pMos has poor mobility, to improve it we have to make cells larger.
- Stacked pmos is bad.
- so we get nand based gates in this synthesis.
### 3.2.2. Why to synth only sub module ?
Just like synthesizing an RTL design at the top mudule level, it can be synthesized at the sub-module level as well. This level of freedom brings the below advantsges.  
* &emsp;  Multiple instantions: In case of having multiple instantiations in our design, this feature helps to synthesize just one instance and use it iteratively throughout the top level.    
* &emsp;  Massive desgins: In case of massive design, sub-module level synthesis reduces the burden on sythesis tool in-terms of performance and time.  
 
We can take a look at the same example of multiple_modules.v.  Let us synthesize only sub_module1 and see how the final netlist appears.  
We can see that the synth command just looks at the specified sub_module1 alone and sub_module2 is ommitted from synthesis.  

- when we have multiple instance of same module.
- To divide and conquer a massive design.
 ![](assets/image_30.png)
- 
**flatten** :</br>This pass flattens the design by replacing cells by their implementation. 
</br>
Cells and/or modules with the 'keep_hierarchy' attribute set will not be flattened by this command." [source: http://yosyshq.net/yosys/cmd_flatten.html]  

As the name suggestes this pass flattens out the design and hence the hierarchy is lost.  
Let us observe the impact of 'flatten' on multiple_modules.v.  
![flatten](assets/image_31.pg)  
</br>


We can see that the submodules were deleted and hierarchy is no longer preserved. 


## 3.3. How to code a flop ?
- abc tool maps only the combinational logic cells from the liberty. It doesn't look for the register cells. 
-  If the RTL design has sequential logic, dfflibmap pass has to be executed before abc pass.
-  dfflibmap pass looks for the register cells in the Liberty and maps to the sequential logic from the synthesis. And then abc pass has to used to complete the mapping for combinatorial logic.  
** why flop?**
- More combinational circuit means more glitch
- we need element to store, i.e flop
- flop will be stable, it changes only at the edge of clock.
- As output changes only at the edge of the clock. So even if input glitching, output will be calmed down.
**why set and Reset pins?**
- If initial state of flop is unknown,it will take garbage value.
- So we use set and reset pins.
- **Asynchronous reset** wont wait for the clock </br>
- If both set and reset are present, this may leads to **race-around** condition.
- One of the example is:
 ![](assets/image_27.png)


 ![](assets/image_28.png)


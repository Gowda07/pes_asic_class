# PES_ASIC_CLASS
This Repository Guides you to complete ASIC flow from scratch (FACULTY : Mahesh Awati, GUIDE : Kunal Ghosh)

## The COURSE files are present under those respective day folders 

### Solutions to frequenty occuring errors are in Error_solution.md

## Install the Prerequisites(for ubuntu)

```
sudo apt update
sudo apt upgrade
chmod +x run_ubuntu.sh
./run_ubuntu.sh
```
The installed contents will be available at ~/riscv_toolchain

# Introduction
### Flow : HLL -> ALP -> Binary -> (HDL) -> GDS
#### 1. HLL -> High level language (c , c++) 
- A high-level programming language is a type of programming language that is designed to be more human-readable and user-friendly compared to low-level languages like assembly or machine code.

#### 2. ALP -> Assembly level program
- Assembly language is a low-level programming language that is closely related to the architecture of a specific computer's central processing unit (CPU). Assembly language programs are written using mnemonic codes that represent specific machine instructions which the machine can understand.

#### 3. HDL -> Hardware Description Language
- It is a specialized programming language used for designing and describing digital hardware circuits. HDLs allow engineers to model and simulate complex digital systems before physical implementation, aiding in the design and verification of integrated circuits and FPGA configurations.
- Verilog, System Verilog, VHDL

#### 4. GDS -> Graphic Data System (layout)
- Format used in the semiconductor industry to represent the layout and design of integrated circuits at a highly detailed level. GDSII files contain information about the geometric shapes, layers, masks, and other essential details that make up the physical layout of a chip.
- Tools : Klayout, Magic

##### The Hardware needs to understand what the Application software is saying it to do.This relation can be eshtablished by System Software

____System Software____
- OS : Operating System : Handles IO, memory allocation, Low level system function
- Compiler : Convert the input to hardware dependent instruction
- Assembler : Convert the instructions provided by compiler to Binary format
- HDL : A program that understands the Binary pattern and map it to a netlist
- GDS : Layout

# COURSE 
<details>
<summary>DAY 1: Introduction to RISCV ISA and GNU Compiler Toolchain</summary>
<br>

## Introduction to Risc-v Basic Keywords
- **Instruction Set Architecture(ISA)**
  - An Instruction Set Architecture (ISA) refers to the set of instructions that a computer's central processing unit (CPU) can understand and execute. It defines the interface between software and hardware, specifying the operations that a CPU can perform, the data types it can manipulate, and the memory addressing modes it supports.

- **Risc-V ISA**
  - Risc-V ISA is an open-source ISA that has simpler and fixed length instructions that allows us to create custom processors for specific needs without being tied to proprietary architectures
 
- **Tools Used for the flow**
  - As we are aware of the flow, we will be using Risc-v ISA ALP and the RTL used will be picorv32a (We will be using rv64i during initial stages)

# Goal : Any High level Program that is written should be able to get executed in our CHIP

### List of well-known extensions present in Risc-V ISA

``` rv32i``` ``` rv64i``` ```rv32imc``` ```rv64imc``` ```rv32imafdc``` ```rv64imafdc``` ```rv32imcb``` ```rv64imcb``` ```rv32imc_sv32``` ```rv64gcv```

### Extensions and their Applications

- **I (Integer)** :The I set includes the base integer instruction set for RISC-V. It provides fundamental integer arithmetic and logical operations, data movement, and control flow instructions.
  - ADD, SUB, AND, OR, XOR, ADDI, SLTI, JAL, BEQ, LW

- **M (Multiply and Divide)** : The M set adds integer multiplication and division instructions to the base integer set. These instructions are particularly useful for arithmetic-heavy computations.
  - MUL, MULH, DIV, REM
  
- **A (Atomic)** : The A set introduces atomic memory access instructions. These instructions enable multiple operations on memory locations to be performed atomically, ensuring that other processors or threads cannot observe intermediate states.
  - LR (Load-Reserved), SC (Store-Conditional), AMO (Atomic Memory Operation)
  
- **F (Single-Precision Floating-Point)**: The F set adds single-precision floating-point instructions. These instructions enable arithmetic operations on 32-bit floating-point numbers.
  - FADD.S, FSUB.S, FMUL.S, FDIV.S, FCVT.W.S, FCVT.S.W

- **D (Double-Precision Floating-Point)** : The D set includes double-precision floating-point instructions. These instructions allow arithmetic operations on 64-bit floating-point numbers.
  - FADD.D, FSUB.D, FMUL.D, FDIV.D, FCVT.W.D, FCVT.D.W

- **C (Compressed)** : The C set introduces a compressed instruction format that reduces the size of code. Compressed instructions maintain the same functionality as their non-compressed counterparts but use shorter encodings.
  - C.ADDI4SPN, C.LWSP, C.ADDI, C.SW, C.JALR, C.BEQZ

- **G (Atomic and Lock-Free Operations)** : The G set, also known as the "GAS Set," is an alternative to the A set. It focuses on providing atomic and lock-free instructions to simplify hardware implementation.
  - LRV (Load-Reserved Variant), SCV (Store-Conditional Variant), AMO (Atomic Memory Operation Variants)

- **V (Vector)** :The V set adds vector instructions to the ISA, enabling Single Instruction, Multiple Data (SIMD) operations. These instructions allow efficient parallel processing of data elements in vectors.
  - VADD, VMUL, VFMADD, VLW, VSW

- **S (Supervisor)** : The S set, often used in privileged modes, includes instructions for managing and interacting with the supervisor-level operations of the system, such as handling exceptions and interrupts.
  - ECALL, EBREAK, SRET, MRET, WFI

- **B (Bit Manipulation)** : The B set introduces instructions for bit manipulation operations, allowing efficient manipulation of individual bits in registers and memory.
  - ANDI, ORI, XORI, SLLI, SRLI, SRAI

## 1. Create a simple C program That calculates sum from 1 to N -> sum1ton.c

_____Compile it using C compiler_____
```
gcc sum1ton.c -o sum1ton.o
./sum1ton.o
```
-o allows you to name your output file

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/caa1c793-da7d-4c53-b727-0827cd90ca1a)

```
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c
spike pk sum1ton.o
```

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/b0183ef8-8596-4fb2-b918-d0dcb68b3fa0)

- ```-O<number>``` : level of optimisation required
- ```-mabi``` : specifies the ABI (Application Binary Interface) to be used during code generation according to the requirements
- ```-march``` : specifies target architecture

_______We can check the different options available for all these fields using the commands_______ 
go to the directory where riscv64-unkonwn-elf is present
- -O1 : ``` riscv64-unkonwn-elf --help=optimizer```
- -mabi : ```riscv64-unknown-elf-gcc --target-help```
- -march : ```riscv64-unknown-elf-gcc --target-help```

_____To view the disassembled ALP code_____
```
riscv64-unknown-elf-objdump -d sum1ton.o
```

_____To debug the ALP generated by the compiler_____
```
spike -d pk sum1ton.o
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/e17674d8-f76c-4cc5-a322-1109737a783c)


- press ENTER : shows the first line and successive ENTER shows successive lines
- reg 0 a2 : checks content of register a2 0th core
- q : quit the debug process

##### Difference between the ALP commands when used different optimizers
- use the command ```riscv64-unknown-elf-objdump -d sum1ton.o | less```
- use ``` /instance``` to search for an instance 
- press ENTER
- press ```n``` to search next occurance
- press ```N``` to search for previous occurance. 
- use ```esc :q``` to quit

_____Contents of main when used -O1 optimizer_____
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/bb2384f2-6ae6-4a0b-9db8-bd915637e7a7)


_____contents of main when used -Ofast optimizer_____
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/27db3d00-693f-471a-a07a-058df696ca51)


## Integer number Representation (n-bit)
- Range of Unsigned numbers : [0, (2^n)-1 ]
* Range of signed numbes : Positive : [0 , 2^(n-1)-1]
                         Negative : [-1 to 2^(n-1)]

## 2. create a C program that shows the maximum and minimum values of 64bit unsigend and signed numbers

```
signedhighesh.c
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/3ccd6525-edb7-4ec9-b508-4792bb5279c5)


[Back to COURSE](https://github.com/Gowda07/Ppes_asic_class/tree/main#course)

</details>
<details>
<summary>DAY 2 : Introduction to ABI and Basic Verification Flow </summary>
<br>

## BASICS :

Instructions that act on signed or unsigned integers are called Base Integer Instructions
There are 47 Base Integer Instructions present in RISC-V ISA

### Types of Instruction based on encoding format

1. **R-Type (Register-Type):**
   - These instructions operate on registers and have a fixed format for their operands.
   - Examples: ADD, SUB, AND, OR, XOR, SLL, SRL, SRA, SLT, SLTU

2. **I-Type (Immediate-Type):**
   - These instructions have an immediate operand and one register operand.
   - Examples: ADDI, SLTI, SLTIU, XORI, ORI, ANDI, SLLI, SRLI, SRAI, LB, LH, LW, LBU, LHU, JALR

3. **S-Type (Store-Type):**
   - These instructions are used for storing values from registers to memory.
   - Examples: SB, SH, SW

4. **B-Type (Branch-Type):**
   - These instructions perform conditional branching based on comparisons.
   - Examples: BEQ, BNE, BLT, BGE, BLTU, BGEU

5. **U-Type (Upper Immediate-Type):**
   - These instructions have a larger immediate field for encoding larger constants.
   - Examples: LUI, AUIPC

6. **J-Type (Jump-Type):**
   - These instructions are used for unconditional jumps and function calls.
   - Examples: JAL

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/e944fb70-6e6f-48b6-a605-5337f4eedd6b)



**[number]** represents number of bits occupied by that field

1. **Opcode [7] :** The opcode is a field within a machine language instruction that indicates the operation to be performed by the instruction. It defines the type of operation, such as arithmetic, logic, memory access, or control flow. Opcodes are used by the CPU to determine how to execute the instruction.

2. **rd (Destination Register) [5]:** The "rd" field represents the destination register in an assembly language instruction. It indicates the register where the result of the operation will be stored. After executing the instruction, the computed value will be placed in this register.

3. **rs1 (Source Register 1) [5]:** The "rs1" field represents the first source register in an assembly language instruction. It indicates the register that holds the value used in the operation. For instructions that involve two operands, "rs1" typically corresponds to the first operand.

4. **rs2 (Source Register 2) [5]:** The "rs2" field represents the second source register in an assembly language instruction. It indicates the register that holds the value used in the operation. For instructions that involve three operands, "rs2" typically corresponds to the second operand.

5. **func7 and func3 (Function Fields)[7] [3]:** These fields further refine the operation specified by the opcode. The "func7" field is used to distinguish different variations of instructions within the same opcode category. The "func3" field is used to specify a more specific operation within the opcode category. Together, these fields allow for a finer level of instruction differentiation.

6. **imm (Immediate Value):** The "imm" field represents an immediate value that is part of the instruction. Immediate values are constants that are embedded within the instruction itself. They can be used for various purposes, such as specifying offsets, constants, or small data values directly within the instruction.


#### ABI : Application Binary Interface

The instructions generated by compiler using a target ISA can be accessed by OS and User directly
- The parts of ISA accessible to User : User ISA
- The parts of ISA accessible to OS : system ISA
The access is done using Sysytem calls with the help of ABI

==> If we want to access hardware resources of processor, it has to be done via registers using ABI(names)

### ABI Names : 
- ABI names for registers serve as a standardized way to designate the purpose and usage of specific registers within a software ecosystem. These names play a critical role in maintaining compatibility, optimizing code generation, and facilitating communication between different software components.
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/269f0024-903b-4b4f-bf3f-312885e881c3)


#### Data can be stored in register by 2 methods
1. Directly store in registers
2. Store into registers from memory

To store 64 bits of data from mem to reg, we use 8*8bit stores ie., m[0],m[1]......m[7]. 

- ___RISC-V uses Little Endian format to store the data ie., Least significant Byte is stored in m[0]___

## Simulate a C program using ABI function call (using registers) and execute 

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/271f9e18-bdb4-4932-ac18-81bb9699e855)


### Further we will see how to run a C program on on RISC-V CPU

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/29622f76-7180-4b89-83fb-5ad8045ea253)


[Back to COURSE](https://github.com/Gowda07/pes_asic_class/tree/main#course)
</details>

<details>
<summary>DAY 3 </summary>
<br>
</details>
# Introduction to open-source simulator iverilog
**Simulator:** RTL design is checked for adherence to the spec by simulating the design. Simulator is the tool used for simulating the design

**Design:** Design is the actual verilog codeor set of verilog codes which has the intended functionality to meet with the required specification

**Testbench:** Testbench is the setup to apply stimulus to the design to check its functionality
### How do simulator works?
- It looks for the changes in input signal
- upon change to the input the out put is evaluated

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/084055be-5faa-4ab8-86bb-b5af28cf6827)


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/6eafc454-a796-436a-9a36-23b5dad97a84)


# Labs using iverilog and gtkwave
## Introduction to lab
- Make new directory `mkdie VSD`
- `git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git`
- This should create a folder `sky130RTLDesignAndSynthesisWorkshop` in `VDS` directory
- You could see two folders under `sky130RTLDesignAndSynthesisWorkshop`
   1. my_lib: It contains all the standard cell libraries and verilog module
   2. verilog_files: It contains all the source code and testbench required for the lab

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/d95b299a-8ae8-4cc4-be6b-6542da15e850)


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/1f1b61a6-f895-4ee0-953e-0cc86d1db34b)


## Introduction iverilog gtkwave
- Go to verilog_files directory
- Load Design and Testbench using the command `iverilog good_mux.v tb_good_mux.v`
### good_mux.v
``` v
module good_mux (input i0 , input i1 , input sel , output reg y);
always @ (*)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
### tb_good_mux.v
``` v
timescale 1ns / 1ps
module tb_good_mux;
	// Inputs
	reg i0,i1,sel;
	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	good_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_good_mux.vcd");
	$dumpvars(0,tb_good_mux);
	// Initialize Inputs
	sel = 0;
	i0 = 0;
	i1 = 0;
	#300 $finish;
	end

always #75 sel = ~sel;
always #10 i0 = ~i0;
always #55 i1 = ~i1;
endmodule
```

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/7239cf3e-d20f-4db8-9b23-736b2549e92b)


- Upon loading sucessfully `a.out` will be generated
- Execute the generated file it would dump `gtkwave tb_good_mux.vcd` file

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/213a1a3b-41da-4ffd-995b-56da3d3737a4)

- Load the vcd file to simulator using the command `gtkwave tb_good_mux.vcd`
  
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/b752119d-e137-4568-b3c7-159150ed5eaf)

# Introduction to Yosys and Logic synthesis

## Introduction to yosys
Yosys is an synthesizer which is used to convert RTL to netlist

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/c9631baf-1521-4cda-8a46-4bb76cde1d5c)


**`netlist` is the representation of `DESIGN` in the form of standard cells present in `.lib`**

- To verify synthesis Netlist need to be fed to iverilog along with testbench
- vcd file generated from iverilog need to be fed to gtkwave simulator
- The output we get should be same as the output we got during RTL simulator

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/0633ac8c-0caf-4761-a180-6af595952dab)


## Introduction to logic synthesis
Logic synthesis is a vital step in digital circuit design where high-level descriptions of circuits are transformed into specific implementations using logic gates. It optimizes circuits for factors like performance, area, power, and cost. The process includes library mapping, optimization, technology mapping, timing analysis, and verification. It's an iterative process often done with specialized software tools, enabling efficient hardware design.

**.lib:** 
- Logic synthesis tools use a library of standard cells. These cells are predefined logic gates with different functionalities and characteristics
- It will also contain fast and slow version of same gate
### why fast and slow version of same gate?
Fast and slow versions of gates are essential in digital circuit design to balance between clock frequency and timing constraints. Fast gates have shorter propagation delays and are used to reduce setup and hold time violations, allowing for higher clock frequencies. Slow gates, with longer delays, can be used to intentionally slow down critical paths or address timing issues. The Tclk formula helps calculate the maximum clock frequency while considering these factors.

**Tclk formula:** ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/58563974-04bd-4705-af1a-26b5df977ef9)



- **t_setup:** The setup time is the minimum time before the clock edge when the input data must be stable.
- **t_hold:** The hold time is the minimum time after the clock edge during which the input data must remain stable.
- **t_propagation:** This term represents the propagation delay of the logic gates in the critical path.
- **Tcq:** This term represents the clock-to-q delay of the flip-flops or registers used in the design. It's often a fixed value based on the chosen flip-flop technology.

# Lab using Yosys and Sky130 PDKs
Steps to realize good_mux(design) in terms of standard cells avilable in library sky130_fd_sc_hd__tt_025C_1v80.lib
+ Go to verilog_files directory
+ once you get to verilog_files directory, Invoke yosys by using the command `yosys`
  ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/f538a51f-422d-45a9-af48-78c32a04b158)


  1. Read library: `read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib`
     ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/c66694f9-e738-493d-a0fe-f9a849c973ea)


  3. Read design: `read_verilog good_mux.v`
     ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/faf284dd-82d2-430f-89ba-7a4a7888ab7d)


  5. Synthesis: `synth -top good_mux`
  6. ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/14dbf62a-33f1-4bce-b04f-d221551a35a1)

  7. Generate netlist: `abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib`
     ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/e3ba558e-8a26-4053-a56f-e964e7ddf57c)

  9. Logic realized: `show`
      ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/45cd4860-4521-4717-b5db-64bba8254716)


  10. Write netlist: `write_verilog -noattr good_mux_netlist.v`, `!gvim good_mux_netlist.v`
      ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/0013c7f5-3643-4c95-9cf1-eeec717cc342)

### good_mux_netlist.v
``` v
/* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

module good_mux(i0, i1, sel, y);
  wire _0_;
  wire _1_;
  wire _2_;
  wire _3_;
  input i0;
  wire i0;
  input i1;
  wire i1;
  input sel;
  wire sel;
  output y;
  wire y;
  sky130_fd_sc_hd__mux2_1 _4_ (
    .A0(_0_),
    .A1(_1_),
    .S(_2_),
    .X(_3_)
  );
  assign _0_ = i0;
  assign _1_ = i1;
  assign _2_ = sel;
  assign y = _3_;
endmodule
```
# Introduction to timing dot libs
### sky130_fd_sc_hd__tt_025C_1v80.lib

+ **sky130:** This indicates that the library is associated with the SkyWater 130nm process technology. Process technology refers to the manufacturing process used to create integrated circuits (ICs) and determines factors like transistor size and performance characteristics.
  
+ **fd_sc_hd:** These letters likely stand for different aspects of the library. "fd" might refer to "Foundation," suggesting that this library contains fundamental building blocks for digital IC design. "sc" could stand for "Standard Cells," which are pre-designed logic gates used in IC design. "hd" could refer to "high-density" libraries, which typically contain smaller, more compact cell designs for achieving higher logic density in ICs.
  
+ **tt_025C:** This part of the name could refer to the library's operating conditions or temperature and voltage settings. "tt" might stand for "typical temperature," and "025C" could refer to 25 degrees Celsius, a common temperature for IC specifications. These conditions are important for characterizing the library's performance.
  
+ **1v80:** This likely represents the supply voltage for the library. In this case, "1v80" indicates a supply voltage of 1.8 volts, which is a common voltage level for digital ICs.

### Libraries contain

+ **Standard Cells:** This library is likely to include a variety of standard cells, which are pre-designed building blocks for digital logic. Standard cells consist of logic gates (e.g., AND, OR, XOR), flip-flops, latches, multiplexers, and other fundamental digital components. These cells are characterized for the specific process technology (in this case, SkyWater 130nm) and operating conditions (temperature and voltage).
  
+ **Timing Information:** The library will provide timing information for each standard cell. This information includes parameters like propagation delay, setup time, hold time, and other timing characteristics. Designers use this data to ensure that signals propagate correctly through the logic gates.
  
+ **Power Characteristics:** Power consumption data is crucial for estimating the energy usage of a design. The library will typically include information on dynamic power (power consumed when the circuit is switching) and static power (power consumed when the circuit is idle).
  
+ **Pin and I/O Information:** The library will specify the input and output pins for each standard cell, including pin names, directions (input or output), and electrical characteristics.
  
+ **Library Files:** These library files often come in various formats, including Liberty (.lib) files, which contain detailed timing and power information, and Verilog models, which allow designers to use these standard cells in their digital designs.
  
+ **Characterization Data:** The library may include data characterizing how the standard cells perform under different operating conditions, including variations in temperature and supply voltage. This helps designers account for variability in their designs.
  
+ **Technology Files:** These files might also include information about the specific characteristics of the SkyWater 130nm process technology, such as transistor models, interconnect information, and other process-related data.

# Hierarchical vs Flat Synthesis
### Hierarchical Synthesis
- Hierarchical synthesis involves dividing the design into logical modules or blocks and synthesizing each module separately.
- These modules can have their own hierarchies, and they communicate through well-defined interfaces
- It enhances reusability, as individual modules can be reused in other designs.
- Supports better scalability for large, complex designs.

### Steps to Hierarchical Synthesis
- Go to verilog_files directory
- once you get to verilog_files directory, Invoke yosys by using the command `yosys`
- once yosys is invoked follow the above sequence of commands
  ``` sh
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog multiple_modules.v
  synth -top multiple_modules
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show multiple_modules
  write_verilog -noattr multiple_modules_hier.v
  !gvim multiple_modules_hier.v
  ```
  ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/d237c6e6-8239-4797-a27a-383601294823)

  **multiple_modules_hier.v**
  ``` v
  /* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

  module multiple_modules(a, b, c, y);
    input a;
    wire a;
    input b;
    wire b;
    input c;
    wire c;
    wire net1;
    output y;
    wire y;
    sub_module1 u1 (
      .a(a),
      .b(b),
      .y(net1)
    );
    sub_module2 u2 (
      .a(net1),
      .b(c),
      .y(y)
    );
  endmodule
 
  module sub_module1(a, b, y);
    wire _0_;
    wire _1_;
    wire _2_;
    input a;
    wire a;
    input b;
    wire b;
    output y;
    wire y;
    sky130_fd_sc_hd__and2_0 _3_ (
      .A(_1_),
      .B(_0_),
      .X(_2_)
    );
    assign _1_ = b;
    assign _0_ = a;
    assign y = _2_;
  endmodule

  module sub_module2(a, b, y);
    wire _0_;
    wire _1_;
    wire _2_;
    input a;
    wire a;
    input b;
    wire b;
    output y;
    wire y;
    sky130_fd_sc_hd__or2_0 _3_ (
      .A(_1_),
      .B(_0_),
      .X(_2_)
    );
    assign _1_ = b;
    assign _0_ = a;
    assign y = _2_;
  endmodule
  ```
### Flat Synthesis
- In flat synthesis, the entire design is synthesized as a single, monolithic entity.
- All modules, submodules, and logic are flattened into a single level of hierarchy.
- This means that all components, regardless of their intended functionality, are combined into a single giant netlist
- Best suited for simple designs where complexity is low and maintainability isn't a significant concern.

### Steps to Flat Synthesis
- Go to verilog_files directory
- once you get to verilog_files directory, Invoke yosys by using the command `yosys`
- once yosys is invoked follow the above sequence of commands
  ``` sh
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog multiple_modules.v
  synth -top multiple_modules
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  flatten
  show
  write_verilog -noattr multiple_modules_flat.v
  !gvim multiple_modules_flat.v
  ```
  ![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/8199252b-14b9-449a-9974-d6bffa123860)



  **multiple_modules_flat.v**
  ``` v
  /* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

  module multiple_modules(a, b, c, y);
    wire _0_;
    wire _1_;
    wire _2_;
    wire _3_;
    wire _4_;
    wire _5_;
    input a;
    wire a;
    input b;
    wire b;
    input c;
    wire c;
    wire net1;
    wire \u1.a ;
    wire \u1.b ;
    wire \u1.y ;
    wire \u2.a ;
    wire \u2.b ;
    wire \u2.y ;
    output y;
    wire y;
    sky130_fd_sc_hd__and2_0 _6_ (
      .A(_1_),
      .B(_0_),
      .X(_2_)
    );
    sky130_fd_sc_hd__or2_0 _7_ (
      .A(_4_),
      .B(_3_),
      .X(_5_)
    );
    assign _4_ = \u2.b ;
    assign _3_ = \u2.a ;
    assign \u2.y  = _5_;
    assign \u2.a  = net1;
    assign \u2.b  = c;
    assign y = \u2.y ;
    assign _1_ = \u1.b ;
    assign _0_ = \u1.a ;
    assign \u1.y  = _2_;
    assign \u1.a  = a;
    assign \u1.b  = b;
    assign net1 = \u1.y ;
  endmodule
  ```
# Various Flop Coding Styles and optimization
## Flop coding styles
**Asynchronous Reset D Flip-Flop**
- When an asynchronous reset input is activated (set to '1'), regardless of the clock signal, the stored value is forced to '0'.
- Otherwise, on the positive edge of the clock signal, the stored value is updated with the data input.
### dff_asyncres_syncres.v
``` v
module dff_asyncres_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```

**Asynchronous Set D Flip-Flop**
- When an asynchronous set input is activated (set to '1'), regardless of the clock signal, the stored value is forced to '1'.
- Otherwise, on the positive edge of the clock signal, the stored value is updated with the data input.
### dff_async_set.v
``` v
module dff_async_set ( input clk ,  input async_set , input d , output reg q );
always @ (posedge clk , posedge async_set)
begin
	if(async_set)
		q <= 1'b1;
	else	
		q <= d;
end
endmodule
```

**Synchronous Reset D Flip-Flop**
- When a synchronous reset input is activated (set to '1') at the positive edge of the clock signal, the stored value is forced to '0'.
- Otherwise, on the positive edge of the clock signal, the stored value is updated with the data input.
### dff_syncres.v
``` v
module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk )
begin
	if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```

**D Flip-Flop with Asynchronous Reset and Synchronous Reset**
- This flip-flop combines both asynchronous and synchronous reset features.
- When the asynchronous reset input is activated (set to '1'), the stored value is immediately forced to '0'.
- When the synchronous reset input is activated (set to '1') at the positive edge of the clock signal, the stored value is forced to '0'.
- Otherwise, on the positive edge of the clock signal, the stored value is updated with the data input.
### dff_asyncres_syncres.v
``` v
module dff_asyncres_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```
## Flop synthesis simulations 
**Asynchronous Reset D Flip-Flop**

**Simulation**

Go to verilog_files directory where the design and test_bench are present

Run the following commands to simulate dff_asyncres.v
```
iverilog dff_asyncres.v tb_dff_asyncres.v
./a.ot
gtkwave tb_dff_asyncres.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/b747ed55-8e27-45de-a1c3-a2ba5a55e9a4)


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/0a94eee0-f77d-441a-987d-6ed0b8fc6610)

**Synthsis**

Go to verilog_files directory and invoke yosys

Once you invoke yosys, Run following commands to Synthsis dff_asyncres
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_asyncres.v
  synth -top dff_asyncres
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/6796b58d-4f61-4b54-8f35-ed81d00599db)


**Asynchronous set D Flip-Flop**

**Simulation**

Go to verilog_files directory where the design and test_bench are present

Run the following commands to simulate dff_async_set.v
```
iverilog dff_async_set.v tb_dff_async_set.v
./a.ot
gtkwave tb_dff_async_set.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/d6d98a90-42f5-456d-ace4-d282c333085c)

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/196d43f5-c856-4d0b-bfa0-04d9945a4ec6)


**Synthsis**

Go to verilog_files directory and invoke yosys

Once you invoke yosys, Run following commands to Synthsis dff_async_set
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_async_set.v
  synth -top dff_async_set
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/2d05d05a-b8b7-4848-86fc-6a2df2ffe17c)

**Asynchronous Reset D Flip-Flop**

**Simulation**

Go to verilog_files directory where the design and test_bench are present

Run the following commands to simulate dff_syncres.v
```
iverilog dff_asyncres.v tb_dff_syncres.v
./a.ot
gtkwave tb_dff_syncres.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/bb46ccb7-a4e6-40b2-acc1-8c3d2eb59b94)

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/8bfb9096-9ffd-4f33-b060-3ed6e85aed40)

**Synthsis**

Go to verilog_files directory and invoke yosys

Once you invoke yosys, Run following commands to Synthsis dff_syncres
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_syncres.v
  synth -top dff_syncres
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/0f565c6b-0fcc-4d2f-8a27-09642be1f989)


 ## Interesting optimisations 
 **mult_2.v**
``` v
module mul2 (input [2:0] a, output [3:0] y);
	assign y = a * 2;
endmodule
```
**Synthsis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog mult_2.v
synth -top mul2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr mul2_netlist.v
!gvim mul2_netlist.v
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/8feb3fc1-f4c3-4406-8c68-60f9f921000e)


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/05db2152-dad2-4bf7-9c7f-89d6f5cba9f5)


**mul2_netlist.v**
``` v
/* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

module mul2(a, y);
  input [2:0] a;
  wire [2:0] a;
  output [3:0] y;
  wire [3:0] y;
  assign y = { a, 1'h0 };
endmodule
```

 **mult_8.v**
``` v
module mult8 (input [2:0] a , output [5:0] y);
	assign y = a * 9;
endmodule
```
**Synthsis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog mult_2.v
synth -top mult8
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr mult8_netlist.v
!gvim mult8_netlist.v
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/18ae7ea5-b8ce-40d6-ab4a-5c78247d6011)


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/8500cde0-a2ce-4eb1-be62-78f3be1ae230)


**mult8_netlist.v**
``` v
/* Generated by Yosys 0.32+51 (git sha1 6405bbab1, gcc 12.3.0-1ubuntu1~22.04 -fPIC -Os) */

module mult8(a, y);
  input [2:0] a;
  wire [2:0] a;
  output [5:0] y;
  wire [5:0] y;
  assign y = { a, a };
endmodule
```
# Introduction to optimizations
1. **CCombinational optimization**
    - Combinational optimization is a branch of mathematical optimization that focuses on selecting the best combination of discrete options to optimize a given function. 
    - Two methods within computational optimization are:
       + **Constant Propagation:** This technique identifies and replaces variables or expressions with their constant values, reducing redundancy in code and improving performance.
       + **Boolean Optimization:** This method simplifies boolean expressions or logic circuits by reducing the number of logical gates or terms while preserving the same logical behavior, which is useful in digital circuit design and logical reasoning.

2. **Sequential logic optimization**
    - Sequential logic optimization is the process of enhancing the performance and efficiency of digital circuits containing flip-flops and state elements.
    - Methods under this optimization umbrella include:
       + **Sequential Constant Propagation:** Identifies and propagates constant values through flip-flops to reduce redundant state transitions.
       + **State Optimization:** Reduces the number of states in finite state machines (FSMs) by merging equivalent states, simplifying the circuit.
       + **Sequential Logic Cloning:** Replicates portions of sequential logic to alleviate bottlenecks and improve circuit throughput.
       + **Retiming:** Adjusts the placement of flip-flops within a circuit to optimize timing, balance critical paths, and enhance overall performance.

# Combinational logic optimizations
**opt_check.v**
``` v
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```
**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/ae42dfdd-c6cc-46ce-b971-c3cf7dce5a6a)

**opt_check2.v**
``` v
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/68eca558-aaa8-4a45-83e0-59819d4328a7)

``` v
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog opt_check3.v
synth -top opt_check3
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/1a0e66cf-4674-4ce4-baa6-fa9eb48db0f7)


**multiple_module_opt.v**
``` v
module sub_module1(input a , input b , output y);
 assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
 assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 


endmodule
```
**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
read_verilog multiple_module_opt.v
synth -top multiple_module_opt
flatten
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/d7197163-fe5b-4481-afdf-84ae3ff4525e)


# Sequential logic optimizations
**dff_const1.v**
``` v
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end

endmodule
```
**Simulate**
```
iverilog dff_const1.v tb_dff_const1.v
./a.out
gtkwave tb_dff_const1.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/6d4d090e-9ced-4af1-9d0a-8f6e2104d4b3)

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/5b0a5508-dcc4-4176-9c7b-a63d9cca7a32)

**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_const1.v
  synth -top dff_const1
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/1bd4c17b-1cac-49c4-afa4-a0c6932b684e)

**dff_const2.v**
``` v
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end

endmodule
```
**Simulate**
```
iverilog dff_const1.v tb_dff_const2.v
./a.out
gtkwave tb_dff_const2.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/4d37a8da-7455-4fa5-a6ce-f6d80be2b4cc)


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/175ef0ad-370b-49be-ab46-996269e303bc)


**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_const2.v
  synth -top dff_const2
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/4e54f6d6-18b5-4c9c-b6ea-abe217eeb908)

**dff_const3.v**
``` v
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end

endmodule
```

**Simulate**
```
iverilog dff_const3.v tb_dff_const2.v
./a.out
gtkwave tb_dff_const3.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/4742f441-c2ff-4f8c-b2ab-81ec6ca798c8)

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/d905b9c0-18b1-42cc-ad66-a95c00a0e09c)


**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog dff_const3.v
  synth -top dff_const3
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/d97a22b3-9d14-405e-a3b7-6f9322b6c607)


# Sequential optimzations for unused outputs
**counter_opt.v**
``` v
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog counter_opt.v
  synth -top counter_opt
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/3cd19395-c172-4389-b1a4-951a89cd896d)

**counter_opt2.v**
``` v
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0] == 3'b100);

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
**Synthesis**
```
  read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
  read_verilog counter_opt2.v
  synth -top counter_opt
  dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
  abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
  show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/d8878c09-071f-4a89-8c06-4feb4958ed02)

# GLS Synthesis-Simulation mismatch and Blocking Non-blocking statements
## GLS Concepts And Flow Using Iverilog
### Gate level simulation
+ Gate-level simulation is a method used in electronics design to test and verify digital circuits at the level of individual logic gates and flip-flops.
+ It's crucial for checking functionality, timing, power consumption, and generating test patterns for integrated circuits.
+ It operates at a lower abstraction level than higher-level simulations and is essential for debugging and ensuring circuit correctness.

### To perform gate-level simulation using Icarus Verilog (iverilog):

1. Write RTL code.
2. Synthesize to generate gate-level netlist.
3. Create a testbench in Verilog.
4. Compile both netlist and testbench.
5. Run simulation with compiled files.Debug and iterate as needed.
6. Perform timing analysis if necessary.
7. Generate test vectors for manufacturing tests.

## Synthesis-Simulation mismatch
+ Synthesis-simulation mismatch is when there are differences between how a digital circuit behaves in simulation at the RTL level and how it behaves after gate-level synthesis.
+ This can occur due to optimization, clock domain issues, library differences, or other factors.
+ To address it, ensure consistent tool versions, check synthesis settings, debug with simulation tools, and follow best practices in RTL coding and design.
+ Resolving these mismatches is crucial for reliable hardware implementation.

## Blocking And Non-Blocking Statements
**Blocking Statements**
+ Blocking statements execute sequentially in the order they appear within a procedural block or always block.
+ When a blocking assignment or operation is encountered, the simulation halts and waits for it to complete before moving on to the next statement.
+ Blocking assignments are typically used to describe combinational logic, where the order of execution doesn't matter, and each assignment depends on the previous one.

**Example (Blocking Assignment):** `a = b + c; // b and c must be known before calculating 'a'`

**Non-Blocking Statements**
+ Non-blocking statements allow concurrent execution within a procedural block or always block, making them suitable for describing synchronous digital circuits.
+ When a non-blocking assignment or operation is encountered, the simulation does not wait for it to complete. Instead, it schedules the assignments to occur in parallel.
+ Non-blocking assignments are typically used to model sequential logic, like flip-flops and registers, where parallel execution is required.

**Example (Non-Blocking Assignment):** 
```
always @(posedge clk)
  begin
    b <= a; // Concurrently scheduled assignment
    c <= b; // Concurrently scheduled assignment
  end
```
## Caveats With Blocking Statements
**Caveats with blocking statements in hardware description languages like Verilog include:**
+ **Sequential Execution:** Blocking statements execute sequentially, which may not accurately represent concurrent hardware behavior in the design.

+ **Order Dependency:** The order of blocking statements can affect simulation results, leading to race conditions or unintended behavior.

+ **Combinational Logic Only:** Blocking statements are primarily used for modeling combinational logic, making them less suitable for sequential or synchronous logic.

+ **Limited for Testbenches:** In testbench code, excessive use of blocking statements can lead to simulation race conditions that don't reflect real-world hardware behavior.

+ **Initialization Issues:** In some cases, initializing variables with blocking assignments can lead to unexpected results due to order-dependent initialization.

To mitigate these issues, designers often use non-blocking statements for modeling sequential logic and adopt good coding practices to minimize order dependencies and improve code clarity.

# Labs on GLS and Synthesis-Simulation Mismatch
**ternary_operator_mux.v**
``` v
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
endmodule
```
**RTL Simulation**
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/5892265f-24c5-48af-96b6-82361ca2fd3b)

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/76892db9-d225-46da-811b-0fa9aeabe7f2)


**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr ternary_operator_mux_netlist.v
show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/2712aa2c-0d4d-41e4-a5f1-6a29a94cd5cf)

**GLS**
To to Gate level simulation, Invoke iverilog with verilog modules
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_netlist.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/e9f9e6da-6f0a-44f3-afd6-93d402cfae5e)

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/cebc3e29-bb5c-46a7-b6af-17c8fa8041d5)

** bad_mux.v**
``` v
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
**RTL Simulation**
```
iverilog bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/b32854d7-cb91-46f6-bf3a-e912888f0514)


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/3849ec80-089a-4da6-976d-a1bcdf4bc576)


**Synthesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_mux.v
synth -top bad_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr bad_mux_netlist.v
show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/a01f7594-e881-48ca-aaba-9e0838b5dd3a)

**GLS**
To to Gate level simulation, Invoke iverilog with verilog modules
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_netlist.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/86c90faf-1455-4dde-9df9-998aeffadd8e)

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/9dc20104-94b9-42cf-ba1e-295ff28ec57b)


# Labs on synth-sim mismatch for blocking statement
**blocking_caveat.v**
``` v
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```
**RTL Simulation**
```
iverilog blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/c1c3bebd-6d76-4ec1-8298-c8ba9e096aa1)


![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/62de4fcc-86dd-4664-a1de-a3d701afdd1d)

**Synrhesis**
```
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog blocking_caveat.v
synth -top blocking_caveat
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr blocking_caveat_netlist.v
show
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/a61a4307-2ed9-4497-a9e8-433feeea0a18)

To to Gate level simulation, Invoke iverilog with verilog modules
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_netlist.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/801eaae9-99ae-4560-8140-03520fb73c99)

![image](https://github.com/Gowda07/pes_asic_class/assets/142581040/2ebedda3-e7e9-4c9b-a123-84a86ffcb189)


# Digital design and Computer architecture (2007)

## Chapter 6 Architecture

### Introduction

Assembly operation: 
* mnemonic 
* source operands
* destination operand  
  
Common architecture styles 
* _Reduced instruction set computer (RISC)_   
  MIPS   
Since it uses sequences of multiple simple instructions which are easily to encode & decode   
* _Complex instruction set computers (CISC)_   
  Intel's IA-32     
  More elaborate operations are more common to use this  
   
Microarchitecture:   
The specific arrangement of registers, memories, ALUs, and other building blocks to form a microprocessor  

Only single-line comments are used in assembly language   

MIPS is a byte-addressable memory     

Operands are stored as constants or in registers (hold only a small amount of data)  
Additional data must be accessed from memory, which is large but **slow**  
so here we introduce registers  
Generally, the fewer the registers, the faster they can be accessed  

**Design Principle** 
* Simplicity favors regularity 
  e.g. add a, b, c
       sub a, b, c
  only difference here is operation specification
* Make the common case faster
  Since it takes sequences of of simple instructions to perform complex operations
* Smaller is faster
  The fewer the registers, the faster they can be accessed 
  Since a small register file is typically built from a small SRAM array
  and it uses a small decoder and bitlines connected to relatively few memory cells -> shorter critical path

#### Registers  
[register set](https://slideplayer.com/slide/9431420/)
* $0                          always holds value 0  
* _assembler temporary_       $at  
* _procedure return values_   $v0 - $v1  
* _procedure arguments_       $a0 - $a3  
* _saved registers_           $s0 - $s7  
* _temporary registers_       $t0 - $t9  
* _OS temporaries_            $k0 - $k1  
* _global pointer_            $gp  
* _stack pointer_             $sp  
* _frame ppointer_            $fp  
* _procedure return address_  $ra  

#### MIPS Addressing (Base Memory address)
* Base address : Written in parenthese in the instruction is a register
* Offset : a constant which is written before the parentheses (can be written in decimal or hexadecimal)
* lw     load word
* sw     store word
* lb     load byte
* sb     store byte (LSB)

#### How acutally are memories organized 
* Big-Endian
  bytes are numbered starting with 0 at the MSB end
* Little-Endian
  bytes are numbered starting with 0 at the LSB end

* MSB is always on the left of the LSB


* Word address are the sam ein both formats and refer the same 4 bytes
* Only the addresses if bytes within a word differ

#### Constants 
* stored in instructions themselves
* In I-Type instructions
  * 16-bit 2's complement number (+-)2^15
  * no subi, since limited no. instructions and equivalent to addi negative

### Type of instructions  
[Type of instructions](https://www.researchgate.net/figure/Instruction-formats-for-MIPS-architecture-1_fig2_228942202)  
* R-type (i.e. register-type)
  * 3 register operands
  * *opcode* = 0
  * determined by *funct* field
  * *shamt* is used only in shift operations
* I-type (i.e. Immediate-type)
  * 2 register operands + 1 immediate operand
  * determined by *opcode* filed (no funct field)
  * rt as the destinition (sometimes)
  * signed extension for immediates in most cases  
  * zero extension only happens on logical operations or possibly load operations
  * addi or addiu both signed extension
   the only difference is merging an expection of overflow or not 
* J-type (i.e. Jump-type)
  * 1 address operand (*addr* 26-bit)
    * 2 lsb are zero since work aligned 
      * 4 -> 0100
      * 8 -> 1000
      * c -> 1100
    * 4 msb are obtained from the 4 msb of PC + 4 


The power of stored program
Instructions can be stored in memory. 
*Stored program* concept
the stored program offers *generak purpose* computing

The address of the current instruction is kept in a 32-bit register called the *program counter* (PC) 
(not in the previous 31 register set)

In MIPS programs, instructions are normally stored starting at address 0x00400000

--> PC = 0x00400000 initially

This *architectural state* of a microprocessor holds the state of a program, consists of the register file and PC
As a sequence, if the OS saves the architectural state at some point in the program and interrupt it, it can restore the state sych that the program continues properly, unaware that it was ever interrupted 


#### Arithemetic / Logical Instructions
* Logical instructions (bit-by-bit)
  * R-type
    * and 
        helpful for *masking* bits
        i.e. forcing unwanted bits to 0
        Any subset of register bits can be masked
    * or 
        useful for *combining* bits from 2 registers
    * xor
    * nor
        Since MIPS does not provide a NOT instruction
        --> A NOR $0 = NOT A (~A)
  * I-type (first zero-extend the immediate to 32 bits)
    * andi
    * ori
    * xori 
* Shift instructions (* / power of 2)   
  R-Type operations  
  [Shift instructions](https://slideplayer.com/slide/8236763/)  
  * 2 register operands + shamt (no. of bits)
  * 3 register operands 
  (one register variable's 5 LSB as the shamt and shamt field is 0s)  
  
  * left shift
    *_sll_ shift left operation  
    fills the LSB with 0's
    e.g. 0000100  ->  0010000
    *_sllv_ shift left logical variable
  * right shift 
    *_srl_ shift right operation
    0's shift into MSB
    *_srlv_ shift right logical variable

    *_sra_ shift right arithmetic
    sign bit shifts into the MSB
    *_srav_ shift arithmetic variable

* Generate / Store constants
  * 16-bit constants
    addi $s0 $0 0xnnnn is much easier
  * 32-bit constants 
    lui $s0, 0xnnnn         load upper immediate instruction (Set the lower half to 0)
    ori $s0, $s0, 0xmmmm    combination (Note: zero extension for immediate in logical operatoins)
    --> $s0 = 0xnnnnmmmm
    
* Multiplication / Division instructions
  * 2 special registers *hi* & *lo* 
  * Multiplication 
    * result is 64-bit 
    * *hi* stores MSB
    * *lo* stores LSB
  * Division
    * quotient is 32-bit
    * remainder is 32-bit
    * quotient is placed in *lo*
    * remainder is placed in *hi*
  * hi and lo are not among the usual 32 MIPS registers, so special instructions are needed to access them. 
    *  mfhi $s2 (move from hi) copies the value in hi to $s2.  
    *  mflo $s3 (move from lo) copies the value in lo to $s3.  
    *  hi and lo are technically part of the architectural state; however, we generally ignore these registers 

#### Branching
[^Click Me]: Braching
* Conditional branch (I-Type)   
  * beq  rs, rt, Label  # Branch rs  =  rt
  * bne  rs, rt, Label  # Branch rs !=  rt
  * bgez rs, Label      # Branch rs >= zero
  * bgtz rs, Label      # Branch rs  > zero
  * blez rs, Label      # Branch rs <= zero
  * bltz rs, Label      # Branch rs  < zero
  
  * Note:   
    *  PC = PC + 4 + offset * 4
    *  offset is no. instructions to branch over
    *  Since it starts from the next instruction of this branch instruction  
      --> first add 4, and does not count this instruction in offset
* Unconditional branch
  * jump (j)
  * jump and link (jal)
    similar to j but is used by procedures to save a return address
  * jump register (jr)  
    jumps to the address held in a register  
    e.g. addi $s0, $0, 0x2010  
         jr $s0  #jump to the address 0x00002010  
        
#### Conditional Statements
* Conditional Branching above [^Click Me]
* R-Type
  * slt   rd, rs, rt    #  rd = 1 when rs < rt, otherwise rd = 0
  * sltu  rd, rs, rt
* I-Type
  * slti  rt, rs, imm   #  rt = 1 when rs < imm, otherwise rt = 0
  * sltiu rt, rs, imm 

#### Array
High level language arraies in hardware are stored in main memory.  
* Access & Modify element(s)  
  load (generally lw - 4 bytes) (base address*)  
  but accessing large amount of elements may cause wasteful duplicated code  
  --> using for loop to iterate  

  Note: 
  1. Since we generally use lw which is 4 bytes   
  need a temporary register to hold the byte offset (i.e. i*4)
  2. Since lw, sw are I-Types, need immediates which cannot change or be replaced by a register --> change the base address for each iteration (which use a temporary registers to hold)  

#### Bytes and Characters

Because there are much fewer than 256 characters on an English keyboard,  English characters are often represented by bytes  

Java use different character encodings, most nitably _Unicode_.
which uses 16 bits to represent each character.  

Lower-case and upper-case letters differ by 0x20 (32)  

History  
  1. Morse Code (dots and dashes)
  2. Baudot Code (5 bits, not sufficiently for all characters)
  3. ASCII Code

lb, lbu, sb are depending on the style of this architecture  
* Little endian
* Big endian 
  
We also need a way to determine a string --> String have a variable length which is a built-in functions for most programming languages

### Functions
* Arguments
  * 4 registers to store
    * a0  $4  
    * a1  $5  
    * a2  $6  
    * a3  $7  
  * Additional input are placed on the stack  
    * above $sp
* Local Variables
  * stored in _saved registers_ 
  * Addition variables are stored in stack frame 
* Return value
  * 2 registers to store
    * v0  $2
    * v1  $3  
      * returning double-precision float 
      * generating 2 32-bit results
* Return Address  
  * $ra     
    * Store it when caller jumps to the callee (jal)
    * Store the next instruction of jal
* Stack pointer & Frame pointer  
  * $sp points to the top of the stack  
    * save and restore caller registers
  * $gp points to the 
  TODO  
* Categories
  * leaf function
    Α function that does not call others
  * nonleaf function  
   A function that does call others
   

#### Function Calls and Returns
MIPS use 
* __jal__ to call a function   
  jal Label(Addrress)
* __jr__  to return from a function  
  jr $ra

#### The Stack
* Definition:  
  The stack is memory that is used to save local variables within a function  
* _last-in-first-out_ (_LIFO) queue     
* Each function may allocate stack space to store local variables but must deallocate it before returning   
* Growing --> growing _down_ in memory  
* The stack space that a function allocates for itself is called its *stack frame*  

|                Memory                 |      $sp      |
|:-------------------------------------:|:-------------:|      
| additional arguments                  |   old $sp     | 
|  $a0 - $a3 (arguments)                |               |   
|   $ra return address                  |               |  
| $s0 - $s7 (local vars)                |               |  
| additional local vars & local arrays  |   new $sp     |  
                                           
#### Preserved Registers
* preserved registers / callee-save    
  must save and restore before jump to callee   
  * saved           $s0 - $s7  (Usually store loacal variables)
  * return address  $ra  
  * stack pointer   $sp
    Note: Rember that the stack above the stack pointer is automatically preserved ALA (as long as) the callee does not write to memory addresses above $sp
* nonpreserved registers / caller-save
  change freely in callee, no need to save and restore in callee before executation   
  * temporary     $t0 - $t9   (rare that the caller needs to save)  
  * arguments     $a0 - $a3  
  * return value  $v0 - &v1    

Note: Caller must save nonpreserved registers data before jumping into callee  

|              |                    |                                  |
|:------------:|:------------------:|:--------------------------------:|  
|              |    Caller          |                  Callee          |  
| callee-save  |   Nothing          |      save & restore -> Change    |     
| caller-save  | save and store     |              Change              |    

#### Recursived Function Calls
For _factorial_ as an exmaple
  factorial:  # First, allocate space and return registers
              addi $sp, $sp, 0x8008 (-8)
              sw   $a0, 0x0004( $sp )
              sw   $ra, 0x0000( $sp )
              
              # Use conditional statements to justfy n
              slti $t0, $a0, 0x0002
              bne  $t0, $0,  else

              # If n < 2, go to Base Case
              addi $v0, $0, 0x0001      # f(1) = 1
              addi $sp, $sp, 0x0008(+8)     # Deallcoate stack space
              ja $ra                    

  else      : # n * f(n-1) here we need to jump to f(n-1)
              addi $a0, $a0, -1
              jal  factorial

              # load and dedallocate stack space
              lw   $a0,  0x0000($sp)
              lw   $ra,  0x0004($sp)
              addi $sp,  $sp, 0x0008(+8)

              # multiple results (n * f(n-1)) & return result
              mul $v0, $a0, $v0
              jr  $ra

### Addressing Mode
* Register-Only Addressing  
  use register for all source and destination operands  
  all R-Type  
* Immediate Addressing   
  immediates along with registers  
  Some I-Type  
* Base Addressing  
  using _base addressing_   
  Memory access instructions  
* PC-Relative Addressing  
  Conditional Branching Instructions    
  Label is _branch target address_ (_BTA_)   
  imm (offset) is no. instructions between the next instruction in the memory block to the BTA
* Pseudo-Direct Addressing
  address (_jump target address_ JTA) is specified in the instruction   
  all J-Type   
  Note: jump range is limited since 4msb are taken from PC + 4  

### Compling Assembling, Loading --> Big Picture

#### Memory Map
32-bit addresses, _address space spans 2^32 bytes = 4GB  
0 - 0xFFFFFFFC  
from lowe - high  

* Text Segement
  * stores machine code
  * almost 256MB of code
* Global Data Segement
  * store global varibales
  * are defined at start-up (i.e. before the program begins executing)
  * 64KB size
  * using $gp (initialized to 0x100080000) as the base register (sign-extened)
  * access using 16-bit signed offset at assembly time
* Dynamic Data Segement
  * holds stack and heap 
  * spanning almost 2GB
  * report out-of-memory error when data corrupted(i.e. meet each other)
  * stack
    * save and restore local variables and data structures
    * goes downward from the top (0x7FFFFFFC) 
    * LIFO
  * heap
    * stores data is allocated by the program during runtime (e.g. malloc / new )
    * grows upward from the bottom
* Reserved Segment
  * used by OS
  * part of it is used for interrupts and memory-mapped I/O

#### Translating and Starting a Program
|   Mechanism    |     Resources    |
|:--------------:|:----------------:|  
|                |  High-Level Code |
|    Complier    |                  |
|                |   Assembly Code  |
|    Assembler   |                  |
|                |    Object File   |
|    Linker      |                  |
|                |    Εxecutable    |  
|    Loader      |                  |
|                |      Memory      |

1. Compliation
   * assembler directives   
     indicate where the corresponding segements begin  
     e.g. .data .text  
2. Assembling
   Two passes    
   1. assigns instruction addresses and finds all the symbols(labels and global variables)  
   Symbol Table (consists of names and addresses of symbols)  
  
   |  Symbol  |     Address  |
   |:--------:|:------------:|
   |    f     |  0x10000000  |
   |    g     |  0x10000004  |
   |    y     |  0x10000008  |
   |    main  |  0x00400000  |
   |    sum   |  0x0040002C  |

   2. produce machine language code & symbol table are stored in the object file  
3. Linking  
   combine all og the object files into one machine language file _executable_
   Use the symbol tables to adjust the addresses of global variables and of labels that are relocated   
   It relocates the data and instructions in the object file so that they are not all on top of each other  

   Executable file

  |  file header  |   Text Size   |   Data Size   |
  |:-------------:|:-------------:|:-------------:|
  |               | 0x34(52 bytes)| 0xC(12 bytes) | 

  | Text Segement |    Address    |  Instruction  |
  |:-------------:|:-------------:|:-------------:|
  |               |  0x00400000   |  0x23BDFFFC   |
  |               |  0x00400004   |  0xAFBF0000   |
  |               |  0x00400008   |  0x20040002   |
  |               |  0x0040000C   |  0xAF848000   |
  |               |  0x00400010   |  0x20050003   |
  |               |  0x00400014   |  0xAF858004   |
  |               |  0x00400018   |  0x0C10000B   |
  |               |  0x0040001C   |  0xAF828008   |
  |               |  0x00400020   |  0x8FBF0000   |
  |               |      ...      |      ...      |    

  | Data Segement |    Address    |     Data      |
  |:-------------:|:-------------:|:-------------:|
  |               |  0x10000000   |       f       |
  |               |  0x10000004   |       g       |
  |               |  0x10000008   |       y       |

4. Loading
  1. read from a storage device (usually the hard disk)
  2. OS sets $gp to 0x10008000 (middle of the global data segement)
  3. OS sets $sp to 0x7FFFFFFC (top of the dynamic data segement)
  4. jal 0x00400000 jump to the beginning of the program

Summary   
test.c (high-level code) ->   
test.s (assembly code) ->   
test.o (Object file) ->   
a.out (Executable) (into the Hard Disk / Soild State Device) ->   
Memory (Main Memory RAM (SRAM / DRAM))  

Binary Executable File on Hard Disk    
-->    
The OS calls a 'loader' program to load the executable file into RAM following rge MIPS memory map  

An 'image file' is just a direct copy of the values making up this memory map layout for a particular process  

1. On start-up, CPU reads machine code instructions from ROM / NOR Flash that boots up machine
2. BIOS (Basic I/O) checks hardware, sets up device drivers for I/O, then transfer Grub loader from disk into RAM and executes it
3. The Grub loader then loads an 'image' of an OS kernel from hard disk into RAM which then executed it
4. Running OS then allows user to 'load' executables from hard disk into RAM and run these


Key concepts booting up a machine  
* CPU can only execute machine code stored in
  * ROM
  * NOR Flash
  * main RAM memory
* Data on other types of storage cannot be executed directly -> load into RAM first
*  CPU starts executing BIOS in ROM/Flash at a specified memory address which checks the hardware and loads device drivers for basic I/O like hard disks, SSD, NAND Flash memory
*  BIOS then loads an '__image file__' of a more specific boot loader from the 'boot sector' of the hard disk into RAM 
*  This in turn can load an '__image file__' for the linux kernel from the hard disk into RAM 

## Chapter 7 Microarchitecture

### Introduction
Microarchitecture is the connection between logic and architecture  

A computer architecture is defined by its instruction set and _architecture state_.  
In MIPS, it consists of the PC & 32 registers as the basic architecture state.  
Some contain additional _nonarchitectural state_ to   
* simplify the logic 
* improve performance 

Basic subset of the MIPS instruction set  
* R-Type: add sub and or slt
* Memory instructions: lw, sw
* Branches: beq

#### Design Process
microarchitectures  
* 32- bit datapath   
  * operates on words of data
* control   
  * receives instruction from datapath and tells it how to execute it

Design a complex system
1. start with hardware containing the state elements   
  * memories 
  * architectural state (PC + 32 registers)
2. Add blocks of combinational logic between state elements to compute the new state based on the current state 

Since the instruction is   
* read from part of memory
* load and store instructions, read or write data from another part of memory

Partition the overall memory into 2 smaller memories  
* containing instructions  
* containing data  

-- heavy lines indicate 32-bit data busses  
-- medium lines indicate barrower busses 5-bit address busses  
-- narror blue lines indicate control singals
e.g. register file write enable  
-- state elements usually have a reset input to put them into a  known state at start-up, which is not shown to save clutter  

MIPS initialise the PC to 0xBFC00000 on reset  and begin executing code to start up the OS
OS then loads an application at 0x0040000  and begins executing machine code

PC input indicates the address of the next instruction 
   output points to the current instruction

instruction memory
*  signle read port A, takes the instruction adress input
*  single read data output RD, reads the instruction from the address onto RD
  
register file
* 2 read ports A1, A2
  each specifying one of the registers as source operands
* 2 read data outputs RD1, RD2
  the register value from A1 and A2 are read onto RD1 and RD2
* 1 write port 
  takes 5-bit address input A3
  32-bit write data input WD
* 1 write enable input WE3
  when WE3 == 1, register file writes the data  into the specified register on the rising edge of the clock 
* 1 clock
  
data memory   
* single read/write port  
  write enable input WE  
  * WE = 1, writes data WD into address A 
  * wE = 0, reads address A onto RD 
  
#### MIPS Microarchitectures
Three microarchitectures
* Single-cycle  
  * executes an entire instruction in one cycle
  * a simple control unit  
  * No nonarchitectural state
  * cycle time is limited by the slowest instruction
* Multicycle
  * executes in a series of shorter cycles
  * Reduces the hardware cost by reusing expensive hardware blocks
  * Adding several nonarchitectural registers to hold intermediate results
* Pipelined
  * applies pipelining to the single-cycle microarchitecture
  * exectue several instructions simultaneously
  * must add logic to handle dependencies between simultameously executing unstructions 
  * requires nonarchitectural pipeline registers

### Performance analysis  
1. Measuring the execution time of a program of interest to you  
2. Measure the total execution time of a collection of programs that are similar you plan to run (benchmarks)  
   Execution time of a program:  
   Execution Time = (# instructions) (cycles/instructions)(seconds/cycle)  

Number of instructions depend on 
* processor architecture
  complicated instructions do more work per instruction --> reducing the number of instructions (they are slower to execute in hardware)
* The cleverness of the programmer

Assume contant here  

number of cycles per instruction  CPI  
instructions per cycle            IPC  

clock period Tc, is determined by the critical path through the logic on the processor  

## Single-cycle processor
ALU (Arithmetic Logical Unit)   
[ALUControl signal reference](https://www.cise.ufl.edu/~mssz/CompOrg/Table4.2-MIPSdatapath-ALUcontrol.gif)   
* 3-bit ALUControl singal
* ALUResult 32-bit 
* Zero flag to indicate whether ALUResult == 0

lw instruction 
1. The instruction memory fetches the instruction from PC
2. read the source register operand,reads the register value onto RD1
3. sign extends the immediates
4. Use ALU to perform the addition of SrcA + SignImm, ALUResult connected to A in Data Memory
5. ReadData bus is connected to the WD3, rt in lw is connected to A3 and a control unit _RegWrite_ 
6. Use another adder to compute the next instruction address, and write it on the next rising edge of the clock

[After lw](https://image1.slideserve.com/2359649/single-cycle-mips-pc-n.jpg)  

sw instruction  
1-4 are the same   
5.
 * ReadData bus is connected to the WD3, 
 * rt in sw is connected to A2, WE3 = 0
 * register value is read onto the RD2, connected to write data port of the data memory 
 * WE in data memory is controlled by _MemWrite_
   MemWrite = 1, Write data into the data memory
6. same as above

R-Type Instruction  
TODO  

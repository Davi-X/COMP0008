
# Digital design and Computer architecture (2007)

## Chapter 6

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
* sb     store byte

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
  * signed extension for immediates  
  * zero extension only happens on logical operations
  * addi or addiu both signed extension
   the only difference is merging an expection of overflow or not 
* J-type (i.e. Jump-type)
  * 1 address operand (*addr* short for address)


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
* Conditional branch (I-Type)  
  * __equal__ (beq)
  * __not equal__ (bne)
  
  * Note:   
    *  PC = PC + 4 + offset * 4
    *  offset is no. instructions to branch over
    *  Since it starts from the next instruction of this branch instruction, so first add 4, and does not count this instruction in offset
* Unconditional branch
  * jump (j)
  * jump and link (jal)
    similar to j but is used by procedures to save a return address
  * jump register (jr)
    jumps to the address held in a register
    e.g. addi $s0, $0, 0x2010
         jr $s0  #jump to the address 0x00002010
        
#### Conditional Statements

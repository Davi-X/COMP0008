# Computer Organization and Design (3rd Ed.)

## Chapter 2
The words of a computer's language are called *instructions*
vocabulary is called an *instruction set*

The secret of computing: stored-program conecpt:  
The idea that instructions and data of many tyoes can be stored in memory as numbers, leading to the stored program computer

Each line of this language can contain at most one instruction  
Comments always terminate at the end of a line  

**Design Principle** 
* Simplicity favors regularity 
  keeping rge hardware simple
  only difference here is operation specification
* Make the common case faster
  Since it takes sequences of of simple instructions to perform complex operations
* Smaller is faster
  * A very large number of registers may increase the clock cycle time simply because it takes electronic signals longer when they must travel farther

  Not absolutes, 31 registers may not be faster than 32
  * using more than 32 is the number of bits it would takae in the instructions format
* Good Design demands good compromises

> To increase portability, Java was originally envisioned as relying on a software interpreter. The instruction set of this interpreter is called *__Java bytecodes__*, which is quite different from MIPS instruction set.

> To get performance close to the equivalent C program, Java systems today typically compile Java bytecodes into the native instruction sets like MIPS. Because the compilation is normally done much later than for C programs, such Java compilers are often called *__Just-In-Time__* (JIT) compilers

operands of arithmetic instructions must be from a limited number of special locations built directly in hardware called *registers*

Alignment restriction: A requirement that data be aligneed in memory on natural boundaries

Spilling registers: The process of putting less commonly used variables(or used later) into memory

What is the rate of increase in the number of registers in a chip over time?
1. very fast: They increase as fast as Moore's law, which predicts doubling the number of transistors on a chip every 18 months
2. very slow: Since programs are usually distributed in the language of the computer, there is inertia in instruction set architecture, and so the number of registers increases only as fast as new instruction sets become viable

MIPS-64 32 64-bit registers
difference talk about in later chapter

Base register in data transfer is also called _index register_ since this instructions was originally invented to holad an index of an array with the offset

### 2.4 Representing Instructions in the Computer

Binary digits: Also called binary bit. One of the two numbers in base 2, 0,or 1, that are the components of information

Machine language: Binary representation used for communication within a computer system

Instruction format: A form of representation of an instruction composed of fields of binary numbers

R-type
I-type
reduce the complexity: 
* first three fields are the same size, same names
* fourth of I-type is equal to the length of the last three fields of R-type

Why doesn't have a subtract immediate 
1. Less frequently
2. imm field holds a signed constant

stored-program concept
can store all code used / generated in compling to machine code
Advantages
* programs can be shipped as files of binary numbers
* computers can inherit ready-made software provided they are compatiable with an existing instruction set

#### Logical Operations

| logical operations | C operators | Java operators | MIPS instructions|
|:------------------:|:-----------:|:--------------:|:----------:|
| Shift Left         |      <<     |            <<       |         sll      |
| Shift Right        |      >>     |            >>>      |         srl      |
| AND                |      &      |            &        |    and / andi    |
| OR                 |      \|      |            \|        |    or / ori    |
| NOT                |      ~      |            ~        |   nor            |

A NOR 0 = ~(A or 0) = ~A

#### Making Desicisions
Conditional branches
* beq r1, r2, L1
* bne r1, r2, L1

Basic Block: A sequence of instructions without branches and without branch targets or branch labels

Test equality 
* slt  rd, rs, rt
* slti rt, rs, imm

case / switch 
* via a sequence of conditional tests
* encoded as a _jump address tabel_
  A table of addresses of alternative instruction sequences
  jr $ra 

Why does C provide 2 sets of oeprations for AND and OR
1. Logical operations AND and OR implement & and |
   conditional brancges implement && and ||
2. && and || correspond to logical operations 
   & and | map to conditional branches
3. Redundant and mean the same thing
   && and || are simply inherited from the predecessor, pg B

#### Supporting procedures in hardware
Procedure: A stored subroutine that performs a specific task based on given parameters

1. Place parameters in a place where the procedure can access them
2. Transfer control to the procedure
3. Aquire the storage resouraces needed for the procedure
4. Perform the task
5. Place the result in a place where the caller can access
6. Return control to the point of origin


Return address: A link to the calling site that allows a procedure to return to the proper address

Program Counter: The register containing the address of the instruction in the program being executed
_jal_ saves PC + 4 in register $ra

Stacks grow from higher addresses to lower addresses

Non-preserved by the callee
temporaries
preserved by the callee
saved registers

leaf procedures: procedures that do not call others
push any registers need to be preserved onto the stack

static var == global var
automatic var == local var

MIPS use _global pointer_ $gp to simplify access the static data 

stack also store arrays or structures

Procedure frame / Activation record: The segement of the stack containing a procedure;s saved registers and local variables
-->
_frame pointer_ ($fp) points the first world of the frame or a procedure
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

> To increase portability, Java was originally envisioned as relying on a software interpreter. The instruction set of this interpreter is called *__Java bytecodes__*, which is quite different from MIPS instruction set.

> To get performance close to the equivalent C program, Java systems today typically compile Java bytecodes into the native instruction sets like MIPS. Because the compilation is normally done much later than for C programs, such Java compilers are often called *__Just-In-Time__* (JIT) compilers

operands of arithmetic instructions must be from a limited number of special locations built directly in hardware called *registers*

Since there are only 32 registers, the more complex data structures are kept in memory
--> 
Need transfer data between memory and registers (Since instructions on registers)
--> 
In MIPS, we use *load* and *store*
* Offset: The constant in a data transfer instruction 
* Base register / address: The register added to from the address (which hold the starting address)
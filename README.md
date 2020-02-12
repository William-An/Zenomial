# Zenomial

A **simple** customized 16 bit processor with minimum instruction set for education purposes

With inspiration from the ARMv6-M Architecture Manual

## Modules

* ALU
* Memory
* Instruction register
  * hold the current instructions
* Register groups (16)
* FSM Controller
  * Control the instruction fetch-execute cycle
* Clock

## Functionality

* Stack operation
* Arithmetic and logic operations
* Branching
* Memory accessing and storing
* Instruction input methods
  * Flash input
  * DIP Button input
    * Use DIP button to encode instruction and save in instruction register
    * Old Atari style 
* **No** Interrupts or exception handler currently

## RTL Diagram

## Instruction Set

Same mnemonic as the ARMv6-M Architecture

### Opcode Descrption

|Opcode|xxxxxxxxxxxx|

* [00](#arithmetic-operation)
* [01](#logic-operation)
* [10](#register-loading-and-memory-accessing)
* [11](#branching--stack-operation)

### Arithmetic Operation

* Addition
  * Adds immediate
  * Adds register
  * Adds with carry, register
* Subtraction
  * Subs immediate
  * Subs register
  * Subs carry register
  * Reverse subs
* Multiplication
  * Mulipcation of two integer

### Logic Operation

* AND
  * AND immediate?s
  * AND register
* OR
  * OR immediate?
  * OR register
* NOT
  * neg register
* XOR
  * eor register
* BIC
  * bics immediate?
  * bics register
* Shift
  * Left shift
  * Right shift
    * Arithmetic shift
    * Logic shift
  * Rotate Right
* ~~Extension?~~
  * Sign Extension
  * Unsigned Extension

### Register Loading and Memory Accessing

* Load
  * ldr word
  * ldrb byte
* Store
  * str word
  * strb byte
* Move
  * movs immediate
  * movs register

### Branching & Stack operation

* Compare
  * CMP immediate
  * CMP Register
* Branching
  * b unconditional branching
  * b[] conditional branching
  * bl branch with link register
  * bx branch to an address in a register
  * blx branch to an address in a register and store the next line of code in link register
* Push
* Pop

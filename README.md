# Zenomial

[![time tracker](https://wakatime.com/badge/github/William-An/Zenomial.svg)](https://wakatime.com/badge/github/William-An/Zenomial)

A **simple** opensourced customized 16-bit harvard architecture processor with minimum instruction set for education purposes with inspiration from the ARMv6-M Architecture.

Implemented with Alinx A301 FPGA development board.

## Development Schedule

### Todos

* [ ] RTL Diagram (Due Feb 24)
  * [ ] Top level RTL Diagram
  * [ ] Supporting block diagram for each submodule
* [ ] System Verilog Development
  * [ ] Add modules
* [ ] Unit Testing
  * [ ] Software simulation and test for each submodule using modelsim
  * [ ] Hardware simulation and test for each submodule on FPGA
* [ ] Integration Testing
  * [ ] Software simulation and test using modelsim
  * [ ] Hardware simulation and test using FPGA
* [ ] CI/CD on GitHub?
* [ ] Rearrange README as wiki
* [ ] Programming
  * [ ] Assembler development
* [ ] Peripherals Development
  * [ ]  BUS/Interface
  * [ ]  Comm protocols
  * [ ]  Flexible design and IP core
* [ ] Flexible design?
* [ ] Von Neumann Architecture?
* [ ] More stages for pipeline
  * [ ] Refer to RISC design
  * [ ] Store and load multiple registers design

## Core Modules

* ALU
* Memory Controller / Bus
  * Instruction Mem
  * Data Mem (12 bits)
  * Memory Space
    * 16 bit address
    * First 4 bits used to identify SOC devices
* General Register groups (8)
* FSM Controller
  * Control the instruction fetch-decode-execute cycle
  * Special registers
    * Intruction register
      * hold the current instructions
    * Program Counter (PC)
      * Next instruction
    * Stack pointer
    * Link register
* Clock

## Executation Pipeline

| First Clock | Second Clock  | Third Clock|
|:-----------:|:-------------:|:----------:|
| Fetch       | Fetch         | Fetch      |
|             | Decode        | Decode     |
|             |               | Execute    |

## Peripherals

* UART Port
* AHB BUS/Interface
* APB BUS/Interface
* I2C
* SPI
* GPIO
* Timers
* BUS matrix to interact with peripherals

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
  * Choose either to save instruction in flash (programming) or run the instruction immediately
* **No** Interrupts or exception handler currently

## RTL Diagram

### TOP Level Design

![Top level architecutre](docs/images/Top.png)

### Individual Block Diagram

#### ALU

![ALU](/docs/images/ALU.png)

#### General Register

![General Register](docs/images/General_Reg.png)

#### Controller

![Controller](/docs/images/Controller.png)

## Instruction Set

TODO: Finish ISA design

Same mnemonic as the ARMv6-M Architecture

### Opcode Descrption

|15 14 | 13 12 11 10 9 8 7 6 5 4 3 2 1 0 |
|:----:|:-------------------------------:|
|Opcode|         xxxxxxxxxxxxxx          |

* [00](#arithmetic-operation) Arithmetic Operation
* [01](#logic-operation) Logic Operation
* [10](#register-loading-and-memory-accessing) Memory loading and storing
* [11](#branching--stack-operation) Branch and Stack operation

### Arithmetic Operation

|15 14 | 13 12 11 | 10 9 8 7 6 5 4 3 2 1 0 |
|:----:|:--------:|:----------------------:|
|00    |  Opcode  |     xxxxxxxxxx         |

* [000](#adds-immediate) Add immediate
* [001](#adds-register-and-special-register) Add with register and specical register
* [010](#adds-with-carry-register) Add register with carry
* [011](#subs-immediate) Subtract immediate
* [100](#subs-register-and-special-register) Subtract register and special register
* [101](#subs-carry-register) Subtract register with carray
* [110](#reverse-subs) Reverse subtract by immediate
* [111](#mulipcation-of-two-integer) Multiply two registers

#### Adds immediate

|15 14 13 12 11| 10 | 9 8 7 6 5 4 3 2 1 0 |
|:------------:|:--:|---------------------|
|   0 0 0 0 0  |Encoding Sel|xxxxxxxxxx|

Encoding T1  
`ADDS rd, rs, #imm4`
|15|14|13|12|11|10| 9 8 7 6 | 5 4 3 | 2 1 0 |
|- |- |- |- |- |- |:-------:|:-----:|:-----:|
|0 |0 |0 |0 |0 |0 |  imm4   |  rs   |   rd  |

Encoding T2  
`ADDS rd, #imm7`
|15|14|13|12|11|10 | 9 8 7 6 5 4 3 | 2 1 0 |
|- |- |- |- |- |:-:|:-------------:|:-----:|
|0 |0 |0 |0 |0 |1  |  imm7         |  rd   |

#### Adds register and special register

|15 14 13 12 11 | 10 9 | 8 7 6 5 4 3 2 1 0 |
|:-------------:|:----:|:-----------------:|
|  0 0 0 0 1    |Encoding Selection|xxxxxxxxxx|

Encoding T1  
`ADDS rd, rs, rn`
|15|14|13|12|11|10|9 | 8 7 6 | 5 4 3 |2 1 0 |
|- |- |- |- |- |- |- |:-----:|:-----:|:----:|
|0 |0 |0 |0 |1 |0 |0 |  rn   |  rs   |  rd  |

Encoding T2  
`ADD sp, #imm9`
|15|14|13|12|11|10|9 | 8 7 6 5 4 3 2 1 0 |
|- |- |- |- |- |- |- |:-----------------:|
|0 |0 |0 |0 |1 |0 |1 |        imm9       |

Encoding T3  
`ADD sp, rd`
|15|14|13|12|11|10|9 | 8 7 6 5 4 3 |2 1 0 |
|- |- |- |- |- |- |- |:-----------:|:----:|
|0 |0 |0 |0 |1 |1 |0 |ignored      |  rd  |

#### Adds with carry, register

|15 14 13 12 11 | 10 9 | 8 7 6 5 4 3 2 1 0 |
|:-------------:|:----:|:-----------------:|
|  0 0 0 1 0   |Reserved|xxxxxxxxxx|

`ADCS rd, rs [, rn]`
|15|14|13|12|11|10|9 | 8 7 6 | 5 4 3 |2 1 0 |
|- |- |- |- |- |- |- |:-----:|:-----:|:----:|
|0 |0 |0 |1 |0 |0 |0 |  rn   |  rs   |  rd  |

if `rn` gets ignored, `rd` will be `rn`

#### Subs immediate

|15 14 13 12 11| 10 | 9 8 7 6 5 4 3 2 1 0 |
|:------------:|:--:|---------------------|
|0 0 0 1 1 |Encoding Sel|xxxxxxxxxx|

Encoding T1  
`SUBS rd, rs, #imm4`
|15|14|13|12|11|10| 9 8 7 6 | 5 4 3 | 2 1 0 |
|- |- |- |- |- |- |:-------:|:-----:|:-----:|
|0 |0 |0 |1 |1 |0 |  imm4   |  rs   |   rd  |

Encoding T2  
`SUBS rd, #imm7`
|15|14|13|12|11|10 | 9 8 7 6 5 4 3 | 2 1 0 |
|- |- |- |- |- |:-:|:-------------:|:-----:|
|0 |0 |0 |1 |1 |1  |  imm7         |  rd   |

#### Subs register and special register

|15 14 13 12 11 | 10 9 | 8 7 6 5 4 3 2 1 0 |
|:-------------:|:----:|:-----------------:|
|  0 0 1 0 0    |Encoding Selection|xxxxxxxxxx|

Encoding T1  
`SUBS rd, rs, rn`
|15|14|13|12|11|10|9 | 8 7 6 | 5 4 3 |2 1 0 |
|- |- |- |- |- |- |- |:-----:|:-----:|:----:|
|0 |0 |1 |0 |0 |0 |0 |  rn   |  rs   |  rd  |

Encoding T2  
`SUB sp, #imm9`
|15|14|13|12|11|10|9 | 8 7 6 5 4 3 2 1 0 |
|- |- |- |- |- |- |- |:-----------------:|
|0 |0 |1 |0 |0 |0 |1 |        imm9       |

Encoding T3  
`SUB sp, rd`
|15|14|13|12|11|10|9 | 8 7 6 5 4 3 |2 1 0 |
|- |- |- |- |- |- |- |:-----------:|:----:|
|0 |0 |1 |0 |0 |1 |0 |ignored      |  rd  |

#### Subs carry register

|15 14 13 12 11 | 10 9 | 8 7 6 5 4 3 2 1 0 |
|:-------------:|:----:|:-----------------:|
|  0 0 1 0 1    |Reserved|xxxxxxxxxx|

`SDCS rd, rs [, rn]`
|15|14|13|12|11|10|9 | 8 7 6 | 5 4 3 |2 1 0 |
|- |- |- |- |- |- |- |:-----:|:-----:|:----:|
|0 |0 |1 |0 |1 |0 |0 |  rn   |  rs   |  rd  |

if `rn` gets ignored, `rd` will be `rn`

#### Reverse subs

|15 14 13 12 11 | 10 9 | 8 7 6 5 4 3 2 1 0 |
|:-------------:|:----:|:-----------------:|
|  0 0 1 1 0    |Reserved|xxxxxxxxxx|

`RSBS rd[,rs[, #imm5]]`
|15|14|13|12|11|10 9 8 7 6 | 5 4 3 |2 1 0 |
|- |- |- |- |- |:---------:|:-----:|:----:|
|0 |0 |1 |1 |0 |    imm5   |  rs   |  rd  |

`rd = rs - #imm`

* If `rs` gets ignored, `rs` will be `rd`.
* If `#imm5` gets ignored, `#0` will be supplied.

#### Mulipcation register

|15 14 13 12 11 | 10 9 | 8 7 6 5 4 3 2 1 0 |
|:-------------:|:----:|:-----------------:|
|  0 0 1 1 1    |Reserved|xxxxxxxxxx|

`MULS rd, rs[, rn]`
|15|14|13|12|11|10|9 | 8 7 6 | 5 4 3 |2 1 0 |
|- |- |- |- |- |- |- |:-----:|:-----:|:----:|
|0 |0 |1 |1 |1 |0 |0 |  rn   |  rs   |  rd  |

`rd = rs * rn`

* If `rn` gets ignored, `rn` will be `rd`

### Logic Operation

|15 14 | 13 12 11 10 | 9 8 7 6 5 4 3 2 1 0 |
|:----:|:-----------:|:-------------------:|
|01    |    Opcode   |     xxxxxxxxxx      |

* [0000](#and-immediate) AND immediate
* [0001](#and-register) AND with register
* [0010](#or-immediate) OR immedate
* [0011](#or-register) OR register
* [0100](#neg-register) NEG register
* [0101](#xor-register) XOR register
* [0110](#bics-immediate) BICS immediate
* [0111](#bics-register) BICS register
* [1000](#left-logical-shift) Logical left shift register
* [1001](#right-arithmetic-shift) Arithmetic right shift register
* [1010](#right-logical-shift) Logical right shift register
* [1011](#right-rotate-register) Rotate right register
* Other opcodes reserved

#### AND immediate

#### AND register

#### OR immediate

#### OR register

#### NEG register

#### XOR register

#### BICS immediate

#### BICS register

#### Left logical shift

#### Right arithmetic shift

#### Right Logical shift

#### Right rotate register

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

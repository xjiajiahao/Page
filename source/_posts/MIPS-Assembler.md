title: MIPS Assembler
date: 2016-05-11 22:59:59
tags: [MIPS, Assembler]
categories: Coding
---
### Introduction
This project is a simple implementation of MIPS assembler and disassembler. The *master* branch is based on Qt, which is recommended to use. The *command* branch is a command line version, which just supports assembler.  

The main functions are as follows:

* Assembler (support .s file as input and .coe and .bin as output file format)
* Disassembler (by now only supports .bin file as input file and .s as output file)

**See Github: [https://github.com/xjiajiahao/MIPS-Assembler](https://github.com/xjiajiahao/MIPS-Assembler)**
<!--more-->

### Screenshot

![screenshot](http://xjiajiahao.github.io/images/MIPS_Assembler_demo.png)

### How to Use
The assembler now supports 55 instructions and 5 data types(.asciiz, .byte, .half, .word) which can be found in `assembler.cpp`.  

The instructions supported are as follows:

* lw
* lb
* lbu
* lh
* lhu
* sw
* sb
* sh
* addi
* addiu
* andi
* ori
* xori
* lui
* slti
* sltiu
* beq
* bne
* blez
* bltz
* bgez
* j
* jal
* jalr
* jr
* mfhi
* mflo
* mthi
* mtlo
* eret
* mfc0
* mtc0
* break
* syscall
* la
* move
* li
* .asciiz
* .byte
* .half
* .word
* add
* addu
* sub
* subu
* slt
* sltu
* and
* or
* xor
* nor
* sll
* srl
* sra
* mult
* multu
* div
* divu

You'd better use **lowercase** keywords because it's expected to do so.

### NOTE

* `lw $rt, imm($rs)`  
* `addi $rt, $rs, imm`  
* `srl $rd, $rt, shamt`  

Here `imm` and `shamt` shoule be a **DECIMAL** integer rather than hexadecimal number.

The `.byte`, `.half`, `.word` values should be **HEXADECIMAL** integerr, i.e. 0x12345678.

The data is 4-alignment, which means it will waste some space for `.asciiz`, `.byte` and `.half` types.

The status line shows some information of current status.

If the program suddenly crashes while working, you might have to correct syntax errors in your code to make the program happy.

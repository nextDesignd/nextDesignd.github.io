---
layout: post
title:  "RISC-V Assembly"
categories: 
---
## Flow Control Instructions
A Instruction Set has to provide mechanism for programs to conditionally execute
code. These would be required to implement if-else, loops and case among other 
Flow Control constructs.

RSIC-V provides following instructions for this task ->

| `Signed`           |||| `Unsigned`          |||| `Description`              |
| -----------------  |||| ------------------- |||| -------------------------- |
| `beq rs1, rs2, L1` |||| `beq  rs1, rs2, L1` |||| `Jump to L1 if rs1==rs2`   |
| `bne rs1, rs2, L1` |||| `bne  rs1, rs2, L1` |||| `Jump to L1 if rs1!=rs2`   |
| `bgt rs1, rs2, L1` |||| `bgtu rs1, rs2, L1` |||| `Jump to L1 if rs1\>rs2`   |
| `bge rs1, rs2, L1` |||| `bgeu rs1, rs2, L1` |||| `Jump to L1 if rs1\<rs2`   |
| `blt rs1, rs2, L1` |||| `bltu rs1, rs2, L1` |||| `Jump to L1 if rs1\>=rs2`  |
| `ble rs1, rs2, L1` |||| `bleu rs1, rs2, L1` |||| `Jump to L1 if rs1<=rs2`   |

All of the above instructions are conditional in  the sense, the branch is taken
only when the condition is true. But programs frequently require unconditional
jumps also. This can be achieved by use conditional jump, but ensuring the
condition is always true. An example would be x0==x0, as x0 register is tied to
0, this condition is always true. So a unconditional jump to L1 would be
    `beq x0,x0,L1`

### Bounds Check
We can use unsigned compare instruction to reduce the cost of a very common check
used in multiple place in programs: Index out-of-bound check for arrays.
When accessing an array, it is important that the index used for access is valid.
Invalid index can lead to memory-corruptions or unexpected crashes.

The condition for a valid index is ->
    ` 0 <= index < array-length `

Normally implementing this with signed compares we would need to use 2 compare
statements in order to check both bounds. But if we use unsigned compare, we 
can reduce this comparison to just below
    ` index < array-length `

This is because, any negative number (<0) would (in 2's complement) be a very
large number (> array-length), thus failing the above comparison, making it
an invalid index



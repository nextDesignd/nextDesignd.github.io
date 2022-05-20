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
| `bgt rs1, rs2, L1` |||| `bgtu rs1, rs2, L1` |||| `Jump to L1 if rs1>rs2`    |
| `bge rs1, rs2, L1` |||| `bgeu rs1, rs2, L1` |||| `Jump to L1 if rs1<rs2`    |
| `blt rs1, rs2, L1` |||| `bltu rs1, rs2, L1` |||| `Jump to L1 if rs1>=rs2`   |
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

### Case/Switch Statements
Switch statements in C/C++ (or equivalent in other languages) allow programmer
to write a multi-branch tree based on value of a variable. One way to implement
this is with one beq statement for each case. This implementation would be similar
to what we have for a multi-branch if-else-if statements.

A better way would be to create a table of addresses (that point to code to execute
for different branches) call a branch-table, and then based on conditions in 
switch statement set a register equal to one of the address in table.
After which we can use RISC-V provided jump-and-link-register instruction to 
perform an **indirect-jump** to whatever address a register has. So suppose, 
we want to jump to address stored in register x9, we can call ->

    `jalr x0, 0(x9)`

First register is updated with address of the next instruction, and program-control
jumps to address in x9. As we don't want to return back to this instruction in-case
of switch statements (it would be useful in case of procedure calls), we provide
x0, as its always tied to 0, we don't actually store the return address.

## Support to implement functions

Programs follow following conventions when calling procedures(/functions), for
allocating registers ->
x10-x17: 8 parameter registers in which to pass parameters or return values
x1: one return address register to return to the point of origin

Convention to preserve register and reduce register spillage ->

- x5-x7 and x28-x31: temporary register that are *not* preserved by the callee
(procedure that is called), on procedure call
- x8-x9 and x18-x27: saved registers that must be preserved on a procedure call
(if used, the callee saves them and restores them)




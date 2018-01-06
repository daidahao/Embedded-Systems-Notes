# Lecture 3

## Flags

also known as `condition codes`.

Flags are used to give a signal that something either has or has not happened.

Flags are essentially **1 bit memory devices** and different microprocessors have different flags.

- the zero flag `Z`
- the negative flag `N`
- the carry flag `C`
- the overflow flag `V`

An ARM instruction that would set the zero flag is :

`SUBS r2, r1, r1`

Note that the mnemonic is `SUBS` rather than `SUB`.

They perform the same function except `SUBS` will set or clear the flags and `SUB` will not change the flags from their current setting.

### Instructions

The following instructions do not alter the flags:

`MOV, ADD, SUB, RSB, MUL, MLA, AND, EOR, ORR, BIC`

The following instructions do **set or clear the flags** as required:

`MOVS, ADDS, SUBS, RSBS, MULS, MLAS, ANDS, EORS, ORRS, BICS`

### Usage

Flags are used in two ways:

1. Main usage: **conditional execution**, determine if another instruction is executed or not.
2. Used in some arithmetic instructions as **an additional value**.

### Conditional Execution

an instruction either does or does not execute depending upon the state of the flags (or ‘condition codes’).

Almost all ARM instructions can be conditionally executed and this is **indicated by the addition of two letters** in the mnemonic of the instruction.

#### Condition Fields

There are **15** different condition fields which may be appended to (almost) any mnemonic.

Most common ones:

- `EQ` - ‘equal’ - executed only if the `Z` is set.
- `NE` - ‘not equal’ - executed if `Z` is clear.
- `CS` - ‘carry set’ - executed if `C` is set.
- `CC` - ‘carry clear’ - executed if `C` is clear.
- `MI` - ‘minus’ - executed only if N is set.
- `PL` - ‘plus’ or ‘positive’ - executed if N is clear.
- `VS` - ‘overflow’ - executed if V is set.
- `VC` - ‘no overflow’ - executed if V is clear.
- There are 7 more condition fields including ‘always’ (`AL`) which is the default if no condition field is specified.

## Branches

### Unconditional Branches

An unconditional branch always executes and it reloads the program counter with a new value.

The purpose of a branch instruction is to continue the program at a different location in memory.

### Conditional Branches

Unconditional branches are of very little use but conditional branches have many uses.

`BNE 0x08C00000`

branch to 0x08C00000 if not equal.

### Restrictions

The memory address in the branch instruction must be contained within the instruction itself.

**24 bits of the 32 bit** instruction code are used to determine the destination address of the branch as an offset from the current program counter value.

This means that the destination address must be within **±32MB** of the memory address of the branch instruction.

### Mnemonics

In general assembly language programs are written without any knowledge of the memory address for the corresponding machine code.

So the mnemonic for branch uses **labels** rather than actual memory addresses and the assembler program determines the actual value used.

### Use of the zero flag

The zero flag is commonly used to **test if a program ‘loop’ has been executed a certain number of times**;

### The carry flag

The carry flag is used in many arithmetic and logic instructions (best illustrated by addition).

- In common with the other flags it can be used to determine if a conditional instruction is executed or not.
- The other use of the carry flag is in the addition instruction `ADC` (and the subtraction instructions `SBC` and `RSC`).

#### `ADC`

The instruction ADC ‘add with carry’ adds together the two values and adds another 1 if the carry flag is set.

`ADC` is used when we add together numbers greater than ($2^{32}-1$).

- `r0` holds `0xCB417800` and `r1` holds `0x00000002`
- `r2` holds `0x42770C00` and `r3` holds `0x00000003`

`ADDS r4, r2, r0`

The carry flag is set and `r4` holds the lowest 32
bits: `0x0DB88400`.

`ADC r5, r3, r1`

Because the carry flag is set, an extra 1 is added into the sum so `r5` will hold `0x00000006`.

Taken together `r5` and `r4` hold the value `0x60DB88400` which is $26,000,000,000_{10}$

## Negative numbers

1) Sign magnitude.
2) Two’s complement.

### Sign bit

In each case the most significant bit - **m.s.b.** - indicates the sign (1 for -ve and 0 for +ve).

To find out if a number is -ve or +ve

`MOVS rx, rx`

The value in register `rx` remains unchanged but the negative flag is set if the `m.s.b.` is `1` and it is cleared if the `m.s.b.` is `0`.

### Sign magnitude

a negative number is the same as a positive number but with the `m.s.b.` equal to `1`.

In general any hexadecimal number is negative if it starts with 8 or greater and it is positive if it starts with 7 or less.

The magnitude of a ‘sign magnitude’ number is easy to find: simply `AND` with `0x7FFFFFFF`.

However **sign magnitude numbers cannot be used for arithmetic**.

### 2’s complement

a negative number, `-x`, is given by the value `2n - x` for an n bit processor.

`-20640`

1. Find the positive value: `0x000050A0`
2. Invert all bits (`0 -> 1`, `1 -> 0`): `0xFFFFAF5F`
3. Add `1`: `0xFFFFAF60`

Unlike sign magnitude, arithmetic is simple in two’s complement.

In two’s complement any hexadecimal number which has an 8 or greater for the most significant digit is negative since the `m.s.b.` is 1.

#### Subtraction

such as `x - y`, is implemented in a microprocessor by finding the two’s complement of `-y` and then performing the addition, `x + (-y)`

### The overflow flag `V`

is set because there is an overflow into the `m.s.b.`.

## Flags - summary.
- The zero flag, `Z`, is set when the result (not including any carry out) is `0x00000000`.
- The negative flag, `N`, is set when the most significant bit of the 32 bit result is 1.
- The carry flag, `C`, is set when the result (taken as an unsigned integer) is greater than ($2^{32} - 1$).
- The overflow flag, `V`, is set when the result (taken as a two’s compliment number) is greater than ($2^{31} - 1$) or less than $-2^{31}$

## Floating point numbers

$A × 2^n$

Using the IEEE 754 standard the `A` value has a sign and 24 bits and the exponent value, `n`, has 8 bits.

### $5,637,144,576_{10}$ in IEEE 754

$5,637,144,576_{10} = 1 0101 0000 0000 0000 0000 0000 0000 0000_2$

Normaliztion:

$= 1.0101 0000 0000 0000 0000 000_2 × 2^{32}$

1. The exponent is $32_{10} = 100000_2$ but in the IEEE 754 format we must add $127_{10} = 1111111_2$ to this to give $10011111_2 (=159_{10})$.
2. In 32 bits this becomes:
0**100 1111 1**010 1000 0000 0000 0000 0000

Note that the normalization means that the m.s.b. of the A value is always 1 so this can be omitted.

The `m.s.b.` of the 32 bit word is a **sign bit**.















...

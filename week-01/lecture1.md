# CS For Hackers - Lecture 1.1

What are we going to cover?

- The nature of a computer
- Digital logic
- The ALU (Arithmetic Logic Unit)

We'll be traveling from the physical to the essense of a working computer in 1 hour.

This may be review for some of you, but bear with it and make sure you understand it.

## Circuits

We'll also look at a bunch of digital logic circuits. You can download and play with
them using [iCircuit](http://icircuitapp.com/), PSPICE, or any program supporting the
`.cir` format.

<style>
td, th { padding: 5px; }
table { padding: 0 20px; }
</style>

----

# But first: The Secret

- The secret to practical computer performance...
- The key to decisions computer scientists made over the years...

> *Computers are a hierarchy of caches that are fast in the average case.*

- We'll come back to this throughout the course.

----

# Early Comptuers

<img src="http://upload.wikimedia.org/wikipedia/commons/0/06/Human_computers_-_Dryden.jpg"
style="max-height: 80%; max-width: 80%;"
/>

<http://en.wikipedia.org/wiki/Human_computer>

----

# Another computer

<iframe style="max-height: 70%" width="840" height="630" src="http://www.youtube.com/embed/GcDshWmhF4A" frameborder="0" allowfullscreen></iframe>

<a href="http://www.youtube.com/watch?v=GcDshWmhF4A">http://www.youtube.com/watch?v=GcDshWmhF4A</a>

----

# Another computer

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/binary-adder.jpg"
style="max-height: 80%; max-width: 80%;">

<http://onecreativeblog.com/post/8785145888/>

----

# So... what is a computer?

- ...stay tuned for week 5 :)
- A computer have many implementations from the physical world<sup>1</sup>.
- But we'll focus on the one's we're familiar with (based on digital logic).

[1] <http://consc.net/papers/rock.html> "Does a Rock Implement Every Finite-State Automaton?" David Chalmers


----

# Digital Logic

digital - adj. - from Latin, digitalus (finger)

- Fingers are *unary*, the most simple way to count, but not vary practical for large numbers! (Could you pay for a car in pennies?)
- So we add another layer of indirection (place value).
- Binary is the simplest way of doing this, and much more practical to represent. (State of rocker, live/dead wire).


> "All problems in computer science can be solved by another level of indirection." -- David Wheeler

> "All problems in computer science can be solved by another layer of complexity." -- Dirk Neumann

----

# Binary numbers

(Quick review if you're rusty, or have only done high-level languages)

<table>
<tr>
<th>Decimal<th>100</th><th>10</th><th>1</th>
<th>Binary</th><th>8</th><th>4</th><th>2</th><th>1</th>
</tr>

<tr>
<td></td><td></td></td><td></td><td>1</td>
<td></td><td></td><td></td></td><td></td><td>1</td>
</tr>
<tr>
<td></td><td></td></td><td></td><td>&#8942;</td>
<td></td><td></td><td></td><td>1</td><td>0</td>
</tr>
<tr>
<td></td><td></td></td><td></td><td>9</td>
<td></td><td></td><td></td><td>1</td><td>1</td>
</tr>
<tr>
<td></td><td></td></td><td>1</td><td>0</td>
<td></td><td></td><td>1</td><td>0</td><td>0</td>
</tr>
<tr>
<td></td><td></td></td><td>&#8942;</td><td>&#8942;</td>
<td></td><td></td><td>1</td><td>0</td><td>1</td>
</tr>
<tr>
<td></td><td></td></td><td>9</td><td>9</td>
<td></td><td></td><td>1</td><td>1</td><td>0</td>
</tr>
<tr>
<td></td><td>1</td><td>0</td><td>0</td>
<td></td><td></td><td>1</td><td>1</td><td>1</td>
</tr>
</table>

Some traditional names:

<table>
<tr>
<th>Nybble</th> <td>1010</td> <td>(A)</td>
</tr>
<tr>
<th>Byte/Octet</th> <td>10101010</td> <td>(AA)</td>
</tr>
<tr>
<th>32-bit Word</th> <td>10101010101010101010101010101010</td> <td>(AAAAAAAA)</td>
</tr>
</table>


----

# Convenient nybbles

<table>
<tr><th>Binary<th>Hex</th><th>Octal</th>
<tr><td>0000</td><td>0</td><td>00</td></tr>
<tr><td>0001</td><td>1</td><td>01</td></tr>
<tr><td>0010</td><td>2</td><td>02</td></tr>
<tr><td>0011</td><td>3</td><td>03</td></tr>
<tr><td>0100</td><td>4</td><td>04</td></tr>
<tr><td>0101</td><td>5</td><td>05</td></tr>
<tr><td>0110</td><td>6</td><td>06</td></tr>
<tr><td>0111</td><td>7</td><td>07</td></tr>
<tr><td>1000</td><td>8</td><td>10</td></tr>
<tr><td>1001</td><td>9</td><td>11</td></tr>
<tr><td>1010</td><td>A</td><td>12</td></tr>
<tr><td>1011</td><td>B</td><td>13</td></tr>
<tr><td>1100</td><td>C</td><td>14</td></tr>
<tr><td>1101</td><td>D</td><td>15</td></tr>
<tr><td>1110</td><td>E</td><td>16</td></tr>
<tr><td>1111</td><td>F</td><td>17</td></tr>
</table>

(flavor in every byte)

----

# Circuits and Truth tables

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/components.png)


----

# AND

```c
    // in c
    c = a && b; // logical
    c = a & b;  // bitwise (logical, but 1 bit at a time)
```

<table style="float:left">
<tr><th>A<th>B</th><th>AND</th>
<tr><td>0</td><td>0</td><td>0</td></tr>
<tr><td>0</td><td>1</td><td>0</td></tr>
<tr><td>1</td><td>0</td><td>0</td></tr>
<tr><td>1</td><td>1</td><td>1</td></tr>
</table>

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/and.png)
[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/and.cir)


Quick Quiz: What is 1010 & 1100?

----

# OR

```c
    // in c
    c = a || b; // logical
    c = a | b;  // bitwise (logical, but 1 bit at a time)
```

<table style="float:left">
<tr><th>A<th>B</th><th>OR</th>
<tr><td>0</td><td>0</td><td>0</td></tr>
<tr><td>0</td><td>1</td><td>1</td></tr>
<tr><td>1</td><td>0</td><td>1</td></tr>
<tr><td>1</td><td>1</td><td>1</td></tr>
</table>

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/or.png)
[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/or.cir)

----

# XOR (Exclusive OR)

```c
    // in c
    c = a ^ b; // bitwise
    // no logical operator, but you can do !a != !b
```

<table style="float:left">
<tr><th>A<th>B</th><th>XOR</th>
<tr><td>0</td><td>0</td><td>0</td></tr>
<tr><td>0</td><td>1</td><td>1</td></tr>
<tr><td>1</td><td>0</td><td>1</td></tr>
<tr><td>1</td><td>1</td><td>0</td></tr>
</table>

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/xor.png)
[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/xor.cir)

----

# Quick Quiz

- What is 1010 XOR 1010? 
- What is 0110 XOR 0110? 
- What is N XOR N?

----

# A + B (Half Adder)

<table style="float:left">
<tr><th>A<th>B</th><th>C</th><th>carry</th>
<tr><td>0</td><td>0</td><td>0</td><td>0</td></tr>
<tr><td>0</td><td>1</td><td>1</td><td>0</td></tr>
<tr><td>1</td><td>0</td><td>1</td><td>0</td></tr>
<tr><td>1</td><td>1</td><td>0</td><td>1</td></tr>
</table>

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/half-adder.png)
[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/half-adder.cir)

- Does the C column look familiar?
- Does the carry column look familiar?

----

# Full Adder Quiz

<table style="float:left">
<tr><th>carry in</th><th>A<th>B</th><th>C</sub></th><th>carry out</th>
<tr><td>0</td><td>0</td><td>0</td><td>?</td><td>?</td></tr>
<tr><td>0</td><td>0</td><td>1</td><td>?</td><td>?</td></tr>
<tr><td>0</td><td>1</td><td>0</td><td>?</td><td>?</td></tr>
<tr><td>0</td><td>1</td><td>1</td><td>?</td><td>?</td></tr>
<tr><td>1</td><td>0</td><td>0</td><td>?</td><td>?</td></tr>
<tr><td>1</td><td>0</td><td>1</td><td>?</td><td>?</td></tr>
<tr><td>1</td><td>1</td><td>0</td><td>?</td><td>?</td></tr>
<tr><td>1</td><td>1</td><td>1</td><td>?</td><td>?</td></tr>
</table>

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/full-adder.png"
style="max-height: 60%; max-width: 80%;">

----

----

# Full Adder

<table style="float:left">
<tr><th>carry in</th><th>A<th>B</th><th>C</sub></th><th>carry out</th>
<tr><td>0</td><td>0</td><td>0</td><td>0</td><td>0</td></tr>
<tr><td>0</td><td>0</td><td>1</td><td>1</td><td>0</td></tr>
<tr><td>0</td><td>1</td><td>0</td><td>1</td><td>0</td></tr>
<tr><td>0</td><td>1</td><td>1</td><td>0</td><td>1</td></tr>
<tr><td>1</td><td>0</td><td>0</td><td>1</td><td>0</td></tr>
<tr><td>1</td><td>0</td><td>1</td><td>0</td><td>1</td></tr>
<tr><td>1</td><td>1</td><td>0</td><td>0</td><td>1</td></tr>
<tr><td>1</td><td>1</td><td>1</td><td>1</td><td>1</td></tr>
</table>

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/full-adder.png"
style="max-height: 60%; max-width: 80%;">
[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/full-adder.cir)

----
# Chained Full Adder

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/adder-chain.png)

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/binary-adder.jpg"
style="max-height: 40%; max-width: 80%;">


----

# Multiplexer (Mux)

Selects between multiple inputs.

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/multiplexer.png"
style="max-height: 80%; max-width: 80%;">

[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/multiplexer.cir)


----

# Demux/Decoder

Selects between multiple outputs for a given input.

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/demux.png"
style="max-height: 80%; max-width: 80%;">

[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/demux.cir)

----

# Basic memory (Latch)

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/d-latch.png"
style="max-height: 80%; max-width: 80%;">

[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/d-latch.cir)

- Fast
- Used for registers, cache 


----

# Not-so basic memory

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/dram.png"
style="max-height: 80%; max-width: 80%;">

[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/dram.cir)

- Dense
- Slow
- Used for DRAM

<!--
----

# We're done!

- Now we have all the building blocks of a really cool machine!
- Anything that mimics the behavior of these blocks can be made a computer

-->


----

# The ALU (Arithmetic logic unit)

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/alu.png"
style="max-height: 90%; max-width: 80%;">

----

# A "Real" ALU

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/1-bit-alu.png"
style="max-height: 80%; max-width: 80%;">

[download circuit](https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/circuits/1-bit-alu.cir)

----

# The Von Neumann Machine

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/von-neumann.png"
style="max-height: 90%; max-width: 80%;">

----

# Execution cycle


```python
    #!/usr/bin/env python

    program_counter = 0
    memory = []

    def get_data_address(instruction):
        ...

    def execute(instruction, data):
        ...

    while True:
        instruction = memory[program_counter]
        data = memory[get_data_address(instruction)]
        execute(instruction, data)
```
        

----

# How instructions look


```c
    // c
    c = a + b;
    if (c != 10) {
        do_something();
    }
```

```assembly
    // assembly
    add eax, ebx;
    sub eax, 10;
    jnz 0x42;
```

```
    // machine code
    0011 0000 0001
    0100 0000 1010
    1001 0010 1010
     ^     |   | 
    opcode |   ^ operand
           ^ operand
```

- Wire up the opcode to the ALU selector
- (The machine code is actually BS, don't feed it to a computer.)

----

# Playing Computer

```c
    // c
    int a = 5, b = 1;
    start:
    a = a + a;
    a = a + b;
    goto start;
```

``` assembly
    // assembly
    nop;
    add A, A;
    add A, B;
    mv  PC, B;
```

<table style="float:left">
<tr><th>Instruction</th><th>Action</th><th>Opcode</th></tr>
<tr><td>nop</td><td>nothing</td><td>0000</td></tr>
<tr><td>add</td><td>X = X + Y</td><td>0001</td></tr>
<tr><td>sub</td><td>X = X - Y</td><td>0010</td></tr>
<tr><td>and</td><td>X = X & Y</td><td>0011</td></tr>
<tr><td>or</td><td>X = X | Y</td><td>0100</td></tr>
<tr><td>xor</td><td>X = X ^ Y</td><td>0101</td></tr>
<tr><td>mv</td><td>X = Y</td><td>0110</td></tr>
<tr><td>load</td><td>X = MEM</td><td>0111</td></tr>
<tr><td>store</td><td>MEM = X</td><td>1000</td></tr>
</table>

<table style="float:left">
<tr><th>Register</th><th>Index</th><th>Contents</th></tr>
<tr><td>A</td><td>0000</td><td>0101</td></tr>
<tr><td>B</td><td>0001</td><td>0001</td></tr>
<tr><td>PC</td><td>0010</td><td>0000</td></tr>
</table>

<table style="float:left">
<tr><th>Memory Address</th><th>Contents</th></tr>
<tr><td>0000</td><td>0000 0000 0000</td></tr>
<tr><td>0001</td><td>0001 0000 0000</td></tr>
<tr><td>0010</td><td>0001 0000 0001</td></tr>
<tr><td>0011</td><td>0101 0010 0001</td></tr>
</table>
    

----

# Playing Computer (Quiz!)

- Start with the system state below.
- What is the value of register B by the end of the program?


<table style="float:left">
<tr><th>Instruction</th><th>Action</th><th>Opcode</th></tr>
<tr><td>nop</td><td>nothing</td><td>0000</td></tr>
<tr><td>add</td><td>X = X + Y</td><td>0001</td></tr>
<tr><td>sub</td><td>X = X - Y</td><td>0010</td></tr>
<tr><td>and</td><td>X = X & Y</td><td>0011</td></tr>
<tr><td>or</td><td>X = X | Y</td><td>0100</td></tr>
<tr><td>xor</td><td>X = X ^ Y</td><td>0101</td></tr>
<tr><td>mv</td><td>X = Y</td><td>0110</td></tr>
<tr><td>load</td><td>X = MEM</td><td>0111</td></tr>
<tr><td>store</td><td>MEM = X</td><td>1000</td></tr>
</table>

<table style="float:left">
<tr><th>Register</th><th>Index</th><th>Contents</th></tr>
<tr><td>A</td><td>0000</td><td>0001</td></tr>
<tr><td>B</td><td>0001</td><td>0101</td></tr>
<tr><td>PC</td><td>0010</td><td>0010</td></tr>
</table>

<table style="float:left">
<tr><th>Memory Address</th><th>Contents</th></tr>
<tr><td>0000</td><td>0101 0010 0010</td></tr>
<tr><td>0001</td><td>0000 0000 0000</td></tr>
<tr><td>0010</td><td>0001 0000 0001</td></tr>
<tr><td>0011</td><td>0011 0001 0000</td></tr>
</table>

----

# Playing Computer (Answer!)

First, it is easier to see what is going on if we translate the contents of
memory (the program that is going to execute) into a format that we can read
more easily. Here, I just translate the binary codes and put a comma between
the operands, which is how most assembly language looks:

<table style="float:left">
<tr><th>Memory Address</th><th>Contents</th></tr>
<tr><td>0000</td><td>xor PC, PC;  # whoa, this is a crazy instruction!</td></tr>
<tr><td>0001</td><td>nop;  # nop does not have operands</td></tr>
<tr><td>0010</td><td>add A, B; # A <- A + B</td></tr>
<tr><td>0011</td><td>and B, A; # B <- B & A</td></tr>
</table>

Second, notice that your esteemed instructor has plaid a practical joke on
you! The program counter is set to `0010` (2 in decimal), which means that
that the crazy instruction at the beginning does not matter! The moral of 
this story is, watch out for people from Caltech, because they are notorious
pranksters (ever heard of Richard Feynman? he would pick your safe!).

<table style="float:left">
<tr><th>Register</th><th>Index</th><th>Contents</th></tr>
<tr><td>A</td><td>0000</td><td>0001</td></tr>
<tr><td>B</td><td>0001</td><td>0101</td></tr>
<tr><td>PC</td><td>0010</td><td>**0010**</td></tr>
</table>

So, starting off from memory location 0010 (2), we have the following instructions:


```
    add A, B; # add the contents of B to the contents of A, store in A
    and B, A; # and the contents of A with the contents of B, store in B
```

We can carry them out by hand (it might be helpful to refer to the truth table for
addition and AND):

```
      0001 A
    + 0101 B
    ------
      0110 A 

      0110 A 
    & 0101 B
    ------
      0100 B
```

W00t! Violá! QED. &#8718;. We have our answer:

> What is the value of register B by the end of the program? **0100** or **5**

----

# Next Time (Monday)

- System layout
- Memory access times
- Caches (a hierarchy of them!)
- Pipelining
- Context switching
- Branch prediction
- Hacking Challenge

----

# Learn more


- "Elements of Computing Systems" Nisan and Schocken
    - Held in high regard
    - Build a computer from scratch
    - [~$20 on Amazon](http://www.amazon.com/Elements-Computing-Systems-Building-Principles/dp/026214087X), but many chapters available [online](http://www1.idc.ac.il/tecs/)

- "Structured Computer Organization" Andrew Tannenbaum
    - I learned from this one
    - [~$115 on Amazon](http://www.amazon.com/Structured-Computer-Organization-Andrew-Tanenbaum/dp/0131485210)

- "The Art of Assembly Language Programming" Randall Hyde
    - A classic! Shift bits like a pro!
    - available [online](http://homepage.mac.com/randyhyde/webster.cs.ucr.edu/www.artofasm.com/index.html)

----

# Fin


----

# Bonus

- Google: "minecraft alu"



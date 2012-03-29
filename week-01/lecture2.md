# CS For Hackers - Lecture 1.2

What are we going to cover?

- System layout
- Interrupts
- Hierarchy of caches
- Pipelining
- Branch prediction
- Context switching

<style>
td, th { padding: 5px; }
table { padding: 0 20px; }
</style>

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

# Playing Computer

## Quiz from last time

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

# Playing Computer (worked out)

First, it is easier to see what is going on if we translate the contents of
memory (the program that is going to execute) into a format that we can read
more easily. Here, I just translate the binary codes and put a comma between
the operands, which is how most assembly language looks:

<table>
<tr><th>Memory Address</th><th>Contents</th></tr>
<tr><td>0000</td><td>xor PC, PC;</td></tr>
<tr><td>0001</td><td>nop;</td></tr>
<tr><td>0010</td><td>add A, B;</td></tr>
<tr><td>0011</td><td>and B, A;</td></tr>
</table>

<table>
<tr><th>Register</th><th>Index</th><th>Contents</th></tr>
<tr><td>A</td><td>0000</td><td>0001</td></tr>
<tr><td>B</td><td>0001</td><td>0101</td></tr>
<tr><td>PC</td><td>0010</td><td>0010 # what?? a trick!</td></tr>
</table>

----

So, starting off from memory location 0010 (2), we have the following instructions:


    add A, B;
    and B, A;

We can carry them out by hand (it might be helpful to refer to the truth table for
addition and AND):


      0001 A
    + 0101 B
    ------
      0110 A 

      0110 A 
    & 0101 B
    ------
      0100 B

W00t! Violá! QED. &#8718;. We have our answer:

> What is the value of register B by the end of the program? **0100** or **4**

----

# System Layout

----

# The Von Neumann Machine

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/von-neumann.png"
style="max-height: 90%; max-width: 80%;">

- We will zoom out from here.

----

# A Typical CPU

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/cpu-layout.png"
style="max-height: 90%; max-width: 80%;">

<!--
- Separate data and instruction caches.
- Interconnects
-->

----

# A Typical System

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/system-layout.png"
style="max-height: 90%; max-width: 80%;">

----

# Aside: Interrupts

This is how things interact with the CPU

- Hardware Interrupts
  - Like a prankster deliveryperson, drop some data somewhere and ring the doorbell (light up an interrupt wire)
  - The PC jumps to an *interrupt handler* somewhere in memory
  - Remember IRQs?
- Software Interrupts
  - Call a kernel or BIOS function

Example:

    mov ax, 0000000000010011b;
    int 10h; # set graphics mode
    mov ax, 0000110000001100b;
    mov cx, 0000000000001010b;
    mov dx, 0000000000001111b;
    int 10h; # set pixel

----

# A Typical System

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/system-layout.png"
style="max-height: 90%; max-width: 80%;">

----

# How you should actually think of it

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/orders-of-magnitude.png"
style="max-height: 90%; max-width: 80%;">

----

# Orders of magnitude

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/orders-of-magnitude-anthro.png"
style="max-height: 80%; max-width: 80%;">

----

# Remember The Secret?

> *Computers are a hierarchy of caches that are fast in the average case.*

Well, there is a reason behind it...

----

# The Locality Principle

> *If you accessed something, you will probably end up accessing its neighbor.*

## A.K.A.

- Locality of reference (theorists)
- Cache locality (systems people)
- Data locality (big data pundits)

## Facets

- Temporal locality
    - Things that were just accessed will tend to get accessed again
- Spacial locality
    - Things that are nearby in memory will get accessed

Can you think of examples?

----

# Applications of locality

How do *you* think locality affects the following things:

- Searching the web
- Web performance
- Games
- Big Data

----

# Hit ratio

Ryan, you keep saying "fast in the average case". What is the average case?

    h = hit ratio
    c = cache access time
    m = memory access time

    average access time = c + (1-h) * m

Notice something:

    # hit ratio --> 0
    average access time = c + m

    # hit ratio --> 1
    average access time = c

----

# Quiz

    average access time = c + (1-h) * m

- How fast is the average access if you have a `1 ns` cache,
a `100 ns` memory, and a hit ratio of `0.5`? What about `0.9`?

- What do you think the hit ratio will be for fetching code?

----

# Our simple jumping computer

<table style="float:left">
<tr><th>Instruction</th><th>Action</th></tr>
<tr><td>nop</td><td>nothing</td></tr>
<tr><td>add</td><td>X = X + Y</td></tr>
<tr><td>sub</td><td>X = X - Y</td></tr>
<tr><td>and</td><td>X = X & Y</td></tr>
<tr><td>or</td><td>X = X | Y</td></tr>
<tr><td>xor</td><td>X = X ^ Y</td></tr>
<tr><td>mv</td><td>X = Y</td></tr>
<tr><td>load</td><td>Y = mem[X]</td></tr>
<tr><td>store</td><td>mem[X] = Y</td></tr>
<tr><td>jz</td><td>if X == 0, PC = Y</td></tr>
<tr><td>jnz</td><td>if X != 0, PC = Y</td></tr>
<!--tr><td>jnz</td><td>if X != 0, PC = Y</td></tr-->
<!--tr><td>srs</td><td>MEM[X] = registers</td></tr-->
<!--tr><td>rrs</td><td>registers = mem[X]/td></tr-->
</table>

<table style="float:left">
<tr><th>Register</th><th>Contents</th></tr>
<tr><td>A</td><td>1010</td></tr>
<tr><td>B</td><td>0001</td></tr>
<tr><td>PC</td><td>0000</td></tr>
<!--tr><td>BP</td><td>0000</td></tr-->
</table>

<table style="float:left">
<tr><th>Memory Address</th><th>Contents</th></tr>
<tr><td>0000</td><td>nop;</td></tr>
<tr><td>0001</td><td>sub A, B;</td></tr>
<tr><td>0010</td><td>nop; # imagine these</td></tr>
<tr><td>0011</td><td>nop; # nop instructions</td></tr>
<tr><td>0100</td><td>nop; # do something</td></tr>
<tr><td>0101</td><td>nop; # useful :)</td></tr>
<tr><td>0110</td><td>nop;</td></tr>
<tr><td>0111</td><td>jnz A, B;</td></tr>
<tr><td>1000</td><td>nop; # these</td></tr>
<tr><td>1001</td><td>nop; # too</td></tr>
<tr><td>1010</td><td>nop;</td></tr>
<tr><td>1011</td><td>nop;</td></tr>
<tr><td>1100</td><td>nop;</td></tr>
<tr><td>1101</td><td>nop;</td></tr>
<tr><td>1110</td><td>nop;</td></tr>
<tr><td>1111</td><td>nop;</td></tr>
</table>

----

# Question

- What does this thing do?
- How fast will it execute?
- What assumptions do you have to make?

Given:

- Our cache is 8 instructions in size
- Fetching an instruction from cache takes 1 cycle
- Fetching an instruction from memory takes 10 cycles, but 8
  instructions are always fetched at a time


----

# It gets worse (or better)

----

# A Generic Pipeline

- Each instruction is divided up into several steps
- The steps happen (or can happen) in *serial*
- Does this not wake your inner industrialist?

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/pipeline-overview.png"
style="max-height: 90%; max-width: 80%;">

- How long does it take to make a car?

----

# Cars

- How long does it take to make a car?
> About 24 hours 

- How many cars does a factory make an hour?
> Maybe 50-100

- And why is that?


----

# Pipeline In Context

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/pipeline-cpu.png"
style="max-height: 90%; max-width: 80%;">

----

# Pipeline Timeline

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/pipeline.png"
style="max-height: 90%; max-width: 80%;">

Note:

- Pipelines can get very long.
- ARM Cortex-A8 (iPhone 3G) has 13 stages!


----

# Question

- What happens when you hit a conditional jump?
- What stage does the conditional jump occur?

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/pipeline-question.png"
style="max-height: 90%; max-width: 80%;">

----

# Branch prediction

## Manual

    #define likely(x)       __builtin_expect((x),1)
    #define unlikely(x)     __builtin_expect((x),0)

    if (unlikely(y > 0)) {
        ...
    }

    if (likely(x > y)) {
        ...
    }


## Automatic

- How would you do it?

----

# Second Chance Machine

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/state-machine.png"
style="max-height: 90%; max-width: 80%;">

- This is called a state machine.
- How would you implement it in code?

----

# Question

- How would you run more than one program at a time?

----

# Context Switching Computer

- We add BP (base pointer), srs (save register state), rrs (restore register state)
- How would you use this to run more than one program?

<table style="float:left">
<tr><th>Instruction</th><th>Action</th></tr>
<tr><td>nop</td><td>nothing</td></tr>
<tr><td>add</td><td>X = X + Y</td></tr>
<tr><td>sub</td><td>X = X - Y</td></tr>
<tr><td>and</td><td>X = X & Y</td></tr>
<tr><td>or</td><td>X = X | Y</td></tr>
<tr><td>xor</td><td>X = X ^ Y</td></tr>
<tr><td>mv</td><td>X = Y</td></tr>
<tr><td>load</td><td>Y = mem[X+BP]</td></tr>
<tr><td>store</td><td>mem[X+BP] = Y</td></tr>
<tr><td>jz</td><td>if X == 0, PC = Y+BP</td></tr>
<tr><td>jnz</td><td>if X != 0, PC = Y+BP</td></tr>
<tr><td>srs</td><td>mem[X] = registers</td></tr>
<tr><td>rrs</td><td>registers = mem[X]</td></tr>
<tr><td>ljz</td><td>if X == 0, PC = Y</td></tr>
<tr><td>ljnz</td><td>if X != 0, PC = Y</td></tr>
</table>

<table style="float:left">
<tr><th>Register</th><th>Contents</th></tr>
<tr><td>A</td><td>1010</td></tr>
<tr><td>B</td><td>0001</td></tr>
<tr><td>PC</td><td>0000</td></tr>
<tr><td>BP</td><td>0000</td></tr>
</table>

<!--
----

# What does a CPU look like?

<a href="http://www.flickr.com/photos/biwook/153056995/" title="Old processor's die by Ioan Sameli, on Flickr"><img src="http://farm1.staticflickr.com/64/153056995_5ef8b01016.jpg" width="500" height="386" alt="Old processor's die"></a>

----
# How is it made?
-->

----

# What did we learn?

- System layout
- Interrupts
- Hierarchy of caches
- Pipelining
- Branch prediction
- Context switching

----

# Learn more

- [OSDev Wiki](http://wiki.osdev.org/Main_Page)
    - Some people build OSes from scratch for fun.
    - They learn a lot about hardware along the way!

- "Elements of Computing Systems" Nisan and Schocken
    - Held in high regard
    - Build a computer from scratch
    - [~$20 on Amazon](http://www.amazon.com/Elements-Computing-Systems-Building-Principles/dp/026214087X), but many chapters available [online](http://www1.idc.ac.il/tecs/)

- "The Art of Assembly Language Programming" Randall Hyde
    - A classic! Shift bits like a pro!
    - available [online](http://homepage.mac.com/randyhyde/webster.cs.ucr.edu/www.artofasm.com/index.html)

----

# Fin

----

# Hacking Challenge

Background reading:
<http://igoro.com/archive/gallery-of-processor-cache-effects/>

- What kind of caches does your computer have? What are their sizes?  
- Write a program (in a language of your choice) that shows convincing evidence
  of your computer’s caches in operation. Can you determine the cache size by
  plotting the time taken by different access patterns?  You might have to get
  creative if your language is dynamic, reading up on how data types are stored
  and how big they are.

Try to get the above done in less than 4 focused hours.

*Bonus question:* Can you write a program that shows the effects of branch
prediction? Can you trip up the branch predictor and slow down your program?
How much does branch prediction matter versus cache locality for the programs
you write every day?


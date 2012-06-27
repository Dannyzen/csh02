# CS For Hackers - Week 1 - Systems Architecture

Instructor: [Ryan Witt](http://twitter.com/onecreativenerd), [Opani](http://opani.com/)

Location: New GA classrooms!

You have to know the hardware in order to understand how the software behaves. This
will be a theme through the rest of the class, and we'll come back to the
points we learn this week. Topics: Binary logic and the physics of
implementation, parts of a computer and how they interact, understanding the
speed order of magnitude of each part, computing as a hierarchy of caches,
cache locality.

## Lectures

1. [Lecture 1](https://github.com/generalassembly-studio/cs-for-hackers/blob/master/week-01/lecture1.md): What is a computer? From digital logic to the ALU.
2. Lecture 2: System dynamics, cache locality and branch prediciton.

## Hacking Challenge

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


## Further Reading

- <http://igoro.com/archive/fast-and-slow-if-statements-branch-prediction-in-modern-processors/>
- <http://stackoverflow.com/questions/3896859/reproducing-gallery-of-processor-cache-effects>
- <http://www.ece.uah.edu/~milenka/docs/milenkovic_spe02.pdf>


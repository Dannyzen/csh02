# CS For Hackers - Lecture 6.1
## Programming Languages (In Theory)

When we're done, you should understand:

- What makes a language more powerful than another (maybe)
- The difference between *syntax* and *semantics*
- Abstract Syntax Trees

----

# But first, a video

<http://www.youtube.com/watch?v=ywWBy6J5gz8>

----

# What is the purpose of a programming language?

----

# Purpose of a programming language?

- Communicate *your intent* to a computer
- Communicate it *well*
- Communicate it *quicky*
- Communicate it *with few errors*

- Can you talk about love in German?
- Can you talk about organic chemistry in Italian?

----

# Some (fun)ctions

Remember quicksort?

- Pick a pivot
- Move everything greater than the pivot to the right
- Move everything less than the pivot to the left
- Quicksort the left
- Quicksort the right

Quotes are from Mike Vanier's *If programming languages were cars*:

<http://users.cms.caltech.edu/~mvanier/hacking/rants/cars.html>

----

# C

> C is a racing car that goes incredibly fast but breaks down every fifty miles.

```c
    void swap(int *a, int *b)
    {
      int t=*a; *a=*b; *b=t;
    }
    void sort(int arr[], int beg, int end)
    {
      if (end > beg + 1)
      {
        int piv = arr[beg], l = beg + 1, r = end;
        while (l < r)
        {
          if (arr[l] <= piv)
            l++;
          else
            swap(&arr[l], &arr[--r]);
        }
        swap(&arr[--l], &arr[beg]);
        sort(arr, beg, l);
        sort(arr, r, end);
      }
    }
```

From <http://alienryderflex.com/quicksort/>, originally from Wikipedia.

----

# Java

> Java is a family station wagon. It's easy to drive, it's not too fast, and you can't hurt yourself.

```java
    public class Quicksort {
        static <T extends Comparable<? super T>> void quicksort(T[] array) {
            quicksort(array, 0, array.length - 1);
        }
        static <T extends Comparable<? super T>> void quicksort(T[] array, int left0, int right0) {
            int left = left0; int right = right0 + 1; T pivot, temp; pivot = array[left0];
            do {
                do left++; while (left <= right0 && array[left].compareTo(pivot) < 0);
                do right--; while (array[right].compareTo(pivot) > 0);
                if (left < right) {
                    temp = array[left];
                    array[left] = array[right];
                    array[right] = temp;
                }
            }
            while (left <= right); 
            temp = array[left0];
            array[left0] = array[right]; array[right] = temp;
            if (left0 < right) quicksort(array, left0, right);
            if (left < right0) quicksort(array, left, right0);
        }
    }
```

Adapted from <http://literateprograms.org> and licensed under MIT/X11.

----
# Python

> Python is a great beginner's car; you can drive it without a license. Unless you want to drive really fast or on really treacherous terrain, you may never need another car.

```python
    def qsort(list):
        if list == []: 
            return []
        else:
            pivot = list[0]
            lesser = qsort([x for x in list[1:] if x < pivot])
            greater = qsort([x for x in list[1:] if x >= pivot])
            return lesser + [pivot] + greater
```

Adapted from <http://literateprograms.org> and licensed under MIT/X11.

----

# Lisp/Scheme

> At first it doesn't seem to be a car at all, but now and then you spot a few people driving it around. After a point you decide to learn more about it and you realize it's actually a car that can make more cars. You tell your friends, but they all laugh and say these cars look way too weird. You still keep one in your garage, hoping one day they will take over the streets.

```scheme
    (defun factorial (n)
       (if (<= n 1)
           1
           (* n (factorial (- n 1)))))

    -- one line
    (defun factorial (n) (if (<= n 1) 1 (* n (factorial (- n 1)))))
```

From <http://en.wikipedia.org/wiki/Lisp_(programming_language)>

----
# Haskell

> Haskell is an incredibly elegantly-designed and beautiful car, which is rumored to be able to drive over extremely strange terrain. The one time you tried to drive it, it didn't actually drive along the road; instead, it made copies of itself and the road, with each successive copy of the road having the car a little further along. It's supposed to be possible to drive it in a more conventional way, but you don't know enough math to figure out how.

```haskell
    quicksort :: Ord a => [a] -> [a]
    quicksort []     = []
    quicksort (p:xs) = (quicksort lesser) ++ [p] ++ (quicksort greater)
        where
            lesser  = filter (< p) xs
            greater = filter (>= p) xs
```

From <http://www.haskell.org/haskellwiki/Introduction>

----

# A question of power

- What does power mean?
- Can you say: assemmbly < C < Python < Haskell?

What about this?

```R
    plot(speed~dist, cars)
    abline(lm(speed~dist, cars))
```

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-06/img/rplot.png)

- Does power == conciseness?

# Presenter notes

 - conciseness illustrates power != power is conciseness (perl :)
 - no real difference in power (universal turing machine, i.e. you can compute anything computable with any computer, but it might be slower)
 - "In theory, there is no difference between theory and practice. But, in practice, there is." • Jan L. A. van de Snepscheut

----

# Declarative style

Math:

    0! = 1
    n! = n (n-1) (n-2) ... 2 * 1 
       = n (n-1)!

Python:

```python
    def factorial(n):
        if n == 1:
            return 1
        else:
            return factorial(n-1)

    def factorial(n):
        result = 1
        for i in range(n):
            result = result * i
        return result
```

Haskell:

```haskell
    factorial 0 = 1
    factorial n = n * factorial (n - 1)
```

----

# Syntax and Semantics

- What does syntax mean?
- What does semantics mean?
- What's funny about the above questions?

# Presenter notes

- Of course, what's funny is that I'm asking what semantics *means*, and semantics means "what does it mean?"

----

# Syntax and Semantics

- Could two programs compile to the same instructions?
- Could the same program compile to two different sets of instructions?

C:

```c
    c = a + b;
    if (c != 10) {
        do_something();
    }
```

Assembly:

```asm
    add eax, ebx;
    sub eax, 10;
    jnz 0x42;
```

Machine code:


    0011 0000 0001
    0100 0000 1010
    1001 0010 1010
     ^     |   | 
    opcode |   ^ operand
           ^ operand


----

# Syntax and Semantics Quiz:

> **This sentance is false.**

- Is the above syntactically correct?
- Is the above semantically correct?

> **I can haz cheezburger?**

- Is the above syntactically correct?
- Is the above semantically correct?

----

# Syntax and Semantics Answers

> **This sentance is false.**

- Is the above syntactically correct? *Yes, it's a valid English sentance.*
- Is the above semantically correct? *No, it just killed your inner robot.*

> **I can haz cheezburger?**

- Is the above syntactically correct? *Not valid English (but will be soon).*
- Is the above semantically correct? *Yes, did you understand it's meaning?*


----

# Syntax

```python
     File "/var/www/queue/views.py", line 636

       if method.lower() == 'GET'
                                ^
    SyntaxError: invalid syntax
```

- Syntax is the easy part :)
- It's *how* you say what you mean.
- We'll take a *meaningful* look at semantics next time.

----

# Abstract syntax tree (AST) for "english"

**Sentance:** Tom found a cat.

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-06/img/tom-found-a-cat.png)

    Sentance   ::= NounPhrase Verb [NounPhrase]
    NounPhrase ::= Noun | Article Noun
    Article    ::= 'an' | 'a' | 'the'
    Verb       ::= 'found' | 'rescued' | 'skinned'
    Noun       ::= 'Tom' | 'cat'

This is called a *grammar* and it's in *Backus Naur Form* or BNR. It describes a *language*. Remember last week?

----
# Aside: Remember last week?

Context free grammars and pushdown automata are equivalent. You can construct a pushdown automaton to recognize a language.

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-06/img/pushdown-automaton.png)

----

# Quiz: draw a tree for

**Sentance:** Tom gallantly rescued a tabby cat.

    Sentance   ::= NounPhrase Verb [NounPhrase]
    NounPhrase ::= Noun | Article Noun
    Article    ::= 'an' | 'a' | 'the'
    Verb       ::= 'found' | 'rescued' | 'skinned'
    Noun       ::= 'Tom' | 'cat'

----

# What are we missing?

**Sentance:** Tom gallantly rescued a tabby cat.

    Sentance   ::= NounPhrase VerbPhrase [NounPhrase]
    NounPhrase ::= Noun | Article Noun | Adjective Noun | Article Adjective Noun
    Article    ::= 'an' | 'a' | 'the'
    VerbPhrase ::= Verb | Adverb Verb
    Verb       ::= 'found' | 'rescued' | 'skinned'
    Adverb     ::= 'gallantly' | 'fearfully' | 'carefully'
    Noun       ::= 'Tom' | 'cat'
    Adjective  ::= 'tabby' | 'black' | 'calico'

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-06/img/tom-gallantly-rescued-a-tabby-cat.png)

----

# What makes this abstract?

    Sentance   ::= NounPhrase VerbPhrase [NounPhrase]
    NounPhrase ::= Noun | Article Noun | Adjective Noun | Article Adjective Noun
    Article    ::= 'an' | 'a' | 'the'
    VerbPhrase ::= Verb | Adverb Verb
    Verb       ::= 'found' | 'rescued' | 'skinned'
    Noun       ::= 'Tom' | 'cat'

- Notice there are no punctuation, just focus on the things that convey meaning
- Concrete syntax trees, or parse-trees, hold everything.
- Abstract syntax trees, hold the things that are *semantically meaningful*

----

# AST for a program

```c
    // Expression
    a + b

    // Statement (Assignment)
    c = a + b;

    // Program
    c = a + b;
    if (c > 10) {
        c = 0;
    }
```

AST: 

    StatementSequence ::= Statement StatementSequence | Statement
    Statement         ::= If | Assignment
    If                ::= Expression StatementSequence
    Assignment        ::= Variable Expression
    Expression        ::= Variable Operator Variable | Variable Operator Value
    Operator          ::= '>' | '<' | '==' | '+'
    Variable          ::= 'a' | 'b' | 'c' | 'd'
    Value             ::= '0' | '1' | '2' | '3'

----

# AST for a program

![](https://github.com/generalassembly/cs-for-hackers/raw/master/week-06/img/program.png)


----

# What can you do with an AST?

- Translation
- Transformation
- Out-of-order execution
- Stay tuned for next week!

----

# What's missing from the AST?

    StatementSequence ::= Statement StatementSequence | Statement
    Statement         ::= If | Assignment
    If                ::= Expression StatementSequence
    Assignment        ::= Variable Expression
    Expression        ::= Variable Operator Variable | Variable Operator Value
    Operator          ::= '>' | '<' | '==' | '+'
    Variable          ::= 'a' | 'b' | 'c' | 'd'
    Value             ::= '0' | '1' | '2' | '3'

- Details of syntax are missing
- Lexing can bridge the gap
- Lexers are *regular grammars*, equivalent to FSM or finite automata with input

----

# Next time

- Semantics 
- A gentle introduction to lambda calculus

Some of:

- Type theory
- Model checking
- Practical functional programming


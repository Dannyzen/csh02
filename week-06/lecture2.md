# CS For Hackers - Lecture 6.2
## Programming Languages (In Theory)

When we're done, you should understand:

- Semantics
- Basics of the &lambda; calculus
- Major ideas from functional programming

<style>
table {
border-collapse: collapse;
background: #efefef;
}
table tr th, table tr td {
border: 1px solid #CCC;
text-align: left;
margin: 0;
padding: 6px 13px;
}
table tr th {
font-weight: bold;
}
table tr:nth-child(2n) {
background-color: #F8F8F8;
}
</style>


----

# Review: Abstract Syntax Trees

**Sentance:** Tom found a cat.

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-06/img/tom-found-a-cat.png)

Grammar:

    Sentance   ::= NounPhrase Verb [NounPhrase]
    NounPhrase ::= Noun | Article Noun
    Article    ::= 'an' | 'a' | 'the'
    Verb       ::= 'found' | 'rescued' | 'skinned'
    Noun       ::= 'Tom' | 'cat'

----

# Syntax vs Semantics again

- Syntax is *how* we say something
- Semantics is what we *mean*
- What do people mean by the semantic web?
- Why are semantics useful?

# Presenter notes

- Useful semantics
  - Logic vs implementaion

----

# Levels of meaning

- Price of Persia assembly code vs. "Hit the enemy, he loses 1 health."
- "All models are wrong, but some are useful." *--George Box*

----

# Prefix / Infix / Postfix notation

<table>
<tr><th>Infix<th>Prefix (lisp)</th><th>Postfix (reverse polish)</th>
<tr><td>1 + 1</td><td>+ 1 1</td><td>1 1 +</td>
<tr><td>2 * x - 1</td><td>- (* 2 x) 1</td><td>1 (2 x *) -</td>
<tr><td>(5 * 10) - 8</td><td>- (* 5 10) 8</td><td>(5 10 *) 8 -</td>
</table>

- What's a grammar for the above?
- Why would you want to use these?

#  Presenter notes

- What notation is function application?

----
# Try some of your own

- x + 10
- x<sup>2</sup> + 1
- sin(x)
- x<sup>3</sup>

Reference:

<table>
<tr><th>Infix<th>Prefix (lisp)</th><th>Postfix (reverse polish)</th>
<tr><td>1 + 1</td><td>+ 1 1</td><td>1 1 +</td>
<tr><td>2 * x - 1</td><td>- (* 2 x) 1</td><td>1 (2 x *) -</td>
<tr><td>(5 * 10) - 8</td><td>- (* 5 10) 8</td><td>(5 10 *) 8 -</td>
</table>

----

# What is a function?

- A *rule* that maps input to output

```
    function(a, b) { return a & b; }
```

<table>
<tr><th>a<th>b</th><th>a&b</th>
<tr><td>0</td><td>0</td><td>0</td></tr>
<tr><td>0</td><td>1</td><td>0</td></tr>
<tr><td>1</td><td>0</td><td>0</td></tr>
<tr><td>1</td><td>1</td><td>1</td></tr>
</table>


```
    function(x) { return x * x; }
```

<table>
<tr><th>x<th>x<sup>2</sup> = f(x) = x * x</th><th>notes</th>
<tr><td>0</td><td>0</td><td>f(x) = x</td></tr>
<tr><td>1</td><td>1</td><td>f(x) = x</td></tr>
<tr><td>2</td><td>4</td></td><td></tr>
<tr><td>3</td><td>9</td></td><td></tr>
<tr><td>...</td><td>...</td></tr>
</table>

----

# Functions


    // c, java
    int f(int x) { return x+1; }

    // javascript
    f = function(x) { return x+1; }

    # R
    f = function(x) { x+1 } 

    # Python
    def f(x):
        return x+1

    f = lambda x: x+1
 
    # Ruby
    def f (x)
        x+1
    end

    f = lambda { |x| x+1 }


Lambda calculus:

> &lambda;x.+ 1 x


----

# &lambda;-calculus notation for functions

- Nothing to do with the dx/dy sort of calculus
- Invented by Alonzo Church, influenced John McCarthy
- Nothing more than a notation
- Good for expressing / manipulating *arbitrary* functions

Grammar: <span style="font-family: courier">A ::=  c | v | A A | &lambda;v.A</span>

(c constant or operator like 1, 2, &pi;, +, *, sin; v variable like x, y, x)

<table>
<tr><th>JavaScript<th>Math</th><th>&lambda;-calculus</th>
<tr><td>function(x) {return x+1}</td><td>f(x) = x+1</td><td>&lambda;x.+ x 1</td></tr>
<tr><td>function(x) {return Math.sin(x)}</td><td>f(x) = sin(x)</td><td>&lambda;x.sin x</td></tr>
<tr><td>function(x) {return Math.sin(x+2)}</td><td>f(x) = sin(x+2)</td><td>(&lambda;x.sin x)(&lambda;x.+ x 2)</td></tr>
<tr><td>function(x) {return Math.pow(x,2)}</td><td>f(x) = x<sup>2</sup></td><td>&lambda;x.* x x</td></tr>
</table>

- What are the syntx trees for these?

----

# Quiz: write these in &lambda; notation

- x + 5
- 2x
- 4x<sup>2</sup> - 1
- cos(x/&pi;)

Reference:

<table>
<tr><th>JavaScript<th>Math</th><th>&lambda;-calculus</th>
<tr><td>function(x) {return x+1}</td><td>f(x) = x+1</td><td>&lambda;x.+ x 1</td></tr>
<tr><td>function(x) {return Math.sin(x)}</td><td>f(x) = sin(x)</td><td>&lambda;x.sin x</td></tr>
<tr><td>function(x) {return Math.sin(x+2)}</td><td>f(x) = sin(x+2)</td><td>(&lambda;x.sin x)(&lambda;x.+ x 2)</td></tr>
<tr><td>function(x) {return Math.pow(x,2)}</td><td>f(x) = x<sup>2</sup></td><td>&lambda;x.* x x</td></tr>
</table>

----

# Performing computation: &beta;-reductions

&beta;-reduction is a fancy term for substituting the argument for the variable:

- (&lambda;x.sin x) &pi;/2 &rarr; sin &pi;/2

This captures the idea of a computaitonal step.

Try reducing these:

- (&lambda;xy.* x y) 7 6
- (&lambda;y.y &pi;/2) (&lambda;x.sin x)
- (&lambda;x.x x) (&lambda;x.x x)

----

# Pure functions

- self = &lambda;x.x
- first (argument) = &lambda;xy.x
- second (argument) = &lambda;xy.y
- (apply) once = (&lambda;fx. f x)
- (apply) twice = (&lambda;fx. f (f x))

We could use *twice* to make x<sup>3</sup>:

- times_x = &lambda;x.* x
- twice times_x = (&lambda;fx. f (f x)) (&lambda;x. * x) &rarr; &lambda;x. * x ( * x x)

----

# Simulating loops

- &lambda;cxy. if c x y
- &lambda;nxy. if (==0 n) x y

Looper:

- I = &lambda;nfx.if (==0 n) x (I (- n 1) f (f x))
- L = &lambda;F.(&lambda;nfx.if (==0 n) x (F (- n 1) f (f x))

To find the end of the loop, we'd want to go until this is true:

- I = L I

This actually exists:

- Y M = M (Y M)
- Y = (&lambda;xy. y (x x y)) (&lambda;xy. y (x x y))

You can use this to implement recursion in languages that don't have it.

----

# Three awesome ideas from functional programming

----

# Higher order functions (HOF)

- ycombinator
- animation callbacks
- python decorators
- map reduce / split apply combine

----

# HOF: Decorators in python

You could use it to 

```python
	@login_required
	def my_view(request):
	    return HttpResponse('hello world')
```

Or to cache results of a function:

```python
	def memoize(function):
		cache = {}
		def decorated(*args):
			if args not in cache:
				cache[args] = function(*args)
			return cache[args]
		return decorated

	@memoize
	def factorial(n):
		if n == 1: return 1
		return n * factorial(n - 1)
```

Adapted from <http://askawizard.blogspot.com/2008/09/decorators-python-saga-part-2_28.html>

----

# HOF: Split / apply / combine

Python:

```python
    sum(int(line.split()[0]) for line in file('access.log'))
```

R:

```c
    sapply(c(1, 2, 3, 4, 5, 6, 7, 8), function(x) { x^2 })
```

Hadoop streaming (python):

```python
    # map.py
    for line in sys.stdin:
        for word in line.split():
            print word, 1

    # reduce.py
    word, total = '', 0
    for line in sys.stdin:
        new_word, count = line.split()
        total += int(count)
        if word and word != new_word:
            print word, total
            total = 0
```


----

# Pattern matching

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

# Immutability

----

# Resources

- Wikipedia is kinda thick: 
    - http://en.wikipedia.org/wiki/Lambda_calculus
- A Short introduction to the Lambda Calculus, Achim Jung
    - <http://www.cs.bham.ac.uk/~axj/pub/papers/lambda-calculus.pdf>




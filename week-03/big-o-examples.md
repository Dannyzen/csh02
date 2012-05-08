# Big-O notation examples

These examples are inspired by Rob Bell's [blog post](http://rob-bell.net/2009/06/a-beginners-guide-to-big-o-notation/).

If you find bugs in these examples, or want to add some in your favorite language, please let me know via pull request. :)

# O(1)

Also called "constant time", this means the running time does not depend on the
size of the data input at all.

```python
    # Python
    def first_letter(book_as_string):
        return book_as_string[1]
```

```ruby
    # Ruby
    def first_letter book_as_string
      book_as_string[0,1]
    end
```

```javascript
    // JavaScript
    var first_letter = function(book_as_string) { return book_as_string[0]; }
```

Our book here could be 10 characters of 100,000 characters. Returning the first 
one will always take the same amount of time.

# O(n)

If you touch each peice of data once, your algorithm is `O(n)`, or "linear". You might
see this type of algorithm in data processing or in creating a *linear search*:

```python
    # Python
    def first_index_of(target, list_of_strings):
        for i, s in enumerate(list_of_strings):
            if s == target:
                return i
```

```javascript
    // JavaScript
    var first_index_of = function(target, list_of_strings) {
        var n = list_of_strings.length;
        for (var i=0; i<n; i++) {
            if (list_of_strings[i] === target) {
                return i;
            }
        }
    }
```

Note that this algorithm exits early if it finds the target. This would probably
give it an average running time of `n/2` or `0.5 * n`, but using Big-O notation, 
we drop constants and focus on what is happening to `n`.

# O(n^2)

If, for each peice of data, you touch *every other* peice of data, you are doing
`n` iterations for each of `n` items, for a total of `n*n` or `n^2` iterations.

```python
    # Python
    def find_duplicates(list_of_strings):
        duplicates = []
        for a in list_of_strings:
            for b in list_of_strings:
                if a == b:
                    duplicates.append(a)
        return duplicates

    # Better version
    def find_duplicates(list_of_strings):
        return dict((a,None) 
            for i,a in enumerate(list_of_strings) 
            for j,b in enumerate(list_of_strings) 
            if a==b and i!=j).keys()
```

```javascript
    // JavaScript
    var find_duplicates = function(list_of_strings) {
        var duplicates = [];
        var n = list_of_strings.length;
        for (var i=0; i<n; i++) {
            for (var j=0; j<n; j++) {
                if (list_of_strings[i] === list_of_strings[j]) {
                    duplicates.push(list_of_strings[i]);
                }
            }
        }
        return duplicates;
    }
```

The tip-off here is nested for loops. Two forloops over the same variable means
`O(n^2)`. Three would mean `O(n^3)`.

    
# O(log(n))

Log is often tricky for programmers to understand intuitively. Think of it this
way: `log_2` is the opposite of an exponent like `2^n`. `2^n` doubles for each `n`, but
log "undoes" this (`log_2(2^n) == n`).

**Note:** in CS, we almost always mean the base-2 logorithm (`log_2`), but programming
libraries often supply a base-10 `log` function. 

Any algorithm that involves traveling directly to the leaf of a tree from its
root (like binary search) is going to have `log(n)` performance. Specifically,
it is going to have `log_b(n)` performance where `b` is the number of branches
the tree has at each node. This is not magical or surprising, because
`log_b(n)` is simply the height of a tree with `n` total nodes and `b` branches
allowed at each node.

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-03/binary_tree.png)

Notice if you flatten this tree, it becomes a sorted list. :)

I remember Tim Bray had a great perspective on `O(log(n))` algorithms in his
[Beautiful Code](http://shop.oreilly.com/product/9780596510046.do) chapter "Finding Things":

> On a 32-bit computer, the biggest log_2 you'll ever encounter is 32
> (simillarly 64 on a 64-bit computer), and any algorithm that completes in an
> upper bound of a few dozen steps will be "good enough" for many real-world
> scenarios.

This example roughly follows the diagram above, and has `O(log(n))`
time complexity:

```python
    # Python
    def binary_search(target, sorted_array):
        middle = len(sorted_array)/2
        pivot = sorted_array[middle]
        if target > pivot:
            return middle + 1 + binary_search(target, sorted_array[middle+1:])
        elif target < pivot:
            return binary_search(target, sorted_array[:middle])
        elif target == pivot:
            return middle
        else:
            raise IndexError("Target not found!")
```
            

# Visualizing performance

([R source code](http://opani.com/ryan/big-o/edit/) and [results](http://opani.com/ryan/big-o/833214592954/results/) available on Opani)

A good way to compare the various complexities is to graph the running time based
on the input size. Even starting with something small like `n=10`, it is pretty
ovbvious how things will turn out:

![](http://opani.com/ryan/big-o/833214592954/_figures/10_fig.795361665.png)

Of course, smaller is better here, since we are graphing running time. `O(1)` (pink) is 
a clear winner, but you can see `O(log(n))` (purple), `O(n)` (blue) and even `O(n*log(n))`
(aqua) are not doing too badly, all under 30 operations for a data size of 10. On the
other hand, `O(n^2)` (green), `O(2^n)` (salmon) and `O(n^3)` (barf brown) are already not
looking so hot as they point to the sky.

Let's ramp `n` up up to 100:

![](http://opani.com/ryan/big-o/833214592954/_figures/100_fig.240384150.png)

Everything separates further, Note how `O(n*log(n))` (the complexity of the best
sorting algorithms) is not looking quite as good anymore. Although ~500 operations
for 100 pices of data is not too bad, it's slowly getting worse as `n` increases.
Keep an eye on `O(2^n)`. It is sneakily approaching `O(n^3)` (the complexity of
naïve matrix multiplication).

Ramping up another 10x to 1,000:

![](http://opani.com/ryan/big-o/833214592954/_figures/1000_fig.477226108.png)

The trend continues. `O(2^n)` has clearly passed `O(n^3)`, and `O(log(n))` almost looks
as fast as `O(1)`. Never underestimate the power of a log!

I hope this gives you a better feel for the complexities.



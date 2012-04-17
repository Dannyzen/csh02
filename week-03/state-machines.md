# State Machines

Other names:

- FSA (Finite State Automaton, not Flying Spaghetti Alien)
- Finite State Machine

The gist: You can only be in one state at a time, and you have to obey the arrows.

Remember this?

----

## Second Chance Machine

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-01/img/state-machine.png"
style="max-height: 90%; max-width: 80%;">

- This is called a state machine.
- How would you implement it in code?

----

## A simpler example

Get a valid input from the user:

            /--- correct ----> DONE
      .-> INPUT
      |     \
      |  incorrect
      \-----/

```python
    correct = False # state
    while not correct:
        print 'what is your name? >',
        value = raw_input()
        if value == 'ryan':
            correct = True
     
```

We can call `DONE` an *accepting state*.


# Even number of 1s machine

<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-03/even-1-acceptor.png"
style="max-height: 90%; max-width: 80%;">

How would you implement this?


```python
    def acceptor(ones_and_zeroes_string):
        even = True # state
        for digit in ones_and_zeroes_string:
            if even:
                if digit == '1':
                    even = False
                if digit == '0':
                    even = True
            else:
                if digit == '1':
                    even = True
                if digit == '0':
                    even = False
        return even

    def acceptor2(ones_and_zeroes_string):
        even = True # state
        for digit in ones_and_zeroes_string:
            if digit == '1':
                even = not even
        return even
```

# Tom finder


<img src="https://github.com/generalassembly/cs-for-hackers/raw/master/week-03/tom-acceptor.png"
style="max-height: 90%; max-width: 80%;">

How would you implement this?

```python
    def contains_tom(might_contain_tom):
        state = 'start'
        for letter in might_contain_tom:
            if state == 'start':
                if letter == 'T':
                    state = 'T'
            elif state == 'T':
                if letter == 'O':
                    state = 'O'
                else:
                    state = 'start'
            elif state == 'O':
                if letter == 'M':
                    return True
                else:
                    state = 'start'
        return False
```

# CS For Hackers - Lecture 8.1
## Concurrency and the failure of Moore's Law

When we're done, you should understand:

- Moore's Law and its implications
- Why concurrency is hard (what the principle difficulty is)
- The Dining Philosophers' problem
- The basics of concurrency in code
- What contention means

----

# How chips are made

![](http://farm6.staticflickr.com/5147/5592961146_36bfa6ace1_b.jpg)

Photo: [Travis Goodspeed](http://www.flickr.com/photos/travisgoodspeed/5592961146/)

----

# How chips are made: Lithography

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/lithography.png)

## Presenter notes

- Light source is typically a UV eximer laser
- Photomask is typically quartz, late model mask fab costs 200-500 million

----

# Moore's Law

- Lithography scaled really well!
- The number of transistors on a chip doubles every 18-24 months!
- Related: computers become twice as fast every two years or so!
- This used to mean you could wait a few years and your program would be faster

Gordon Moore senses something is fishy with his law (photo: [Steve Jurveston](http://www.flickr.com/photos/jurvetson/368370/))

<img src="http://farm1.staticflickr.com/1/368370_e1c2ec1891_z.jpg?zz=1"
style="max-height: 50%; max-width: 80%;"
/>

----

# Moore's Law data

<img src="http://www.gotw.ca/images/CPU.png"
style="max-height: 80%; max-width: 80%;"
/>

From [The Free Lunch Is Over](http://www.gotw.ca/publications/concurrency-ddj.htm), courtesy of Herb Sutter

----

# What's going wrong?

Physics:

- Quantum current leakage (power problem)
- Transistor performance (gate width can't get any smaller)
- Bus speeds and memory technology

Moore's 2nd Law (Moore didn't actually say this, but everybody observses):

- The cost of building a chip fab will double every 18-24 months.
- ~ half a billion dollars right now

----

# What is the solution?

- More transistors
- Better pipelines (do more at once)
- More cores!
- The software guys will figure it out...

----

# Scheduling

We talked a bit about this in Week 2:

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/shedule.png)

- What happens when Task 1 switches to Task 2?
- What happens when Task 2 switches cores?

## Presenter Notes

- What costs do you pay?
- Cache locality violated.

----

# Context switching

- Have to save the computing environment of the tasks
- All program state should be the same (including registers) when it comes back
- This can be expensive if you have many tasks

Some processors include instructions to do this, but mostly now done in software:

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/str.png)

----

# Why is concurrency hard?

- Why do you think?
- Let's play a game!
- We need 3 volunteers.

## Presenter Notes

- Coordinating shared resources

----

# Dining Philosophers

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/table.png)

----

# Try this algorithm:

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/states.png)

```!c
    thinking:
        think()
        wait_for_left_chopstick()
        pickup_left_chopstick()
        wait_for_right_chopstick()
        pickup_right_chopstick()
    eating:
        eat()
        put_down_left_chopstick()
        put_down_right_chopstick()
        goto thinking
```

Is this algorithm correct?

## Presenter Notes

- Volunteers: Everybody think. Now, pick up your left chopstick.
- What happened?

----

# Problem: Deadlock

- Volunteers: Everybody think. Now, pick up your left chopstick.
- What happened?
- Come up with a definition of deadlock.
- How would you suggest fixing it?

## Presenter notes

- See if they use a waiter, an ordering, or some kind of locking

----

# Solution: Waiter

- Waiter tells you when both chopsticks are available (same as taking a lock on the chopstick pair)
- There are several solutions involving waiters

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/waiter.png)

----

# Problem: Livelock (Starvation)

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/colors.png)

- Using above algorithm
- Whenever Blue is about to finish thinking, Red or Green aquire the chopstick they share with Blue
- How would a waiter solve this problem?

----

# Stopsigns

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/stopsign.png)

- What does the law say to do in this situation?
- What would you do?

## Presenter notes

- Note that we often use differences in age, gender, social status to break ties at the dinner table, at doors, when running into another person in the hallway, street.
- All of the above examples are examples of using ordering to mediate the usage of a shared resource.

----

# Those clever philosophers...

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/ordered.png)

They decided to number their chopsticks!

```!c
    thinking:
        think()
        wait_for_low_chopstick()
        pickup_low_chopstick()
        wait_for_high_chopstick()
        pickup_high_chopstick()
    eating:
        eat()
        put_down_high_chopstick()
        put_down_low_chopstick()
        goto thinking
```


## Presenter Notes

- Volunteers: everybody pick up your low chopstick
- Deadlock problems in general are caused by a cycle, and can be resolved by breaking the cycle
- Above is useful in distributed systems

----

# Concurrency in practice

----

# Shared memory concurrency

Multiple processes violate a pretty strong assumptions that most programs make:

- A variable, when set, doesn't change its value during your code block

----

# Concurrency example

C provides `fork()`:

- Creates a copy of the current process
- Shares memory with the current process

Consider this program:

```!c
    int i = 0;
    pid = fork();
    if (pid == 0) {        // this is the child process
        while(i != 100) {
           i = i + 1;
        }
    } else {               // this is the parent process
        while(i != 100) {
           i = i + 1;
        }
    }
```

- At what lines could this program be interrupted? Why?
- What could go wrong here? How would you fix it?

----

# Solution: use a waiter

```!c
    int i = 0;
    pthread_mutex_t mutex;
    pthread_mutex_init(&mutex, NULL);

    pid = fork();

    while (true) {
       pthread_mutex_lock(&mutex);
       if (i == 100) break;
       i = i + 1;
       pthread_mutex_unlock(&mutex);
    }

    pthread_mutex_destroy(&mutex);
```

- The section between the lock and unlock is called a 'critical section'
- While the lock is held (lines 8-11), the other process will wait for unlock before entering critical section

----

# Contention

----

# Locking primitives

How do you ensure that something like `pthread_mutex_lock(&mutex);` works correctly if it is made out of lines of code?

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/xchg.png)

----

# Next time

- Producers consumer
- The C10k problem
- Thread based webservers
- Event loop based webservers

----

# Fin



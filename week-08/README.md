### Week 8/9 - Concurrency and the failure of Moore's Law

What happened to Moore's Law? How do we keep making performance progress in the
face of core limits? Topics: shared memory models, threads, locking, actor
models, the c10k problem, asynchronous IO, vector clocks, consensus algorithms,
etc.

Instructor: [Ryan Witt](http://twitter.com/onecreativenerd), [Opani](http://opani.com/)

Location: GeneralAssemb.ly

## Lectures

1. [Lecture 1](https://github.com/ryanwitt/csh/blob/master/week-08/lecture1.md): 
1. [Lecture 2](https://github.com/ryanwitt/csh/blob/master/week-08/lecture2.md):
1. [Lecture 2](https://github.com/ryanwitt/csh/blob/master/week-08/lecture3.md):

## Hacking challenge

Summary: Setup a simple, "hello world" page using a threaded webserver (apache using MPM prefork is a great choice). Also setup a select() style server that uses a single thread (lighttpd or nginx are great for this). What is the best performance you can get out of each? 

Measure performance in terms of number of clients that can connect at once, and total number of requests that can be served in a second. You can use the program "ab" (apachebench) to do the testing.

**Answer these questions:**

1. What is the best performance you can get out of each type of server?
2. What do you think is causing the difference between the two? What evidence would you point to to convince your colleagues of this?

Try to get the above done in less than 4 focused hours.

**Bonus question:**

Write threaded (web.py, rails, django, etc.) and select() based webservers (node.js, twisted, event machine, etc.) that return natural numbers, 1, 2, 3, 4, etc., adding 1 for each request. Which version is faster? Which one was easier to implement? What would you do to increase your performance by 10x?


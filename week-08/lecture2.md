# CS For Hackers - Lecture 8.2
## More locks, concurrency and the C10k problem

When we're done, you should understand:

- Locking strategies
- Producer consumer problem
- The C10k problem
- Thread based webservers
- Event loop based webservers

----

# Review: Locking shared resources

So, I lied a little on Monday. The truth looks something like this:

```c
    #include <stdio.h>
    #include <stdlib.h>
    #include <pthread.h>
    int i = 0; pthread_mutex_t mutex;
    void* f(void* ptr) {
        while (true) {
           pthread_mutex_lock(&mutex);
           if (i == 100) break;
           i = i + 1;
           pthread_mutex_unlock(&mutex);
        }
    }
    int main(void) {
        pthread_t t1, t2; pthread_mutex_init(&mutex, NULL);
        pthread_create(&t1, NULL, f, NULL); pthread_create(&t2, NULL, f, NULL);
        pthread_join(t2, NULL); pthread_join(t2, NULL);
        pthread_mutex_destroy(&mutex);
    }
```

- How many *processes* do we have here?
- Is this program correct? (Does it do anything unexpected?) - Is it useful?

----

# A more useful program

```c
    int i = 0;
    ...

    void* f(void* ptr) {
        int j;
        while (true) {
           pthread_mutex_lock(&mutex);
           if (i == 100) break;
           i = i + 1;
           j = i;
           pthread_mutex_unlock(&mutex);
           do_lots_of_work(j);
        }
    }
```

- We're doing work for each index, so we spend less time waiting for the lock

----

# Read and Write locks

- Many readers allowed, only one writer.
- Writer will block unti last reader is gone.
- File system support in POSIX for this.

----

# Producers and consumers

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/producer-consumer.png)

- Also called the bounded buffer problem.
- This can be used to implement message passing.
- What problems would come up when implementing this simple diagram?
- Hint: what do the people represent, and how do they access the data structure?

----

# Lock-free strategies

- Atomic integers
- Immutability (Example: timestamps instead of update)
- Transactions
- STM

----

# The C10k problem

> It's time for web servers to handle ten thousand clients simultaneously, don't you think? After all, the web is a big place now. --Dan Kegel

Dan's argument:

- Machine: 1GHz clock, 2 GB ram, 1Gb Ethernet
- Divide by 20k clients, and each client gets:
- 50k clock cycles, 100KB ram, 50kbps (6.25 KB/s) of bandwidth.

So why could you only handle a hundered or so?

----

# What webservers look like
# (from the inside)

----

# A Reminder: Scheduling

Keep this in your head as we go forward.

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/shedule.png)


----

# A simple server

We'll look at example programs in c (higher level languages boil down to these calls anyway):

```c
    int backlog = 10;
    int server_socket = socket(...);

    bind(server_socket, ...);
    listen(server_socket, backlog);

    while (1) {
        int client_socket = accept(server_socket, ...); // block until connect
        recv(client_socket, ...);                       // block until data
        create_response(&response);                     // block until finished
        send(client_socket, response, ...);             // block until sent
        close(client_socket);
    }
```

- What's wrong with this approach?

## Presenter notes

- Can only handle 1 connection at a time.
- This is especially bad waiting for IO.

----

# This server is totally forked

```c
    int backlog = 10;
    int server_socket = socket(...);

    bind(server_socket, ...);
    listen(server_socket, backlog);

    while (1) {
        int client_socket = accept(server_socket, ...); // block until connect
        if (fork() == 0) {
            recv(client_socket, ...);                   // block until data
            create_response(&response);                 // block until finished
            send(client_socket, response, ...);         // block until sent
            close(client_socket);
            exit(0);
        }
    }
```

- What's better about this server?
- What's still expensive?

## Presenter notes

- New thread for each request, means many can happen at one time.


----

# Pre-forking

```c
    int backlog = 100;
    int server_socket = socket(...);

    bind(server_socket, ...);
    listen(server_socket, backlog);

    for (int i=0; i < 100; i++)
        if (fork() == 0) break; // child breaks out of loop

    while (1) {
        int client_socket = accept(server_socket, ...); // block until connect
        recv(client_socket, ...);                       // block until data
        create_response(&response);                     // block until finished
        send(client_socket, response, ...);             // block until sent
        close(client_socket);
    }
```

- What's better about this?
- Are there any negatives?
- How could we improve?
- In practice, both pre-forking and on-demand are used (apache)

## Presenter Notes

- Process overhead is cut down.
- Could use threads. lighter weight.
- Context switching becomes an issue.
- Could improve by read() in loop.

----

# Apache config example

```c
    # prefork MPM
    # StartServers: number of server processes to start
    # MinSpareServers: minimum number of server processes which are kept spare
    # MaxSpareServers: maximum number of server processes which are kept spare
    # MaxClients: maximum number of server processes allowed to start
    # MaxRequestsPerChild: maximum number of requests a server process serves
    <IfModule mpm_prefork_module>
        StartServers        2
        MinSpareServers     3
        MaxSpareServers     5
        ServerLimit         16
        MaxClients          16
        MaxRequestsPerChild   0
    </IfModule>

    # worker MPM (threads, not processes)
    <IfModule mpm_worker_module>
        StartServers          2
        MinSpareThreads      25
        MaxSpareThreads      55
        ThreadLimit          64
        ThreadsPerChild      25
        MaxClients          150
        MaxRequestsPerChild   0
    </IfModule>
```

----

# `select()` to the rescue!

```c
    int backlog = 100;
    int num_sockets = 1;
    fd_set sockets; // keep track of sockets
    int server_socket = socket(...);

    bind(server_socket, ...);
    listen(server_socket, backlog);

    while (1) {
        select(..., &sockets, ...); // wake me up when there's data

        for (int i=0, i<num_sockets; i++)
            ... // find active_socket

        if (active_socket == server_socket) {
            client_socket = accept(server_socket, ...); // returns immediately
            FD_SET(client_socket, &sockets);
            num_sockets++;
        } else {
            client_socket = active_socket;
            recv(client_socket, ...);               // returns immediately
            create_response(&response);             // block until finished
            send(client_socket, response, ...);     // block until sent
            close(client_socket);
        }
    }
```

----

# libevent

- select() is nice, but it goes linearly through available connections
- kernel *could* do this on interrupts, that would be awesome!
- epoll() (linux) and kqueue() (freebsd) do just that!
- nginx, lighty, ATS, node, twisted, tornado, eventmachine, use this approach.

![](https://github.com/generalassembly-studio/cs-for-hackers/raw/master/week-08/img/libevent-benchmark2.jpg)

----

# Hacking challenge

Summary: Setup a simple, "hello world" page using a threaded webserver (examples could include apache, web.py, rails, django, or some java framework). Also setup a select() style server that uses a single thread (you could use lighttpd, nginx, nodejs, ATS, etc.). What is the best performance you can get out of each? 

Measure performance in terms of number of clients that can connect at once, and total number of requests that can be served in a second. You can use the program "ab" (apachebench) to do the testing.

**Answer these questions:**

1. What is the best performance you can get out of each type of server?
2. What do you think is causing the difference between the two? What evidence would you point to to convince your colleagues of this?

Try to get the above done in less than 4 focused hours.

**Bonus question:**

Write threaded and select() based webservers that return natural numbers, 1, 2, 3, 4, etc., adding 1 for each request. Which version is faster? Which one was easier to implement? What would you do to increase your performance by 10x?

----

# References

- Dan Kegel's original rant: <http://www.kegel.com/c10k.html>
- Beej's guide to network programming: <http://beej.us/guide/bgnet/>

----

# Next time: Distributed systems

As much as we can cover of:

- Fallicies you will fall victim to when writing distributed systems
- The CAP theorem
- Lamport and timelines (vector clocks)
- Distributed Hash Tables (Chord algorithm)

----

# Fin


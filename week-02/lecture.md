
# Operating Systems

## what is an OS?

* OS as machine abstraction
* OS as a set of services

---
# What an OS Does

## services to a user
* manages resources
* multiprocessing
* process isolation
* security

---
# What an OS Does

## services to a programmer
* manages resources
* cpu (kinda) - multiprocessing
* file interface - everything is a file
* memory - virtual memory & paging


---
# OS Services - CPU

## process: the running state of a program

* instructions
* call stack
    * parameters
    * return address
    * local variables
* memory (heap)
* file descriptors
* process attributes (permissions, owner, etc)
* context (register state, address space, etc)

# Presenter Notes
* draw a process stack

---
# OS Services - CPU

## creating a process

* fork & exec
* always parent/child

---
# OS Services - CPU

## threads: lightweight processes
* shared resources (address space, file descriptors, etc)
* faster context switch

---
# OS Services - CPU

## context switching: how the OS switches between processes
1. one process is interrupted by the hardware
1. execution switches to kernel scheduler
1. saves the "context" (register state) of a process
1. restores context of another process

---
# OS Services - CPU

## scheduling: how the OS decides which process to switch to
* managing finite resources
    * blocking on CPU or IO
* goals:
    * throughput - amount computed in some time
    * turnaround time - time from request to finish
    * response time - time from request to start
    * predicatble/fair - spend time on all jobs
    * utilizing cpu & io - keep them busy

---
# OS Services - CPU

## scheduling: how the OS decides which process to switch to
* process states
    * running - currently executing on the CPU
    * waiting - ready to run
    * blocked - waiting for IO
* job queues & scheduling algorithms
    * fifo
    * round robin
    * priority queue
        - nice value

# Presenter Notes
* draw a few

---
# The Kernel

* privilege / rings
    * to protect device access
    * to protect memory access
* system calls
    * ask the kernel to do priviledged things for you
    * there is no kernel process
    * escalating privilege
    * interrupt to switch
    * %cpu: user, sys, idle
* ex: read()

---
# File Interface

## everything is a file
* block devices vs. character devices
* memory mapped IO - fake memory
* DMA - direct Device Access to memory

---
# File Interface

## access patterns
* blocking
* async
* non-blocking
* memory mapped files

# Presenter Notes
* show code!

---
# OS Services - Memory

## simple direct memory model (physical addressing)

---
# OS Services - Memory

## process isolation through virtual memory
* separate address spaces (same virtual, different physical)
* shared memory to un-isolate
* speed up via MMU (Memory Management Unit) & TLB (Translation Lookaside Buffers)
* segmentation fault

---
# OS Services - Memory

## virtually unlimited through virtual memory & paging
* page tables
* page faults (hardware interrupts by MMU)
* page replacement algorithms - to decide what to page out
    * dirty pages
    * Not Recenty Used
    * FIFO
    * Second-Chance - hybrid of last two
    * Least Recently Used - expensive, have to check all
    * Aging - shifting usage bitfield

# Presenter Notes
* draw virtual table mapping
* draw algorithms


---
# Role in Architecture & Development

* using or avoiding disk
  * disable swap?
  * sync/async i/o
  * ram fs

---
# Role in Architecture & Development

* network usage
  * limitations on connections
  * connection overhead
  * throughput
  * sync/async i/o

---
# Role in Architecture & Development

* memory layout
  * paging or not
  * fragmentation (tie-in to data structures later)

---
# Role in Operational Problems & Scaling

* disk
  * file descriptor limits
  * i/o slows down (virtual env)
  * disk fails
  * too much usage

---
# Role in Operational Problems & Scaling

* network
  * tuning tcp options
  * tracking connections
  * monitoring data being transferred
  * slow network or limit of bandwidth?


Parallel Programming : Is It Still Hard?
=========================================

# First speaker: Michael Wong

Things are bad:
- data races
- sharing
- deadlocks

Law of exponential growth is done for transistors, but not for transistors.

Before: you used to add new hardware and it has kept improving
Now: it's actually a software problem.

Old technology was actually simpler, even if it seems different than what we
now have.

Before, things were considered "for experts" and now since they're more
convenient, you have a bunch more features. "Half of this stuff is just
bloatware, [...] and it happens to computers."

Examples:
- spreadsheets offer computer programming features to non-programmers.
- slides used to need a team of experts

=> The same thing is now happening to parallel programming.

## Parallel programming

Things seem hard, but it's because we've been made to think serially. In order
to think parallel, you do have to go to the reality of it.

Although everyone is seemingly able to do multi-tasking, we still perceive that
parallel programming is difficult.

"It's not parallel programming that's difficult, it's programming that's
difficult."

We now have multi-cores, therefore it's becoming common to be able to do these
kind of things. Today, any GitHub project may have some parallel programming
included in the repo.

If you believe parallel programming is difficult, you should have some of an
idea of the problems (see "bad things").

## Why is parallel programming perceived to be so difficult?

Easy examples of parallel systems:
- SQL
- Shell pipes

You need a way to hide the parallelism very well.

3 goals of design of parallel systems:
1. performance
2. generality/portability
3. productivity

"The iron triangle of parallel programming language nirvana."

What about: correctness, maintainability, robustness?
=> They're not specific to parallel programming. Except for correctness, but
   whose costs conflict with productivity.

### Performance

No longer hardware but actually software (see Moore's law).

Don't necessarily use parallel programming to optimize, only use it if you can:
- really take advantage of multi-core
- scale linearly easily

Otherwise use a sequential optimization beforehand.

### Productivity

Has only increased over time, and is now to the point that it's better than
performance.

Now, general purpose languages try to get the best of all worlds, and are
therefore too general to give good performance on all points.
=> You must make trade-offs to get it.

> With C++14/17 there are now high-level abstractions that are productive.

Examples: Python is a high-productivity language, but you lose the possibility
to tune performance. Java gives great portability and productivity, but at the
cost of performance.

### Generality

Usually, generality is traded with performance or productivity.

# What makes it hard?

- work partitioning: splitting work between threads
- parallel access control: control sharing of data between threads
- resource partitioning and replication: data has to be moved around data nodes
  (NUMA, CPU, GPU, FPGA, pages, cache lines, instances of synchronization
  primitives, or critical section of code)
- interacting with hardware: direct access can be required to squeeze the last
  drop of performance

> THESE ARE IMPORTANT

# Second speaker: Paul McKenny

"Premature abstraction is the root of all evil!!!"

Issues with hardware optimization:
- branch mispredictions
- memory references
- RMW atomic instructions: compare-and-swap
- memory barriers (aka fences)
- memory ordering not created equal: relaxed < consume < acquire / release / acq_rel < seq_cst
- cache miss
- I/O: worst of them all, by orders of magnitude


### Loopholes in the laws of physics

- Immutable data is replicated in all caches, which is great because you can
  parallelize very easily.

In the past, they had a thing called UNIX, which had you compile in the kernel
what all your hardware was. It was therefore pretty easy to get kernel panics
because if you tried to use something that hadn't been compiled in the kernel,
you got one.

Nowadays, it's no longer a problem. You have a bunch of programs that track
what hardware you have.

## Overview

Things hardware do to optimize:
- big caches
- cache prefetching
- store buffers
- speculative execution
- big cachelines


> False sharing can be mitigated by alignment directives.

Store buffers are a source of misordering issues, since temporary values can
make CPUs disagree on what needs to be done.

Memory models in C++11 follow Java's unified model to do things like memory
ordering.

Big caches are pretty great, they have little issues. With thenm  you get
higher throughput & less latency.

Speculative execution can hide latencies and memory misordering, but you have
higher worst-case latency.

Locks are great but cause horrible high-contention behavior, especially with
"dead simple locking".

Lock handoff is still sub-optimal because you can have multiple CPUs spinning
on the same location. Queued locks are great for having at most two CPUs
fighting with a same lock. An even better solution would be to maintain low
levels of contention (ex: select() ).

RW Locking: what's not to like? Well, readers need to write to memory,
therefore you can get cache misses on multiple cores (bad for perf...).

TLS (thread local storage) RW Lock: all readers have a lock, writers must
acquire all locks. No cache misses for writers (although atomic instructions).


# Third speaker: Maged Michael - single producer, single consumer buffer

    template <typename T>
    class SPSC {
      bool tryEnq(T* ptr);
      T* tryDeq();
    };

A single mutex for enqueue / dequeue means that you get cache line bouncing, or
at least you'll have to use slow atomics.

## Lamport (1983)

- Lots of loads & stores.
+ Parallelism, fast instructions.

Caveat of atomic loads/stores: fast only if good cache locality. Problem:
there's actually a lot of cache line bouncing.

## Giacomoni et al. (PPoPP 2008)

Similar implementation than Lamport, but consumer & producer deal with
different pointers, therefore there is no cache line bouncing. There is also
less false sharing because of good alignment.

## Blocking consumer

- condition variable: condition notification
- futex: system call
- selective futex: atomic instruction

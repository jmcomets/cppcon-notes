Multi-threading is the answer, what is the question? (part 2)
=============================================================

# How to review multithreaded code

Shared resource example: phone booth

In order to use the phone, you must be holding the handle of the phone booth.
> Door Handle = mutex

- mutex is a random variable name
- review notes on multi-threaded code inherits from single-threaded code
- watch out for writers using reader locks (eg. operator[] on map)

# A better way

Higher level abstractions are better than raw mutexes:

- guarded
- shared_guarded
- ordered_guarded
- deferred_guarded
- lr_guarded

Thread-aware containers:

- rcu_guarded
- rcu_list

RCU: read, copy update

- used internally in Linux
- established algorithm for multi-threaded linked lists

Writers:

- only one writer at a time (blocking)

Readers:

- multiple concurrent readers
- readers are lockless
- do not block writers

Algorithm is hard to implement in raw C++ because "later" is hard to define (no
preemptive synchronisation). Extensive use of atomics helps in implementing this.

Check out:

- library with the above guards: libguarded?
- `std::shared_timed_mutex` / `std::shared_lock`

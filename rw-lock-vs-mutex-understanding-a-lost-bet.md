Reader-Writer Lock vs Mutex, understanding a lost bet
=====================================================

Speaker: Jeff M. (Bloomberg)

### Definitions

- mutex: single (exclusive) ownership, `std::mutex`
- reader-writer lock: singe writer or multiple reader ownership, expect higher concurrency
  when primarily reading, read / write preference, `std::shared_mutex`

### Mutex: all we need?

- reader-writer locks must have additional overhead
- hold times for a lock should be small
- overhead > concurrency

### Shall we bet?

- initially shown mutex > reader-writer lock for a map-like component
- investigated reader-writer locks currently available at Bloomberg
- strongly believed-out-loud overhead is too much, we can do better
- intern developed new (awesome) lock
- struck a bet...and lost

Benchmark process:

1. take next work item
2. work is either read / write
3. lock appropriately
4. busy-loop, duration determined by "load"
5. unlock appropriately

Benchmark process:

- shutdown after specified run duration
- measure actual time used
- repeat a bunch of times
- report 80th percentile of total count of work done per second

[Benchmark source](https://github.com/bloomberg/rwl-bench).

### Cost of one atomic

Atomic lock:

- (un)lock does `fetch_add` with `memory_order_acq_rel`
- (un)lock shared does `load` with `memory_order_acquire`

Null lock:

- (unlock) does nothing
- (unlock) does nothing

## Design criteria: RW-Lock

- fairness: do threads acquire lock in the order of arrival
- CPU utilization while a thread is blocked on the lock (spinlocks)
- handling of priority inversion
- having real usage scenarios

### Losing implementation

- minimal overhead
- writer preferring
- `unlock` prefers to provide access to readers
- use a primitive to select the next writer

### Winning implementation

- allow lock reacquisition
- no explicit preference for readers or writers
- use a primitive to select the next reader or writer

## Recap

- 1 time mutex significantly outperformed winning
- 1 time mutex slightly outperformed winning
- 8 times mutex tied winning
- 10 times mutex significantly underperformed winning

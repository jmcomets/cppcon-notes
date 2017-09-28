An -Overly- Not so Complicated Lock-free Queue (Part 2 of N)
============================================================

Speaker: Tony Van Eerd

Guide to threaded coding:

1. stop sharing (if possible)
2. use locks otherwise
3. measure
4. measure
5. change your algorithm
6. goto 1

*(at infinity)* Lock-free
*(at infinity + 1)* Measure

*Lock-free coding is the last thing you want to do.*

TL;DR "Don't share, use locks"

1st rule of lock-free coding, don't use lock-free coding.

unrelated: __MACROS ARE EVIL__

CAS: compare & swap (= `compare_exchange`)

# MPMC Queue: review

> comptomisation: a compromise for optimization

1 queue with multiple producers/consumers = bottleneck. Use N queues if
possible.

false_sharing: If head/tail are atomic, if they're side-by-side you'll get a
lot of bouncing, therefore add some spacing between them.

head-ish/tail-ish: head & tail are a bit before the actual one, when
searching, the first non-data is the real one

Add a "generation" number to differentiate the head/tail ends (marks non-data
for head/tail).

# MPMC Queue: new path

Add generation number along with the data.

- head: First non-zero data (of correct generation).
- tail: First zero data (of correct generation).

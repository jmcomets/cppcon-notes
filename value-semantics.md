Value Semantics in C++17
========================

# Value vs Object, Semantics

- value is abstract, does not have identity; example: 42
- objects are concrete and have identity; example: `int x = 42;`

Value semantics is model in which we operate and think in terms of values. It's
also an approach to manage objects, using: deep copy semantics & automatic
lifetimes - RAII.

Benefits: closer to mathematical notation, referential transparency (deep-copy
=> local reasoning), no manual memory management issues.

# optional

## use-case: return values

Magic values:

A *valid* value of type T that is used to indicate the *absence* of a value of
type T.

Examples:
- error code (fork / kill)
- parse failure: exception used for control flow, pointers mean no stack,
  `pair<T, bool>` means that T needs to be default constructible, bool and T&
  out parameter adds an out parameter (booooh)

## use-case: function parameter

(?)

## use-case: struct/class member

## pitfall

Relational operators are perfectly OK.

## differences with boost

- forwarding construction
- conditional explicit

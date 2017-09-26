folly::Function: a non-copyable alternative to std::function
============================================================

# std::function

- needs copyable objects
  => use `std::shared_ptr<T>` - extra memory allocation, increment / decrement atomic refcount
  => "move wrapper" - copy constructor moves the wrapped value, hence breaking
     copy semantics (eg. auto_ptr)
- const-correctness is off, invokes the wrapped object as a non-const reference
  => does not enforce const-correctness

# folly::Function

- 64 bytes (larger than `std::function`)
  - function pointer
  - pointer to management function
  - 48 bytes to store wrapped objects inline
- types that are not noexcept movable are never stored inline

## non-copyable

- must be able to store non-copyable callables
- no run-time performance regression
- maintain value semantics
- must be non-copyable

=> cannot be wrapped in a `std::function`

## noexcept-movable

Non-copyable types need to be noexcept movable: usage with `std::vector` and
other STL containers which need movable types to be noexcept to be able to
really take advantage of move semantics.

## const-correct

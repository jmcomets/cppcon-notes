Practical C++17
===============

Speaker: Jason Turner

ChaiScript:

- embedded scription language
- VS, GCC, Clang
- Windows, Linux, MacOS, FreeBSD, Haiku
- Header only - no external deps
- all types are the same and directly shared between script and C++
- 25k lines of C++
- C++03 + Boost -> C++11 => C++14 => C++17
- complex template usage for automatic reduction of function calls

# Features

## Structured Bindings

    auto [a,b,c] = <expression>;

Automatically split a structure into multiple variables.


## if-init expressions

    if (auto [key, value} = *map.begin(); key == "mekey") {}

Also works for switch conditions

## emplace_back

    // before
    container.emplace_back(42);
    auto& val = container.back();


    // after
    auto& val = container.emplace_back();

## string_view

Light-weight wrapper over `std::string`, automatically convertible to/from
`std::string` and `const char*`.

## nested namespaces

    namespace a::b {}

## CTTD: class template type deduction

    std::pair p{1, 2.3}; // std::pair<int, double>

Also applies for return types & aggregate construction.

## class template deduction guides

    std::decay<T>

Turing complete?

## if constexpr

Compile-time conditional block

    if constexpr(/* constant expression */) {
      // ...
    } else {
      // ...
    }

## fold expressions

- Allows folding variadic parameter packs into a single value
- Allowed on all operators
- Can be on left/right or binary (= middle)

## noexcept is now part of the type system

Esoteric?

noexcept fps can be converted to non-noexcept, but not the other way around.

#  Which feature had the most impact (on ChaiScript)?

- compile time
- runtime
- code size
- readability

## impact of noexcept

- Caused a 2x duplication of function type deduction code
- Required conditional compilation???

Typed support for noexcept => 1% performance boost

- (+) performance
- (-) maintainability
- (~) compile time
- (~) readability

## impact of if-init

- Useful, not much impact though
- Helps when in non-modular code
- Formatting sucks

- (~) performance
- (+) maintainability
- (~) compile time
- (-) readability

## impact of nested namespaces

- quite satisfying
- large whitespace diffs
- can cause large diffs again if you need to insert a nested namespace

- (~) performance
- (-) maintainability
- (~) compile time
- (+) readability

## impact of CTTD

No impact. Initializer lists aren't often used, nothing to deduce in most
cases.

`auto` is way better than CTTD.

- (-) performance
- (~) maintainability
- (~) compile time
- (+) readability

## impact of structured bindings

- Helps clean up code
- Return value optimization (NRVO) cannot apply to a sub object

- (-) performance
- (-) maintainability
- (~) compile time
- (+) readability

## impact of fold expressions

Almost everything you can do with fold expressions is possible with initializer
lists.

- (~) performance
- (-) maintainability
- (~) compile time
- (-) readability

## impact of emplace_back

- (~) performance
- (-) maintainability
- (~) compile time
- (~) readability

## impact of string_view

- need to avoid temporaries
- conversions are practical
- don't use if you don't need a string view, use owned strings if you want some

=> need to check `is_transparent` (provided by `std::less<>`)

- (-/+) performance
- (~) maintainability
- (~) compile time
- (~) readability

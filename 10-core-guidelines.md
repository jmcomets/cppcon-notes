10 Core Guidelines You Should Start Using Today
===============================================

Speaker: Kate Gregory

GSL: Guidelines Support Library

# Bikeshedding

## C.45: don't define a default constructor that only initializes data members, use in-class member initializers instead.

Example: 3 different constructors with different values for members => use
in-class member initializers.

Benefits:

- no arguing about equivalent
- may prevent bugs
- may increase performance

## F.51: prefer default argument values to overloads.

Benefits:

- no arguing
- will not forget to make same change to both copies
- difference between two versions are clear

# Don't run with scissors

## C.46: define and initialize members in order of definition.

Benefits:

- protects you from an oddity of the language
- might encourage you to rethink your class design so this dependency goes away

## I.23: keep the number of function arguments low (4 is the limit).

Example: `area(x1, y1, x2, y2)` => `area(p1, p2)`

Benefits:

- lower cognitive burden and recall effort
- introduce abstractions that may be useful later
- perhaps save ripple changes

# That Old Thing? Nobody Goes There Anymore.

## ES.50: Don't cast away `const`.

Example: cache pattern works only if there are more reads than writes, when
there are more writes than reads you *could* cast away `const` and process
writes when the first reader comes along.

Could also use a pointer to the cache, hence the pointer is `const` and not the
pointed value.

Better solution: use `mutable` to keep the header in-line with what's really
happening.

Benefits:

- code accessing mutable members is shorter and more readable
- easier to write, read, & maintain
- `const` correctness may enable optimizations

## I.11: Never transfer ownership by a raw pointer `T*`.

Alternatives:

1. Return by value
2. Take by non-const reference and change it (if it's an in-out parameter)
3. Use a smart pointer
4. Use `owner<T>` from GSL

Implementation of `owner<T>`:

    template <typename T, typename = std::enable_if<std::is_pointer<T>>>
    using owner = T

Benefits:

- memory management is *hard*
- things can be automated
- can leave a marker to do it

# All the Cool Kids are Using It

## F.21: To return multiple out values, prefer returning a tuple or a struct.

1 use your own struct
2. `std::optional`: if the two things are an object and a `bool` about whether
   or not the thing is usable, consider using optional
     > casting to `bool` is easy
     > `value_or` helps for defaulting to a value
3. tuple, tie, structured bindings otherwise

Benefits:

- readability: all the returns are together
  > not mixed between one special return value and the rest as out parameters
- when parameters are passed by pointer or non-const ref, readers can assume
  they are in-out parameters

## Enum classes

Old-style enums can now be used to fully qualify the enumeration!

- names can overlap
  > you can have an OK in every enum if you want
- helpful conversions to `int` do not happen
- can use a backing type other than `int` if you want

# You Are Not The First Developer With This Problem

## I.12: use not_null for pointers that must not be null.

`gsl::non_null<T*>`

Benefits:

- avoid dereferencing `nullptr` errors
- improve performance by avoiding redundant checks for `nullptr`
- express intent to readers

## ES.46: avoid lossy (narrowing, truncating) arithmetic conversions.

- use like `static_cast<>`
- name carries information
- `narrow_cast<>` means I don't mind losing data
- `narrow<>` throws error on losing data (the cast changed the value)

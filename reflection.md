Compile-Time Support For Reflection
===================================

Speaker: Andrew Sutton

[Old repository](https://github.com/asutton/clang/)

[Up to date repository](https://github.com/asutton/clang-reflect)

After working with H. Sutter on metaclass proposal, implement reflection &
meta-programming.

Goal: support better network programming tools

- single specification (all in C++)
- generate safer, possibly more efficient decoders
- generate useful encoders (fuzzing?)

## What is reflection?

### Concepts and operators

Set of language features that allows a program to examine its own structure.

- Supports the use of source code as data in an algorithm
- A set of features that allows introspection of code

Reflection is a set of operators that translate between two domains:

- A set of constructs: entities, specifiers, etc., written in a program
- A set of reflections: values that describe the constructs in a program

A reflection operators returns a unique reflection for a construct

A set of action operators that do things with reflections:
- dynamic method invocation
- dynamic method generation (JIT?)

### Meta-programming strategy

1. Get a reflection of some entity
2. Analyze the reflection
3. Perform actions related to the entity

Example: `to_string` for enums

1. Get the reflection of the enumeration type
2. Get the reflections of each enumerator
3. Search the list for an enumerator whose value is equal to the argument
4. Return the declared name of that enumerator

## Dynamic vs Static

### Dynamic reflection

A lot of language support it.

- Algorithms and modifications are executed at runtime
- Not a zero-cost abstraction
- Incurs binary size overheads
- Not easy, if at all possible, to opt out

Why would I want this?

- dynamic method lookup & invocation
- runtime code generation
- questionable things like self-modification
- no support for compile-time computation
- typically, poor support for generic programming

Current state in C++:

- great support for both compile-time computation & generic programming
- type traits allow querying and computing of types
- concepts allow constraining & querying of types
- no built-in support for enumerating the members of a class

### Why reflection?

- To get comprehensive support for compile-time meta-programming
- Write less boilerplate
- More to come?

### Static reflection

- set of operators on two domains: constructs and reflections
- constructs are defined by the language

Operators:

- maps source code entities to values
- takes a named entity (a declaration), returns an encoded value of that entity
- encoded value is typically AST-like
- supports queries of properties (name, specifiers, type, value)

Projections:

- type projection produces a type specifier
- namespace projection produces a nested name specifier (eg. `std::`)
- value projection produces a reference to a typed entity
- name projection produces an identifier for subsequent lookup

Encoding reflections:

- as types: reflections values are represented by OR encoded in types
- as objects: reflection values are objects with the same type

### Type-based reflection

    using meta_int = $reflexpr(int);

The definition of the class is unspecified.

- Models a concept that determines what properties are available
- Properties encoded as static members of the class

Projection:

    using T = typename get_type_t<$reflexpr(int)>; // T is int

    enum E { A, B, C };
    auto x = get_constant_v<$reflexpr(A)>; // x == A

Simply access members included in the unspecified class.

Other operators proposed for non-type, non-value projections (`$unreflexpr`).

TMP required:

- expressing computations with types
- few compiler intrinsics
- poor compile-time performance: potentially disastrous memory usage

## Type-encoded reflection

*Stop using templates to express computation!*

The reflection object yields a `prvalue` whose type encodes a handle to a
construct. Syntactically:

- `$` is primary-expression
- operand is an id-expression, type-id, or namespace-name

The type of `$` is a specialization of a class template in the `meta::`
namespace.

Which template depends on the entity reflected:

- id-expression => function, variable, etc...
- reflected type-id is `class_type`, `enum_type`
- reflected namespace-name is `ns`

### Encoded reflections

- Encode a handle to the compiler's internal representation of the construct
- Need a one-to-one mapping between values of `reflection_t` and constructs
- Could be a laundered pointer to an AST node (it is)

### Projection intrinsics

Uses compiler internals:

- `__reflect_name`
- `__reflect_type`

### Value projection

Library intrinsics: when instantiated, constructs an expression that yields the
value.

- `__reflect_pointer`
- `__reflect_value`

### Reflecting lists

Lists of class members are (~) tuples. Elements of the tuple can have different
types (obviously).

- `size()` and `get()` are *lazy*, values are computed only when used
- often filtered by a wrapping tuple

Iterating: __NOT EASY__

Requires some more advanced features / C++ knowledge (`boost::hana`).

=> New language feature: loop expansion `for...`

Loop expansion allows:

- tuples
- arrays
- de-structurable classes
- parameter packs (WIP)

Other projection operators:

- `typename(r)`: get the type specifier
- `namespace(r)`: get the semantic scope
- `idexpr(r)`: get an unqualified-id

Name construction:

- `idexpr($f, "_fn_", 3) // makes the id f_fn_3`
- closer to code generation

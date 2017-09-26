Mocking Frameworks Considered Harmful
=====================================

DOC: dependent other component (= decouple!)
SUT: system under test

- mocking is often used for stateful APIs
- verify usage of DOC by SUT
- check call sequence from Order (SUT)

Unit-test phases:

1. setup
2. exercise
3. verify
4. teardown

In an ideal world, SUT has no dependencies.

Legacy code => too many dependencies hence hard to test, therefore components
need to be replaced somehow.

Hard examples:

- network failures
- hardware / kernel failures
- database connection
- random number generator
- user interface
- not yet implemented components

Test double: parameterize SUT with a component (DI)

Mocks allow verifying that the SUT uses the other component correctly. Mocking
is about verification. Mock objects are configurable test doubles. Mocking
frameworks allow easy configuration of doubles, therefore tendency to overuse.

Mocking framework:

- dependency injection: replace DOC by test double = "introduce seam" by M. Feathers
- fake DOC's function results for SUT
- trace calls and arguments of SUT on DOC's Test Double
- match sequence of calls and argument values made on DOC from SUT

C++ Mocking frameworks:

- GMock: old?
- Trompeloeil: easy to use?
- Mockator: pitched framework

Seams:

- object: introduce interface (virtual with inheritance)
  > pass DOC/Test Double as a (constructor) argument by reference
- compile: extract template parameter
  > make DOC default template argument, pass Test Double in tests
- linker, preprocessor: last-resort, or for C-functions

Issues with mocking frameworks:

- GMock `ON_CALL` shows a warning when it shouldn't be an issue
- checks are done at destruction of mock objects
- specific DSL for behavior - "cheat sheets"

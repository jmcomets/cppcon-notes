Writing Effective Documentation for C++ Libraries
=================================================

- write a short sentence describing the overall use of the library
- understandable to enumerate everything provided, better to summarize the
  contents (functions, classes, macros => facilities)

**Examples:**

- boost::align: clear, but should be shorter
- boost::any: opaque description, too generic
- boost::array: too much yada-yada
- boost::asio: much better

### RingView

**Old:**

> This proposal introduces a ring to the standard library operating on a span,
> named `ring_span`. The `ring_span` offers similar facilities to `std::queue`
> with the additional feature of storing the elements in contiguous memory and
> being of a fixed size.

**New:**

> A `ring_view` is an adaptor providing first in / first out (FIFO) access to
> existing storage.

## Tips

- separate public API & implementation documentation
- write documentation for a specific person (eg. user / maintainer)

**Suggestions for contents:**

- introduction / purpose
- motivating examples with explanation
- notes
- rationale
- reference
- concepts
- types
- functions
- meta-functions

See C++ Standard, section [17.5 [description]][].

[17.5 [description]]: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4296.pdf

Tools don't create the document, you do!

### Doxygen

Good: includes reference documentation in code comments.
Bad: no obvious way to include concept pages.
Ugly: including narrative, examples, is a lot of work.

Generating alternative output formats requires more work.

### DocBook / BoostBook

Good: decouples content from format - can generate html, pdf and others from
the same source.
Bad: open source tools for processing / editing XML are hard to use.
Ugly: XML is very verbose and a pain to edit.
QuickBook is a favorite editor used by Boost Library Authors to address this
last issue.

### XMLMind

- WYSIWYG editor for BoostBook / DocBook files
- Enforces BoostBook / DocBook syntax rules - Easy to use
- Free copy available for personal use

See Boost Library Incubator for introduction.

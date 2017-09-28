
Problems with polymorphism:

- incurs heap allocation
- tends towards smart (or not so smart) pointers
- doesn't play well with algorithms
- intrusive

See [talk by Sean Parent](https://youtu.be/QGcVCgEVMJg).

What do we want?

=> Types should satisfy interfaces, not derive base classes (~duck-typing).

# How can we have an `interface` keyword?

Goal: independent storage and dynamic method dispatch.

- storage policy
- vtable policy

### Inheritance

Member -> Type -> VTable

### Remote storage

- Type -> VTable
- Pointer to the concrete data


Gist of the implementation:

    class Interface {
      VTable const* const vtable;
      void* ptr;

      public:
        void method() {
          vtable->method(ptr);
        }
    };

    struct VTable {
      void (*method)(void* this_);
    };

    template <typename T>
    VTable const VTableFor = {
      [](void* this_) {
        static_cast<T*>(this_)->method();
      }
    };

*Rest of the talk is a presentation of the library*.

[Link to dyno repository](https://github.com/ldionne/dyno)

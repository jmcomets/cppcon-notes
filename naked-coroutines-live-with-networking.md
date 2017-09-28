Naked Coroutines -- Live with Networking
========================================

## Design Principles

- scalable to billions of concurrent coroutines
- efficient: resume / suspend operations comparable in cost to a function call
  overhead
- seamless interaction with existing facilities *with no overhead*
- open ended coroutine machinery allowing library designers to develop
  coroutine libraries extensions (generators, tasks, queues)
- usable in environments where exceptions are forbidden

*Coroutines are a generalization of a subroutine.* - Donald Knuth (1986)

**In C++20 Networking TS:**

- udp + tcp: endpoint / socket / acceptor / resolver
- `executors`
- `timers`
- `io_context`

Async sources go into an `io_context`, which postpones where the work is done.

**In C++20 Coroutines TS:**

Useful:

- `co_yield`
- `co_await`
- `co_return`

Not so useful?

- `suspend_never`
- `suspend_always`
- `coroutine_handle`
- `coroutine_traits`

Not so much, hence NAKED.

#### Hello World

    #include <experimental/coroutine>
    #include <stdio.h>
    #include <future>

    template <typename R, typename... Args>
    struct std::experimental::coroutine_traits<std::future<R>, Args...> {
        struct promise_type {
            std::promise<R> p;

            suspend_never initial_suspend() { return {}; }
            suspend_never final_suspend() { return {}; }

            // should be more generic
            void return_value(int v) {
                p.set_value(v);
            }

            auto get_return_object() { return p.get_future(); }

            void unhandle_exception() { p.set_exception(std::current_exception()); }
        };
    };

    using namespace std::experimental;

    std::future<int> f() {
        puts("Hello");
        co_return 42;
    }

    int main() {
        printf("%d\n", f().get());
    }

**Final thoughts:**

- `std::future` has significative overhead
- raw awaitables are much faster

## More information

- [Networking TS Implementation](https://github.com/chriskohlhoff/networking-ts-impl)
- [Another C++ coroutine library](https://github.com/lewissbaker/cppcoro)
- [Some examples](https://github.com/GorNishanov/await/tree/master/2017_CppCon)
- [An example on GodBolt](https://godbolt.org/g/26viuZ)

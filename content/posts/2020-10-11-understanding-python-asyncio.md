---
title: Understanding Asyncio in Python
date: 2020-10-11
slug: understanding-asyncio
---

In this post, I will dive deep into asyncio related topics in python. This is mainly a learning note for me around this topic.

<!--more-->

To implement an asyncio like library, we need the following tools:
1. We need to have a scheduler that is responsible for looping through all available tasks. Run tasks that are ready, and if a certain task is blocked, pause it and execute another task.
2. A way to represent state for a task that can be run asynchronously so that we can pause the execution when it is blocked and continue when it is ready.


Will first take a look at event loop, then we will look at how generator stores state of a subroutine and how we can pause/continue a subroutine. Finally, I will show you a simple asyncio sleep implementation to give you a taste of asyncio library.


## Event Loop

Event loop is simply a while loop looping through all jobs and schedule which job to execute next. 

```python
class EventLoop:
    def __init__(self):
        self._ready = deque()

    def add(self, task):
        # How to represent task?
        self._ready.append(task)

    def run(self):
        while self._ready:
            task = self._ready.popleft()
            # How to execute task??
```

High level structure is simple, however, you might wonder how do we represent a task? How do we pause a task? How do we continue the task?

## Generators

The answer is generators! Let's use one simple example to get a refresher on how generator works. Here, we created a very simple generator `gen` that returns 1 for the first iteration and returns 2 for the second iteration. As you may notice, generator's interface looks very similar to iterators (object that defines `__iter__`, `__next__`), call `next()` to get the next element and triggers `StopIteration` exception if there is no more item. However, implementation of generator is quite different than a stateful iterator implementation given generator can use yield to pause program execution and continue when we call `next()` on it.


```python
def gen():
    yield 1
    yield 2

if __name__ == '__main__':
    g = gen()
    print(next(g))  # 1
    print(next(g))  # 2
```


`dis.disco` is an easy way to look at python generated instructions. Python stores generator's code, last instruction, variables in a [frame object](https://docs.python.org/2/library/inspect.html). When we first create a generator object `g = gen()`, last instruction is at -1. After `next` is called, run code til `YIELD_VALUE` which yields control to others, last instruction pointer will point to `YIELD_VALUE`. If we call `next` another time, continue right after last `YIELD_VALUE` and run till next `YIELD_VALUE`.

```
>>> g = gen()
>>> dis.disco(g.gi_code, g.gi_frame.f_lasti)
  2           0 LOAD_CONST               1 (1)
              2 YIELD_VALUE
              4 POP_TOP

  3           6 LOAD_CONST               2 (2)
              8 YIELD_VALUE
             10 POP_TOP
             12 LOAD_CONST               0 (None)
             14 RETURN_VALUE
```

After we understand the basics of generator, let's take a look at `send()` which enables us sending value into generators and continue generators. As an example, we define a function that takes an input and then output the square of the input.

```python
def square():
    x = yield
    while True:
        x = yield x ** 2

if __name__ == '__main__':
    generator = square()
    generator.send(None)  # Run til first yield
    print(generator.send(1))  # Returns 1 ** 2 = 1
    print(generator.send(2))  # Returns 2 ** 2 = 4
    print(generator.send(3))  # Returns 3 ** 2 = 9
```

With `yield` and `send`, we now have a way for generators to 1) `yield` control and pause the execution.
2) `send` values into generators after I/O is done.

### Coroutine

When people talks about asyncio, you might often hear about coroutine. Below we have a `coro` based on generators for your reference.

```python
def helper():
    yield 1

@asyncio.coroutine
def coro():
    yield from helper()
```

With the introduction of `async` / `await`, we have new a new way to declare coroutine.

```python
async def helper():
    return 1

async def coro():
    return await helper()
```

## An Example

With generators, async / await, and event loops, now we can combine all these to implement a simple asyncio like library with non-blocking sleep ability!

Assume we have a `count_down` function that counts down from `n` and reports one number every second. Assume we have another `count_up` function that counts from `0` till `n` every 3 seconds. To do it in an async fashion, essentially we need a way to do non-blocking sleep.

Main idea for the non-blocking sleep is as follows:
- Initially all coroutines are in `self._ready` queue.
- When `sleep` is called, move the coroutine to `self._sleeping` heapqueu ordered by deadline.
- If all coroutines need sleep, get the coroutine with earliest deadline. Do a blocking sleep `time.sleep` till the deadline and move the coroutine to `self._ready` loop.

Full code here:

<script src="https://gist.github.com/wwei10/5e6d43444c75341224e575034390a753.js"></script>


To enable more asyncio functionalities (handle network I/O, file I/O), the process would be pretty similar, we will create non-blocking version of calls and yield control to scheduler when coroutine is blocked, schedule will poll each I/O source to see if network has new I/O, file has new I/O and schedule coroutines accordingly.

## Conclusions

Generator like coroutines + async / await API + event loops = asyncio-like library. Hopefully, through this article, you learn more about asyncio in python! One thing to note is we are just scratching the surface here and we only covered how to implement a simple non-blocking sleep function. To implement async networking I/O, file I/O would be more challenging. One tricky situation I ran into was that customly built asyncio library had some bugs handling RPC calls. Whenever an exception happens in RPC calls, the library failed to move the task from waiting queue to ready queue so the event loop would hang there forever causing big latency spike.


-------

## Reference

[How the heck does async/await work in Python 3.5?](https://snarky.ca/how-the-heck-does-async-await-work-in-python-3-5/), [Build your own async](https://www.youtube.com/watch?v=Y4Gt3Xjd7G8) are extremely helpful content on this topic. If you have time, those two materials are definitely worth a read.
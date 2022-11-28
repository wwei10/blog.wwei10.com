---
title: Understanding Asyncio in Python (Part 2)
date: 2022-11-23
slug: understanding-asyncio-2
---

It has been a long time since we dived into [asyncio](https://blog.wwei10.com/posts/understanding-asyncio/). This part 2 mainly talks about some tips & caveats when using `asyncio.create_task` function.

# A Simple Example

Imagine we have a code snippet like this:

```python
async def a():
     print('a() starts heavy compute')
     time.sleep(1)
     print('a() ends heavy compute')
     print('a() starts heavy I/O')
     await asyncio.sleep(5)
     print('a() ends heavy I/O')
     return random.randrange(10)


 async def b(x):
     print('b() starts heavy compute')
     time.sleep(5)
     print('b() ends heavy compute')
     print('b() starts heavy I/O')
     await asyncio.sleep(5)
     print('b() ends heavy I/O')
     return 10 * x
     
     
  async def main1():
     t1 = time.time()
     ret = await a()
     await b(ret)
     t2 = time.time()
     print(f'Elapsed time: {t2 - t1}')
     
     
 if __name__ == '__main__':
     asyncio.run(main1())
```

NOTE: `asyncio.sleep` is non-blocking while `time.sleep` is blocking.This whole program will take roughly 16 seconds to finish. `a()` has some I/O happening in the critical path. One way to optimize is by running it in parallel with other I/O or compute. One idea is kicking off `a()` in the background instead of a blocking call. The following code snippet implements such behavior. `asyncio.create_task` will schedule a task in the event loop but won't execute it immediately.

```python
 async def b2(task):
     print('b() starts heavy compute')
     time.sleep(5)
     print('b() ends heavy compute')
     print('b() starts heavy I/O')
     await asyncio.sleep(5)
     print('b() ends heavy I/O')
     ret = await task  # Key difference is here.
     return 10 * ret


 async def main2():
     t1 = time.time()
     awaitable = asyncio.create_task(a())
     ret = await b2(awaitable)
     t2 = time.time()
     print(f'Elapsed time: {t2 - t1}')


 if __name__ == '__main__':
     asyncio.run(main2())
```

After doing this, the whole program takes 11 seconds. `a()`'s I/O and `b()`'s I/O will get executed in parallel.

As we can see from this example:

1. It is important to optimize any I/O that blocks the execution of critical path.
2. `asyncio.create_task` is a neat way to achieve concurrency and save latency without refactoring tons of code to leverage util function `asyncio.gather`.

## A More Complex Example

However, there are cases where `asyncio.create_task` may slow things down:
1. `asyncio`'s scheduler does cooperative scheduling not preemptive scheduling, if a coroutine keeps the cpu busy, it will not yield cpu resources to other callers causing starvation to the other callers. To do preemptive scheduling, (round robin) threads are necessary and it more heavy weight than coroutine (check part1, coroutine is essentially a simple generator.)
2. Different combinations of I/Os and compute will benefit from different ways of usages of `asyncio`. To achieve best result, A/B test is necessary. 


A more detailed example highlighting why using `asyncio.create_task` to get rid of I/O on the critical path may not work as expected.

```python
t = None

 async def rpc():
     global t
     if not t:
         # First time calling RPC, triggers prefetch
         t = time.time()
         print('first time calling rpc at', t)
         return random.randrange(10)
     else:
         # Second time calling RPC, if time gap is larger than 5 seconds,        prefetch already sets the result into cache, returns immediately.
         # Otherwise, second call needs to wait for the first call to finish.
         # (First call needs 10 seconds to set data into cache)
         time_gap = time.time() - t
         print('time gap', time_gap)
         if time_gap < 10:
             await asyncio.sleep(20 - time_gap)
         return random.randrange(10)
         
         
     print('b() starts heavy compute')
     time.sleep(5)
     print('b() ends heavy compute')
     print('b() starts heavy I/O')
     await asyncio.sleep(5)
     print('b() ends heavy I/O')
     return 10 * x


 async def b2(task):
     print('b() starts heavy compute')
     time.sleep(5)
     print('b() ends heavy compute')
     print('b() starts heavy I/O')
     await asyncio.sleep(5)
     print('b() ends heavy I/O')
     x = await task
     return 10 * x
     
 async def c():
     print('c() starts RPC call')
     ret = await rpc()
     print('c() ends RPC call')
     return ret


 async def main():
      t1 = time.time()
      ret = await a()
      await b(ret)
      await c()
      t2 = time.time()
      print(f'Elapsed time: {t2 - t1}')
      
      
  async def main2():
      t1 = time.time()
      task = asyncio.create_task(a())
      await b2(task)
      await c()
      t2 = time.time()
      print(f'Elapsed time: {t2 - t1}')   
 
```


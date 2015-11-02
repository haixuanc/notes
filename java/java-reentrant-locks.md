## Intrinsic Locks vs Reentrant Locks

Prior to the **reentrant locks**, **synchronized** and **volatile** were the means for achieving concurrency.

Synchronized uses intrinsic locks or monitors. Every object in Java has an intrinsic lock associated with it. Whenever a thread tries to access a synchronized block or method, it acquires the intrinsic lock or the monitor on that object. In case of static methods, the thread acquires the lock over the class object. An intrinsic locking mechanism is a clean approach in terms of writing code, and is pretty good for most of the use-cases.

An intrinsic locking mechanism can have some functional limitations, such as:

1. It is not possible to interrupt a thread waiting to acquire a lock (lock Interruptibly).
2. It is not possible to attempt to acquire a lock without being willing to wait for it forever (try lock).
3. Cannot implement non-block-structured locking disciplines, as intrinsic locks must be released in the same block in which they are acquired.

Aside from that,
- ReentrantLock supports lock polling
- and interruptible lock waits that support time-out
- ReentrantLock also has support for configurable fairness policy, allowing more flexible thread scheduling

Lets see a few of the methods implemented by ReentrantLock class (which implements Lock):

``` java
void lock();

void lockInterruptibly() throws InterruptedException;

boolean tryLock();

boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
```

## Why being fair is bad for reentrant locks

With fair locking, threads can acquire locks only in the order in which they were requested, whereas an unfair lock allows a lock to acquire it out of its turn. This is called barging (breaking the queue and acquiring the lock when it became available).

Fair locking has a significant performance cost because of the overhead of suspending and resuming threads. There could be cases where there is a significant delay between when a suspended thread is resumed and when it actually runs. Let's see a situation:

A -> holds a lock.
B -> has requested and is in a suspended state waiting for A to release the lock.
C -> requests the lock at the same time that A releases the lock, and has not yet gone to a suspended state.

As C has not yet gone to a suspended state, there is a chance that it can acquire the lock released by A, use it, and release it before B even finishes waking up. So, in this context, unfair lock has a significant performance advantage.

Intrinsic locks and extrinsic locks have the same mechanism inside for locking, so the performance improvement is purely subjective. It depends on the use cases we discussed above. Extrinsic locks give a more explicit control mechanism for better handling of deadlocks, starvation, and so on.
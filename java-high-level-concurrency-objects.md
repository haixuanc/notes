## High Level Concurrency Objects

New features introduced since Java 5.0:

- Lock objects support locking idioms that simplify many concurrent applications.
- Executors define a high-level API for launching and managing threads. Executor implementations provided by java.util.concurrent provide thread pool management suitable for large-scale applications.
- Concurrent collections make it easier to manage large collections of data, and can greatly reduce the need for synchronization.
- Atomic variables have features that minimize synchronization and help avoid memory consistency errors.
- ThreadLocalRandom (in JDK 7) provides efficient generation of pseudorandom numbers from multiple threads.

## 1. Lock Objects

Synchronized code relies on a simple kind of reentrant lock. This kind of lock is easy to use, but has many limitations. More sophisticated locking idioms are supported by the `java.util.concurrent.locks` package. 

The biggest advantage of Lock objects over implicit locks is their ability to **back out of an attempt to acquire a lock**. 

`tryLock()`

The tryLock method backs out if the lock is not available immediately or before a timeout expires (if specified). 

`lockInterruptibly()`

The lockInterruptibly method backs out if another thread sends an interrupt before the lock is acquired.

## 2. Executors

In large-scale applications, it makes sense to separate thread management and creation from the rest of the application. Objects that encapsulate these functions are known as *executors*.

### Executor Interfaces

- `Executor`

    The `Executor` interface provides a single method, execute, designed to be a drop-in replacement for a common thread-creation idiom. If r is a `Runnable` object, and e is an `Executor` object you can replace:

    ```java
    (new Thread(r)).start();
    e.execute(r);
    ```
    However, the definition of `execute` is less specific. The low-level idiom creates a new thread and launches it immediately. Depending on the `Executor` implementation, execute may do the same thing, but is more likely to use an existing *worker thread* to run r, or to place r in a queue to wait for a *worker thread* to become available.

- `ExecutorService`

    The `ExecutorService` interface supplements `execute` with a similar, but more versatile `submit` method. Like `execute`, `submit` accepts `Runnable` objects, but also accepts `Callable` objects, which allow the task to return a value. The `submit` method returns a `Future` object, which is used to retrieve the `Callable` return value and to manage the status of both `Callable` and `Runnable` tasks.

- `ScheduledExecutorService`

    The ScheduledExecutorService interface supplements the methods of its parent ExecutorService with schedule, which executes a Runnable or Callable task after a specified delay. In addition, the interface defines scheduleAtFixedRate and scheduleWithFixedDelay, which executes specified tasks repeatedly, at defined intervals.

### Thread Pools

Most of the executor implementations in java.util.concurrent use thread pools, which consist of worker threads. This kind of thread exists separately from the Runnable and Callable tasks it executes and is often used to execute multiple tasks.

- Using worker threads **minimizes the overhead due to thread creation**. 
- One common type of thread pool is the **fixed thread pool**. 
- An important advantage of the fixed thread pool is that applications using it **degrade gracefully**. With a limit on the number of the threads that can be created, the application will not be servicing HTTP requests as quickly as they come in, but it will be servicing them as quickly as the system can sustain.

### Fork/Join
## High level concurrency

> Remember, a **happens-before** relationship is about visibility, not sequence.

### 1. Lock objects

### 2. Executor

#### Executor interfaces

The `java.util.concurrent` package defines three executor interfaces:

- `Executor`, a simple interface that supports launching new tasks.
        ```java
        // Replace
        (new Thread(r)).start();
        // with
        e.execute(r);
        ```
- `ExecutorService`, a subinterface of Executor, which adds features that help manage the lifecycle, both of the individual tasks and of the executor itself.
        ```java
        es.submit(runnable);
        es.submit(callable);
        ```
- `ScheduledExecutorService`, a subinterface of ExecutorService, supports future and/or periodic execution of tasks.

#### Thread pools

Most of the executor implementations in `java.util.concurrent` use thread pools, which consist of worker threads. This kind of thread exists separately from the Runnable and Callable tasks it executes and is often used to execute multiple tasks.

One common type of thread pool is the fixed thread pool. This type of pool always has a specified number of threads running; if a thread is somehow terminated while it is still in use, it is automatically replaced with a new thread. Tasks are submitted to the pool via an internal queue, which holds extra tasks whenever there are more active tasks than threads.

A simple way to create an executor that uses a fixed thread pool is to invoke the `newFixedThreadPool` factory method in java.util.concurrent.Executors This class also provides the following factory methods:

- The `newCachedThreadPool` method creates an executor with an expandable thread pool. This executor is suitable for applications that launch many short-lived tasks.
- The `newSingleThreadExecutor` method creates an executor that executes a single task at a time.
- Several factory methods are `ScheduledExecutorService` versions of the above executors.

### 3. Fork/Join

The fork/join framework is an implementation of the ExecutorService interface that helps you take advantage of multiple processors. It is designed for work that can be broken into smaller pieces recursively. The goal is to use all the available processing power to enhance the performance of your application.

As with any ExecutorService implementation, the fork/join framework distributes tasks to worker threads in a thread pool. The fork/join framework is distinct because it uses a **work-stealing** algorithm. Worker threads that run out of things to do can steal tasks from other threads that are still busy.

### 4. Built-in concurrency constructs

- Concurrent collections
- Atomic variables
- Concurrent random numbers

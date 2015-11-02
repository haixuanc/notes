`InterruptedException` is a checked exception.

## Blocking methods

When a method throws InterruptedException, it is telling you that

- it is a blocking method
- and that it will make an attempt to unblock and return early -- if you ask nicely.

### What is a blocking method

A blocking method is different from an ordinary method that just takes a long time to run.

#### An ordinary long-running method

The completion of an ordinary method is dependent only on how much work you've asked it to do and whether adequate computing resources (CPU cycles and memory) are available.

#### A blocking method

The completion of a blocking method, on the other hand, is also dependent on some external event, such as timer expiration, I/O completion, or the action of another thread (releasing a lock, setting a flag, or placing a task on a work queue). 

### Cancel a blocking method

Because blocking methods can potentially take forever if the event they are waiting for never occurs, it is often useful for blocking operations to be **cancelable**. (It is often useful for long-running non-blocking methods to be cancelable as well.)

A cancelable operation is one that can be externally moved to completion in advance of when it would ordinarily complete on its own. E.g. `Thread.sleep()`, and `Object.wait()` provides interruption mechanism so one thread can request another thread to stop what it is doing early.

> When a method throws InterruptedException, it is telling you that if the thread executing the method is interrupted, it will make an attempt to stop what it is doing and return early and indicate its early return by throwing InterruptedException. 

### APIs with built-in interrupt handling

In response to interrupt, these APIs will terminate immediately and throw an exception:

- `Object.wait()`, `Thread.sleep()`
- Most `java.util.concurrent` structures
- java NIO (but not `java.io`) and it does not use `InterruptedException`, instead using `ClosedByInterruptException`.

## Thread interruption

Every thread has a Boolean property associated with it that represents its **interrupted status**. The interrupted status is initially false; when a thread is interrupted by some other thread through a call to `Thread.interrupt()`, one of two things happens:

- If that thread is executing a low-level interruptible blocking method like `Thread.sleep()`, `Thread.join()`, or `Object.wait()`, it unblocks and throws `InterruptedException`.
-  Otherwise, `interrupt()` merely sets the thread's interruption status. Code running in the interrupted thread can later poll the interrupted status to see if it has been requested to stop what it is doing; the interrupted status can be read with `Thread.isInterrupted()` and can be read and cleared in a single operation with the poorly named `Thread.interrupted()`.

Interruption is a *cooperative mechanism*. When one thread interrupts another, the interrupted thread does not necessarily stop what it is doing immediately. You are free to ignore an interruption request, but doing so may compromise responsiveness.

One of the benefits of the cooperative nature of interruption is that it provides more flexibility for safely constructing cancelable activities. We rarely want an activity to stop immediately; program data structures could be left in an inconsistent state if the activity were canceled mid-update. Interruption allows a cancelable activity to clean up any work in progress, restore invariants, notify other activities of the cancellation, and then terminate.

## Dealing with InterruptedException

1. The easiest strategy is to throw `InterruptedException` yourself, as shown bellow

``` java
public class TaskQueue {
    private static final int MAX_TASKS = 1000;

    private BlockingQueue<Task> queue 
        = new LinkedBlockingQueue<Task>(MAX_TASKS);

    public void putTask(Task r) throws InterruptedException { 
        queue.put(r);
    }

    public Task getTask() throws InterruptedException { 
        return queue.take();
    }
}
```

2. Sometimes it is necessary to do some amount of cleanup before propagating the exception. In this case, you can catch `InterruptedException`, perform the cleanup, and then rethrow the exception.

### Don't swallow interrupts

Sometimes throwing `InterruptedException` is not an option, such as when a task defined by `Runnable` calls an `interruptible` method. (Because the defintion of `run()` method is `pubic void run()` without `throws` clause.)

When a blocking method detects interruption and throws `InterruptedException`, *it clears the interrupted status*.

If you catch `InterruptedException` but cannot rethrow it, you should preserve evidence that the interruption occurred so that code higher up on the call stack can learn of the interruption and respond to it if it wants to. This task is accomplished by calling `interrupt()` to **"reinterrupt"** the current thread.

``` java
public class TaskRunner implements Runnable {
    private BlockingQueue<Task> queue;

    public TaskRunner(BlockingQueue<Task> queue) { 
        this.queue = queue; 
    }

    public void run() { 
        try {
             while (true) {
                 Task task = queue.take(10, TimeUnit.SECONDS);
                 task.execute();
             }
         }
         catch (InterruptedException e) { 
             // Restore the interrupted status
             Thread.currentThread().interrupt();
         }
    }
}
```

> At the very least, whenever you catch InterruptedException and don't rethrow it, reinterrupt the current thread before returning.

If you cannot rethrow `InterruptedException`, whether or not you plan to act on the interrupt request, you still want to reinterrupt the current thread because a single interruption request may have multiple "recipients." The standard thread pool (`ThreadPoolExecutor`) worker thread implementation is responsive to interruption, so interrupting a task running in a thread pool may have the effect of both canceling the task and notifying the execution thread that the thread pool is shutting down.

## Implementing cancelable tasks

Nothing in the language specification gives interruption any specific semantics, but in larger programs, it is difficult to maintain any semantics for interruption other than cancellation. 

Just because a task is cancelable does not mean it needs to respond to an interrupt request immediately.

The one time it is acceptable to swallow an interrupt is when you know the thread is about to exit. 

### Noninterruptible blocking

Not all blocking methods throw InterruptedException. 

- The input and output stream classes may block waiting for an I/O to complete, but they do not throw InterruptedException, and they do not return early if they are interrupted.
-  Similarly, attempting to acquire an *intrinsic lock* (enter a `synchronized` block) cannot be interrupted, but `ReentrantLock` supports an interruptible acquisition mode.

## References

- [Java theory and practice: Dealing with InterruptedException](http://www.ibm.com/developerworks/library/j-jtp05236/)
- [InterruptedException and interrupting threads explained](http://www.nurkiewicz.com/2014/05/interruptedexception-and-interrupting.html)
- [Handle InterruptedException](http://www.javapractices.com/topic/TopicAction.do?Id=251)
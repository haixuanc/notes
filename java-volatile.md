## Definition

The Java `volatile` keyword is used to mark a Java variable as **"being stored in main memory"**. More precisely that means, that every read of a volatile variable will be read from the computer's main memory, and not from the CPU cache, and that every write to a volatile variable will be written to main memory, and not just to the CPU cache.

## Java volatile Guarantees Variable Visibility

The Java volatile keyword guarantees visibility of changes to variables across threads. 

In a multithreaded application where the threads operate on non-volatile variables, each thread may copy variables from main memory into a CPU cache while working on them, for performance reasons

With non-volatile variables there are no guarantees about when the Java Virtual Machine (JVM) reads data from main memory into CPU caches, or writes data from CPU caches to main memory.

The problem with multiple threads that do not see the latest value of a variable because that value has not yet been written back to main memory by another thread, is called a "visibility" problem. The updates of one thread are not visible to other threads.

## The Java volatile Guarantee

Actually, the volatile keyword guarantees this:

- If Thread A writes to a volatile variable and Thread B subsequently reads the same volatile variable, then all variables visible to Thread A before writing the volatile variable, will also be visible to Thread B.
- The reading and writing instructions of volatile variables cannot be reordered by the JVM (the JVM may reorder instructions for performance reasons as long as the JVM detects no change in program behaviour from the reordering).

The reading and writing of the non-volatile variable cannot be reordered to happen before or after the reading and writing of the volatile variable.

## When is volatile Enough?

But in case one thread reads and writes the value of a volatile variable, and other threads only read the variable, then the reading threads are guaranteed to see the latest value written to the volatile variable. 

## Performance Considerations of volatile

Reading and writing of volatile variables causes the variable to be read or written to main memory. Reading from and writing to main memory is more expensive than accessing the CPU cache. Accessing volatile variables also prevent instruction reordering which is a normal performance enhancement technique. Thus, you should only use volatile variables when you really need to enforce visibility of variables.
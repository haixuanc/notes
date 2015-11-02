# Synchronization

Threads communicate primarily by sharing access to fields and the objects reference fields refer to. This form of communication is extremely efficient, but makes two kinds of errors possible:

- thread interference
- memory consistency

The tool needed to prevent these errors is **synchronization**.

However, synchronization can introduce **thread contention**, which occurs when two or more threads try to access the same resource simultaneously and cause the Java runtime to execute one or more threads more slowly, or even suspend their execution. Thread contentions occur in forms like:

- starvation
- livelock


## 1. Thread Interference

> Describes how errors are introduced when multiple threads access shared data.

Sample program:

``` java
class Counter {
    private int c = 0;

    public void increment() {
        c++;
    }

    public void decrement() {
        c--;
    }

    public int value() {
        return c;
    }

}
```

Interference happens when two operations, running in different threads, but acting on the same data, interleave. This means that the two operations consist of multiple steps, and the sequences of steps overlap.

It might not seem possible for operations on instances of Counter to interleave, since both operations on c are single, simple statements. *However, even simple statements can translate to multiple steps by the virtual machine.*

Because they are unpredictable, thread interference bugs can be difficult to detect and fix.

## 2. Memory Consistency Errors

> Describes errors that result from inconsistent views of shared memory.

The key to avoiding memory consistency errors is understanding the **happens-before** relationship. This relationship is simply a guarantee that memory writes by one specific statement are visible to another specific statement.

There are several actions that create happens-before relationships. One of them is synchronization:

- When a statement invokes `Thread.start`, every statement that has a happens-before relationship with that statement also has a happens-before relationship with every statement executed by the new thread. The effects of the code that led up to the creation of the new thread are visible to the new thread.
- When a thread terminates and causes a `Thread.join` in another thread to return, then all the statements executed by the terminated thread have a happens-before relationship with all the statements following the successful join. The effects of the code in the thread are now visible to the thread that performed the join.

[** More actions that create happens-before relationships](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html#MemoryVisibility)

## 3. Synchronized Methods

> Describes a simple idiom that can effectively prevent thread interference and memory consistency errors.

The Java programming language provides two basic synchronization idioms:

- synchronized methods
- synchronized statements

Sample program:

``` java
public class SynchronizedCounter {
    private int c = 0;

    public synchronized void increment() {
        c++;
    }

    public synchronized void decrement() {
        c--;
    }

    public synchronized int value() {
        return c;
    }
}
```

If count is an instance of SynchronizedCounter, then making these methods synchronized has two effects:

- First, it is not possible for two invocations of synchronized methods on the same object to interleave. When one thread is executing a synchronized method for an object, all other threads that invoke synchronized methods for the same object block (suspend execution) until the first thread is done with the object.
- Second, when a synchronized method exits, it automatically establishes a happens-before relationship with any subsequent invocation of a synchronized method for the same object. This guarantees that changes to the state of the object are visible to all threads.

Note that **constructors cannot be synchronized** — using the synchronized keyword with a constructor is a syntax error. Synchronizing constructors doesn't make sense, because only the thread that creates an object should have access to it while it is being constructed.

When constructing an object that will be shared between threads, be very careful that a reference to the object does not "leak" prematurely.

Synchronized methods enable a simple strategy for preventing thread interference and memory consistency errors: if an object is visible to more than one thread, all reads or writes to that object's variables are done through synchronized methods. (*An important exception: `final` fields, which cannot be modified after the object is constructed, can be safely read through non-synchronized methods, once the object is constructed*) This strategy is effective, but can present problems with **liveness**, as we'll see later in this lesson.

## 4. Implicit Locks and Synchronization

> Describes a more general synchronization idiom, and describes how synchronization is based on implicit locks.

Synchronization is built around an internal entity known as the **intrinsic lock** or **monitor lock**. (The API specification often refers to this entity simply as a "**monitor**.")

Intrinsic locks play a role in both aspects of synchronization: 
- enforcing exclusive access to an object's state
- establishing happens-before relationships that are essential to visibility.

*Every object has an intrinsic lock associated with it.* By convention, a thread that needs exclusive and consistent access to an object's fields has to acquire the object's intrinsic lock before accessing them, and then release the intrinsic lock when it's done with them. The other thread will block when it attempts to acquire the lock. When a thread releases an intrinsic lock, a happens-before relationship is established between that action and any subsequent acquistion of the same lock.

### Locks In Synchronized Methods

The lock release occurs even if the return was caused by an uncaught exception.

When a static synchronized methd is invoked, the thread acquires the intrinsic lock for the *Class object* associated with the class.

### Synchronized Statements

Unlike synchronized methods, *synchronized statements must specify the object that provides the intrinsic lock*:

``` java
public void addName(String name) {
    synchronized(this) {
        lastName = name;
        nameCount++;
    }
    nameList.add(name);
}
```

> Avoid invoking other objects' methods from synchronized code, because it can create liveness problems.

Synchronized statements are also useful for improving concurrency with fine-grained synchronization.

Instead of using synchronized methods or otherwise using the lock associated with this, we create two objects solely to provide locks:

``` java
public class MsLunch {
    private long c1 = 0;
    private long c2 = 0;
    private Object lock1 = new Object();
    private Object lock2 = new Object();

    public void inc1() {
        synchronized(lock1) {
            c1++;
        }
    }

    public void inc2() {
        synchronized(lock2) {
            c2++;
        }
    }
}
```

> Use this idiom with extreme care. You must be absolutely sure that it really is safe to interleave access of the affected fields.

### Reentrant Synchronization

> A thread cannot acquire a lock owned by another thread. But a thread can acquire a lock that it already owns.

Allowing a thread to acquire the same lock more than once enables **reentrant synchronization**. This describes a situation where synchronized code, directly or indirectly, invokes a method that also contains synchronized code, and both sets of code use the same lock. Without reentrant synchronization, synchronized code would have to take many additional precautions to avoid having a thread cause itself to block.

## 5. Atomic Access

> Talks about the general idea of operations that can't be interfered with by other threads.

**Definition:** In programming, an atomic action is one that effectively happens all at once.
- An atomic action cannot stop in the middle: it either happens completely, or it doesn't happen at all.
- No side effects of an atomic action are visible until the action is complete.

Some atomic actions:

- Reads and writes are atomic for reference variables and for most primitive variables (all types except long and double).
- Reads and writes are atomic for all variables declared `volatile` (including long and double variables).

Atomic actions cannot be interleaved, so they can be used without fear of thread interference. However, this does not eliminate all need to synchronize atomic actions, *because memory consistency errors are still possible*.

Using simple atomic variable access is more efficient than accessing these variables through synchronized code, but requires more care by the programmer to avoid memory consistency errors. Whether the extra effort is worthwhile depends on the size and complexity of the application.

### Volatile varibles

Any write to a volatile variable establishes a happens-before relationship with subsequent reads of that same variable.

This means that changes to a volatile variable are always visible to other threads. What's more, it also means that when a thread reads a volatile variable, it sees not just the latest change to the volatile, but also the side effects of the code that led up the change.
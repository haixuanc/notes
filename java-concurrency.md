## Processes

A process generally has a complete, private set of basic run-time resources; in particular, each process has its own **memory space**.

To facilitate communication between processes, most operating systems support **Inter Process Communication** (IPC) resources, such as:

- pipes
- sockets.

IPC is used not just for communication between processes on the same system, but processes on different systems.

Most implementations of the *Java virtual machine* run as a **single process**. A Java application can create additional processes using a `ProcessBuilder` object.

## Threads

Threads are sometimes called *lightweight processes*. Both processes and threads provide an execution environment, but creating a new thread requires fewer resources than creating a new process.

Threads exist within a process — every process has at least one. Threads share the process's resources, including:

- memory
- open files

This makes for efficient, but potentially problematic, communication.

Every application has at least one thread — or several, if you count "system" threads that do things like memory management and signal handling. But from the application programmer's point of view, you start with just one thread, called the main thread. This thread has the ability to create additional threads.

## Thread Objects

Each thread is associated with an instance of the class `Thread`. There are two basic strategies for using Thread objects to create a concurrent application.

- To directly control thread creation and management, simply instantiate `Thread` each time the application needs to initiate an asynchronous task.
- To abstract thread management from the rest of your application, pass the application's tasks to an `executor`.

## Defining and Starting a Thread

An application that creates an instance of Thread must provide the code that will run in that thread. There are two ways to do this:

- Provide a `Runnable` object. The `Runnable` interface defines a single method, `run`, meant to contain the code executed in the thread. The `Runnable` object is passed to the Thread constructor, as in the HelloRunnable example:

  ``` java
  public class HelloRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new Thread(new HelloRunnable())).start();
    }
  }
  ```

- Subclass `Thread`. The Thread class itself implements `Runnable`, though its run method does nothing. An application can subclass `Thread`, providing its own implementation of `run`, as in the HelloThread example:

  ``` java
  public class HelloThread extends Thread {
    @Override
    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new HelloThread()).start();
    }
  }
  ```
  
Notice that both examples invoke `Thread.start` in order to start the new thread.
  
## Pausing Execution with Sleep
  
`Thread.sleep` causes the current thread to suspend execution for a specified period.
  
- This is an efficient means of making processor time available to the other threads of an application or other applications that might be running on a computer system.
- The sleep method can also be used for pacing, as shown in the example that follows, and waiting for another thread with duties that are understood to have time requirements.

Also, the sleep period can be terminated by interrupts. In any case, you cannot assume that invoking sleep will suspend the thread for precisely the time period specified.

### InterruptedException

This is an exception that sleep throws when another thread interrupts the current thread while sleep is active.

``` java
public class SleepMessages {
    public static void main(String args[])
        throws InterruptedException {
        String importantInfo[] = {
            "Mares eat oats",
            "Does eat oats",
            "Little lambs eat ivy",
            "A kid will eat ivy too"
        };

        for (int i = 0;
             i < importantInfo.length;
             i++) {
            //Pause for 4 seconds
            Thread.sleep(4000);
            //Print a message
            System.out.println(importantInfo[i]);
        }
    }
}
```
# Guidance for Multithreading

## Table of Contents

[TOC]

## Learning Objectives

+ Understand multithreaded programming.
+ Understand synchronization and mutual exclusion in concurrent environments.
+ Learn to handle data races in multithreaded programs.

## Background

### Parallel Analysis of Cloud-Service Logs

In the previous section, we completed the basic log-parsing features. Real cloud services, however, produce enormous numbers of logs stored across many files. Parsing those files one at a time can take an unacceptably long time.

In this section, you will use multiple threads to analyze cloud-service logs in parallel. For example, suppose a cloud service produces these eight files over eight days:

```shell
+-Logs
  20260701.log
  20260702.log
  20260703.log
  20260704.log
  20260705.log
  20260706.log
  20260707.log
  20260708.log
```

With three analysis threads, the work might be distributed as follows:

```shell
Thread 0: analyze 20260701.log, 20260704.log, and 20260707.log
Thread 1: analyze 20260702.log, 20260705.log, and 20260708.log
Thread 2: analyze 20260703.log and 20260706.log
```

This can greatly reduce the total analysis time.

## Knowledge Primer

This section reviews the knowledge required for the tasks and introduces a few additional concepts.

### Processes and Threads

When a program runs, the operating system creates an illusion: it appears to be the only program on the computer and to have exclusive use of the processor and memory, even if the machine has only one CPU core and one memory module. Think back to learning C. You never had to worry that an out-of-bounds pointer in your program would overwrite another program's memory, or that another program would overwrite one of your variables. The operating system isolates programs so they cannot see one another. Each isolated, running program is a **process**. Processes are independent and do not interfere with each other. Every time we launch a program, we start a process.

A program does not necessarily perform only one activity at a time. Sometimes several activities must progress together. The processes described so far run from beginning to end in sequence; this is single-threaded execution. When one execution path cannot meet our needs, we run multiple **threads** inside the same process. Unlike separate processes, threads in one process share some of that process's resources, including its virtual address space.

### Synchronization and Mutual Exclusion

In a multithreaded program, several threads may access the same memory or variable “at the same time.” This creates a **data race**. We use mutual exclusion to prevent those races, and synchronization to coordinate the threads and control the order in which their work occurs.

> Notice the quotation marks around “at the same time.” Register optimizations, caches shared among CPU cores, and similar effects mean that this phrase is not identical to simultaneity in everyday physical time. Programming languages therefore provide new semantics that define what “at the same time” means to a program. These semantic abstractions are usually expressed in terms of a **memory barrier** or **memory fence**. Problems at this level are called **concurrency** problems, a concept distinct from physical simultaneity or **parallelism**.

#### Critical Sections and Mutexes

When discussing competition for data or resources, the portion of a program that accesses shared data is called a **critical section**. Preventing a data race therefore means preventing several threads from entering the critical section simultaneously. A **mutex** is commonly used for this purpose:

```c
int mutex = 1; // Define a mutex

void thread() {
    lock(&mutex);   // Lock on entry; wait here if another thread holds the lock
    // Critical section
    unlock(&mutex); // Unlock on exit so another thread can acquire the lock
}
```

#### Monitors and Condition Variables: The Producer-Consumer Problem

Whether to acquire or release a lock often depends on state shared among threads. Consider the classic **producer-consumer problem**.

Imagine a warehouse with capacity N. At irregular intervals, a producer adds one item and a consumer removes one. When the warehouse is full, the producer must wait until space becomes available. When it is empty, the consumer must wait until an item arrives.

Suppose the producer and consumer each run on their own thread. Adding to and removing from the shared warehouse creates a data race, so the code that accesses it is a critical section protected by a mutex. Whether the thread can proceed depends on whether the warehouse is empty or full. A waiting thread must release the mutex, then immediately reacquire it and enter the critical section when the required state becomes available.

One way to solve this problem is a **monitor**. Monitors have three main models: Hansen[^1], Hoare[^2], and Mesa[^3], of which the Mesa model is the most widely used.

A **condition variable** is an implementation of the Mesa monitor model[^3]. It provides three fundamental operations:

+ `wait`: Atomically unlock the mutex and sleep. The mutex is reacquired when the thread wakes.
+ `signal`: Wake one sleeping thread.
+ `broadcast`: Wake all sleeping threads.

To simplify the problem, suppose the warehouse has unlimited capacity. The producer-consumer problem can then be solved as follows:

```c
int mtx = 1;           // Define mutex
condition_variable cv; // Define condition variable
int buffer = 0;        // Number of items currently in the warehouse

void producer() {
    while (1) {
        produce();      // Produce an item
        lock(&mtx);     // Lock before adding the item
        buffer += 1;    // Enter the critical section and add it
        signal(&cv);    // An item is available; wake a consumer, if any
        unlock(&mtx);   // Addition is complete; release the mutex
    }
}

void consumer()
{
    while (1)
    {
        lock(&mtx);            // Lock before taking an item
        while (buffer == 0) {  // Check whether an item is available
            wait(&cv, &mtx);   // None is available; unlock and wait
            // On wake, reacquire the mutex. wait returns, then the loop checks again.
        }
        buffer -= 1;           // Take an item
        unlock(&mtx);          // Release the mutex
        consume();             // Consume the item
    }
}
```

> [!IMPORTANT]
>
> For several reasons, a condition variable may experience a **spurious wakeup**. Never guard its condition with `if`; use `while` so the condition is checked again after the thread wakes.

> [!TIP]
>
> Why is it called a “monitor”?
>
> Here, a monitor is more than an ordinary condition-variable value or a collection of methods such as C\#'s `Monitor`. It was originally proposed as an independent programming model that encapsulates shared state and the operations that synchronize access to it. See the paper on the Hoare model[^2]; [Java's use of `synchronized` methods](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html) also preserves part of this original design. Like a process, thread, or coroutine, a monitor is an abstraction over program execution—not simply a variable that observes something. Chinese terminology differs by region: Mainland China commonly uses “管程,” while Taiwan commonly uses the more literal “監視器.” See [Appendix A](../appendix/appendix-a-glossary.md).

### Queues

A **queue** is a common first-in, first-out data structure. Objects can be added one at a time (enqueued) and later removed (dequeued). Like customers waiting in line at a store, the first object to arrive is the first one served, so objects leave in the same order in which they entered:

```c
queue q; // Define a queue

q.enqueue(1); // Add 1; current queue: 1
q.enqueue(2); // Add 2; current queue: 1, 2
q.enqueue(3); // Add 3; current queue: 1, 2, 3
int x1 = q.dequeue(); // Remove 1; current queue: 2, 3
int x2 = q.dequeue(); // Remove 2; current queue: 3
q.enqueue(4); // Add 4; current queue: 3, 4
int x3 = q.dequeue(); // Remove 3; current queue: 4
int x4 = q.dequeue(); // Remove 4; current queue is empty
```

## Tasks for This Section

Building on `01-basic`, create a directory-level log analyzer that parses files in parallel.

### Task Description

Implement a `LogFileAnalyzer` class with this interface:

```csharp
class LogFileAnalyzer {
    // Specify a log directory, then scan it for files whose extension is .log
    LogFileAnalyzer(string? directoryPath);
    // Change the log directory and scan the new location for .log files
    void ChangeDirectory(string? directoryPath);
    // Get the names of all log files in the directory
    IReadOnlyList<string> GetLogFiles();
    // Start degreeOfParallelism parallel tasks and analyze all log files
    // A degreeOfParallelism of 0 uses the number of logical processors
    void AnalyzeAll(int degreeOfParallelism);
    // Start degreeOfParallelism parallel tasks and analyze the files named in fileNames
    // A degreeOfParallelism of 0 uses the number of logical processors
    void AnalyzeFiles(int degreeOfParallelism, IEnumerable<string> fileNames);
    // Store the result for fileName in result and return true, or return false if none exists
    bool TryGetAnalysisResult(string fileName, out AnalysisResult? result);
}
```

### (S2.1) Step 1: A Thread-Safe Queue

First, implement a queue that works correctly with multiple threads. When different threads access its elements at the same time, the queue must prevent data races.

C\# provides [`BlockingCollection<T>`](https://learn.microsoft.com/en-us/dotnet/standard/collections/thread-safe/blockingcollection-overview) as a thread-safe queue. To practice multithreaded programming, however, you will build a simple version yourself on top of C\#'s non-thread-safe [`Queue<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.queue-1?view=net-10.0).

The thread-safe queue belongs in `LogAnalyzer/WorkQueue.cs` and must implement this interface:

```csharp
class WorkQueue<T> {
    // Add an item to the queue
    void Enqueue(T item);
    // Indicate that no more items will be added
    void CompleteAdding();
    // Remove an item from the queue.
    // A. If an item is available, immediately store it in item and return true.
    // B. If the queue is empty and adding is complete, set item to default and return false.
    // If the queue is empty but adding is not complete, wait:
    //   until an item is added, then perform A; or
    //   until adding is complete, then perform B.
    bool TryDequeue([NotNullWhen(true)] out T? item);
}
```

This is the classic producer-consumer problem with unlimited warehouse capacity: `Enqueue` is the producer and `TryDequeue` is the consumer, with one additional operation that completes adding. Define a variable that records whether adding has completed. When it completes, wake all consumers with `broadcast`. When a consumer tries to take an item—including after it wakes—it must check both whether an item is available and whether adding has completed. If an item exists, remove it. If none exists and adding is complete, return `false`.

**Remember to protect both the internal data structure and the shared flag that records completion.**

> [!NOTE]
>
> **Task 2.1 (T2.1)**
>
> Complete `WorkQueue<T>` in `LogAnalyzer/WorkQueue.cs`.
>
> Run `test-02-multithreading` after completing your implementation. All tests in `Test_2_1_WorkingQueue`—those whose names begin with `T2.1`—should pass.

**APIs you may find useful:**

+ C\# `Queue<T>` operations:

  ```csharp
  var q = new Queue<int>();
  q.Enqueue(1);        // Add to the queue
  int x = q.Dequeue(); // Remove an item from the queue
  ```

+ Mutual exclusion in C\#:

  Any **reference-type** object can act as a lock in C\#. The `lock` keyword provides convenient syntax:

  ```csharp
  lock (obj) { // Acquire the lock on obj when entering the braces
      // Critical section
  } // Release the lock when leaving the braces
  ```

  This is equivalent to:

  ```csharp
  Monitor.Enter(obj);
  try {
      // Critical section
  } finally {
      Monitor.Exit(obj); // Release even if the critical section throws an exception
  }
  ```

+ C\# monitor (condition-variable) operations:

  C\# monitors follow the Mesa model. Their condition-variable operations are members of `Monitor`:

  ```csharp
  lock (obj) {
      while (condition) {
          Monitor.Wait(obj); // wait
      }
      Monitor.Pulse(obj);    // signal
      Monitor.PulseAll(obj); // broadcast
  }
  ```

### (S2.2) Step 2: Parallel Log Analysis

The code for this step is in `LogAnalyzer`:

```shell
LogAnalyzer
    AnalysisResult.cs
    LogFileAnalyzer.cs
    WorkQueue.cs
```

For the basic portion of this project, assume that log files use the `.log` extension. Given a directory, the system automatically scans it for those files and analyzes them on demand. Read the starter code carefully, then complete `AnalyzeAll`, `AnalyzeFiles`, `RunWorkers`, and any other unfinished portions.

`LogFileAnalyzer` must implement this interface:

```csharp
class LogFileAnalyzer {
    string? CurrentDirectory { get; } // Directory containing the log files
    bool HasDirectory { get; }        // Whether a log directory has been set
    bool IsAnalyzing { get; }         // Whether logs are currently being analyzed
    LogFileAnalyzer(string? directoryPath = null); // Construct with an optional log directory
    bool ChangeDirectory(string? directoryPath);   // Set or change the log directory
    IReadOnlyList<string> GetLogFiles();           // Names of all log files in the directory
    
    // Get the analysis result for the log file named fileName.
    // Return false if the file does not exist; otherwise return true and store the result.
    bool TryGetAnalysisResult(string fileName, out AnalysisResult? result);
    
    // Use at most degreeOfParallelism threads to analyze all log files.
    // If degreeOfParallelism is 0, use the number of logical processors on this machine.
    // Do not return until analysis is complete.
    void AnalyzeAll(int degreeOfParallelism);
    
    // Use at most degreeOfParallelism threads to analyze the logs specified by fileNames.
    // If degreeOfParallelism is 0, use the number of logical processors on this machine.
    // Do not return until analysis is complete.
    void AnalyzeFiles(int degreeOfParallelism, IEnumerable<string> fileNames)
}
```

The methods must also meet these requirements:

+ The `AnalysisResult` returned by `TryGetAnalysisResult` must obey these constraints:
  + Before analysis is complete, `State` is `NotAnalyzed` and `Entries` is an empty array.
  + After successful analysis, `State` is `Succeeded` and `Entries` contains the result.
  + If analysis fails—for example, because the log format is invalid—`State` is `Failed`, `ErrorMessage` contains the error information, and `Entries` is an empty array.
+ `AnalyzeAll` and `AnalyzeFiles` must skip files whose results have already been saved, conserving compute resources. `AnalyzeFiles` must call `RunWorkers` to distribute the analysis tasks, and threads created in `RunWorkers` must use `WorkerMain` as their entry point.

> [!TIP]
>
> This step depends on your implementation from `01-basic`, so merge the changes from `homework/01-basic` into this section. First confirm that the current branch is `homework/02-multithreading`:
> 
> ```shell
> git branch
> ```
> 
> Then run:
> 
> ```shell
> git merge "homework/01-basic"
> ```
> 
> This synchronizes the contents of `homework/01-basic` into `homework/02-multithreading`.
>
> Later chapters use the same merge process, so it will not be repeated.

> [!NOTE]
>
> **Task 2.2 (T2.2)**
>
> Complete `LogFileAnalyzer` in `LogAnalyzer/LogFileAnalyzer.cs`.
>
> Run `test-02-multithreading` after completing your implementation. All tests should pass.
>
> **Tip:** If your implementation has a bug that you cannot locate immediately, you may begin S2.3 first.

**APIs you may find useful:**

+ Create a C\# file stream with `var reader = new StreamReader(filePath);`, where `filePath` is the input path and `reader` reads the file.
+ Access file information through `FileInfo`:
  + `Name` gets the file name.
  + `FullName` gets its full path.
+ Create an empty array of type `T` with `Array.Empty<T>()`.
+ Get text from an exception with `ex.Message`. `ex.ToString()` also includes more detailed information such as the [stack trace](https://stackoverflow.com/questions/3988788/what-is-a-stack-trace-and-how-can-i-use-it-to-debug-my-application-errors).

### (S2.3) Step 3: A Simple Interactive Console

You now have a reasonably complete log-analysis system. To make later debugging easier, implement a very simple interactive console for it.

The starter code is in `LocalCli/Program.cs`. Complete the remaining portions by calling the `LogFileAnalyzer` you implemented earlier:

+ `InputDirectory`: Read the directory containing log files and construct the `analyzer`.
+ `ShowLogFiles`: Display the files in the directory by calling `LogFileAnalyzer.GetLogFiles`.
+ `AnalyzeFiles`: Read a comma-separated series of file names and analyze them by calling `LogFileAnalyzer.AnalyzeFiles`.
+ `AnalyzeAll`: Analyze every log file by calling `LogFileAnalyzer.AnalyzeAll`.
+ `GetAnalysisResult`: Read a file name and display its result by calling `LogFileAnalyzer.TryGetAnalysisResult`:
  + Tell the user if the file has not been analyzed.
  + For a successfully analyzed file, call `KeyValueVisitor.Dump` to print the result.
  + For a failed analysis, print `AnalysisResult.ErrorMessage`.
+ **Exception handling (important):** `LogFileAnalyzer` handles invalid input either by returning `false` or by throwing an exception. For robustness, catch these exceptions, tell the user that the input is invalid, and ask them to try again. Invalid input must never crash the entire program.

One possible finished interface looks like this:

![Local CLI](./assets/localcli.png)

> [!NOTE]
>
> **Task 2.3 (T2.3)**
>
> Complete the implementation in `LocalCli/Program.cs`.
>
> After finishing, create a text file named `report.md` in `docs/02-multithreading`. Describe the features you implemented and include screenshots of the complete program (using the example above for reference) and robustness tests with various invalid inputs.

**APIs you may find useful:**

+ C\# string operations:

  + `string.Join`: Join an enumerable sequence, such as a list, with a separator:

    ```csharp
    var list = new List<int> { 1, 2, 3 };
    var str = string.Join(", ", list); // str = "1, 2, 3"
    ```

  + `string.Split`: Split a string at a delimiter:

    ```csharp
    var str = "1,2,3";
    var result = str.Split(','); // result = { "1", "2", "3" }
    ```
    
  + `string.Trim`: Return a new string with whitespace removed from both ends:

    ```csharp
    var str = "  abc    ";
    var result = str.Trim(); // result = "abc"
    ```

  + `string.IsNullOrEmpty`: Test whether a string is null or empty:

    ```csharp
    var b1 = string.IsNullOrEmpty("");    // true
    var b2 = string.IsNullOrEmpty("abc"); // false
    ```

+ LINQ operations: LINQ is a relatively advanced C\# feature that can greatly simplify work with enumerable types. Its method syntax can replace many loops, though beginners may use ordinary loops if they are not yet comfortable with it. For more information, see the official documentation: [Write LINQ queries in C#](https://learn.microsoft.com/en-us/dotnet/csharp/linq/get-started/write-linq-queries). Common LINQ methods include:

  + `Where`: Filter elements by a condition.
  + `Select`: Transform elements.
  + `OrderBy`: Sort elements by a specified key.

  LINQ methods return `IEnumerable<T>`. This lazily evaluated type is produced with **coroutine** techniques; in C\#, `yield return` can implement lazy evaluation. Iterate it with `foreach`, or convert it to a list or another container with a method such as `ToList`.

## Questions

Answer the questions in `docs/02-multithreading/report.md`.

### (Q2.1)

This question assesses your understanding of critical sections.

A section of code that accesses a critical resource is a critical section. In a multithreaded program, critical resources are variables shared among threads. Answer the following:

+ Which variables are shared in `WorkQueue<T>`, and how are they protected from data races?
+ Which variables are shared in `LogFileAnalyzer`, and how are they protected from data races?
+ What happens if a condition variable is guarded by `if` rather than `while` when a spurious wakeup occurs? On UNIX-like systems, mechanisms such as UNIX signals can wake a thread in `wait` even if nobody called `signal` or `broadcast`. Briefly explain the consequences using the unlimited-capacity producer-consumer problem.

### (Q2.2)

In the provided `LogFileAnalyzer` starter code:

+ Which code scans the specified directory for every log file whose name ends in `.log`? If the requirement also called for recursively finding log files in every child directory, grandchild directory, and so on, how would you implement it? A brief answer is sufficient.

### (Q2.3)

Did you use AI for this assignment? Based on your answer, choose either (Q2.3.a) or (Q2.3.b).

#### (Q2.3.a)

If you did not use AI, approximately how long did it take to pass every test? How difficult was this assignment compared with assignments from programming courses you have taken? Did you use a traditional search engine? Overall, was the section too easy, appropriately challenging, or too difficult?

#### (Q2.3.b)

If you used AI, what prompts did you provide? Did you ask about APIs or a specific implementation technique, ask it to write part of the assignment, or ask it to explain the starter code? Were any of its answers incorrect? If so, which ones? Overall, was the section too easy, appropriately challenging, or too difficult?

## Other Information

For task point values and related details, see [tasks.md](./tasks.md).

## Further Reading

+ [Multithreading and asynchronous programming](https://docs.eesast.com/docs/languages/CSharp/CSharp_2_multithread): Learn more about multithreading and asynchronous programming. (Chinese)

## Previous / Next

+ Previous: [Tasks in Basic Functions](../01-basic/tasks.md)
+ Next: [Tasks in Multithreading](./tasks.md)

## References

[^1]: [Hansen, P. B. (1973). *Operating System Principles*. Prentice-Hall, Inc.](https://dl.acm.org/doi/abs/10.5555/540365)
[^2]: [Hoare, C. A. R. (1974). Monitors: An operating system structuring concept. *Communications of the ACM, 17*(10), 549–557.](https://dl.acm.org/doi/abs/10.1145/355620.361161)
[^3]: [Lampson, B. W., & Redell, D. D. (1980). Experience with processes and monitors in Mesa. *Communications of the ACM, 23*(2), 105–117.](https://dl.acm.org/doi/pdf/10.1145/358818.358824)

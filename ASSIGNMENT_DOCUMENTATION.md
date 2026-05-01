# Assignment 3 - Complete Documentation

**Student Name**: [Aljawharah ALdosari ]  
**Student ID**: [445052070]  
**Date Submitted**: [1 may]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: https://drive.google.com/file/d/1ICecYtPtEd1nHLEjhkNgmTF4UFGNdBhX/view?usp=drivesdk

**Video filename**: `445052070_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [30 may, 7:15]
**What I implemented**: 
started the project and built the fundamental scheduler framework using threads and processes.
**Challenges encountered**: 
Understanding how multiple threads interact with shared variables.
**How I solved it**: 
Reviewed thread execution flow and identified shared resources in the code.
**Testing approach**: 
Ran the program without synchronization to observe inconsistent behavior.
**Time spent**: 
2 hours
---

### Entry 2 - [30 may, 9:40]
**What I implemented**: 
Added ReentrantLock to protect shared counters and execution log.
**Challenges encountered**: 
Ensuring all critical sections were properly protected.
**How I solved it**: 
Wrapped shared operations inside lock.lock() and unlock() using try-finally.
**Testing approach**: 
Ran the program multiple times and verified stable outputs.
**Time spent**: 
1:30 hours
---

### Entry 3 - [30 may, 10:45]
**What I implemented**: 
Added Semaphore to control CPU access.
**Challenges encountered**: 
Understanding where to place acquire/release.
**How I solved it**: 
Placed acquire at start of run() and release in finally block.
**Testing approach**: 
Checked that only one process executes at a time.
**Time spent**: 
1 hours
---
### Entry 4 - [1 may, 12:00]
**What I implemented**: 
Implemented logging and waiting time calculation.
**Challenges encountered**: 
Ensuring thread-safe logging.
**How I solved it**: 
Protected executionLog using lock.
**Testing approach**: 
Verified log size consistency (78 entries).
**Time spent**: 
1 hours
---


### Entry 5 - [1 may, 1:00]
**What I implemented**: 
Final testing and debugging.
**Challenges encountered**: 
Ensuring no race conditions remain.
**How I solved it**: 
Ran multiple tests and verified statistics.
**Testing approach**: 
Executed program 5+ times and compared outputs.
**Time spent**: 
1 hours
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:
[Two race conditions exist in the original code:
contextSwitchCount
Multiple threads increment this variable simultaneously. Without synchronization, updates may be lost due to overlapping reads and writes.
executionLog (ArrayList)
ArrayList is not thread-safe. Concurrent additions can corrupt the list or cause runtime exceptions.
Concurrent access is problematic because threads may read outdated values or overwrite each other’s updates. This can result in incorrect statistics or program crashes.]
---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[ReentrantLock provides mutual exclusion, allowing only one thread to access a critical section at a time.
Semaphore controls how many threads can access a resource simultaneously.
In my code:
I used ReentrantLock to protect shared variables like counters and logs.
I used Semaphore to limit CPU access to one process at a time.
This combination ensures both data safety and controlled execution.]
---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock occurs when threads wait indefinitely for resources held by each other.
Two prevention techniques:
Using try-finally blocks to always release locks
Consistent lock usage to avoid circular waiting
In my code:
I used try-finally to ensure locks and semaphores are always released.
Only one lock is used, reducing deadlock risk.]
---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I used one lock (coarse-grained) for all counters.
Reason:
Simpler implementation
Avoids complexity and deadlocks
Trade-offs:
Coarse-grained: simpler but less concurrency
Fine-grained: better performance but more complex
Since the counters are independent, fine-grained locking would provide better concurrency. However, for this assignment, simplicity and correctness were prioritized.]
---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables
**Which variables**: 
contextSwitchCount, completedProcessCount, totalWaitingTime
**Why they need protection**: 
Multiple threads update them simultaneously
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
lock.lock();
try {
    contextSwitchCount++;
} finally {
    lock.unlock();
}
**Justification**: 
Prevents lost updates and ensures consistency
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog (ArrayList)
**Why it needs protection**: 
Not thread-safe
**Synchronization mechanism used**: 
ReentrantLock
lock.lock();
try {
    executionLog.add(message);
} finally {
    lock.unlock();
}
**Justification**: 
Prevents data corruption
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
Control CPU access
**Number of permits and why**: 
1 → single CPU
**Where implemented**: 
It is implemented inside the run() method and also in the runToCompletion() method in the Process class. The semaphore is acquired before execution starts and released after the process finishes or yields the CPU.
**Code snippet**:
cpuSemaphore.acquire();
...
cpuSemaphore.release();
**Effect on program behavior**: 
Ensures only one process executes at a time
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
(repeat 5 times)
**Results**: 
Outputs are consistent
**Why synchronization is necessary**: 
Without sync → race conditions corrupt data
**Conclusion**: 
Synchronization ensures correctness
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
ConcurrentModificationException
**Results**: 
No exception occurred
**What this proves**: 
executionLog is protected
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Processes = 19
Context switches = 39
**Actual values**: 
Matches output
**Analysis**: 
Correct
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 
Different quantum
**Results**: 
Behavior changed but remained correct
**What I learned**: 
Scheduling depends on quantum
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[I learned that synchronization is very important when working with multiple threads, especially when they share the same data. Without synchronization, problems like race conditions can happen, which lead to incorrect results. I also understood how locks like ReentrantLock help protect critical sections by allowing only one thread at a time. In addition, I learned how semaphores can control access to resources, like simulating a CPU. One challenge I faced was knowing exactly where to place the lock and semaphore, but after testing I understood it better. I also realized the importance of using try-finally to avoid issues like deadlock. Overall, this assignment helped me understand how threads interact in real systems.]
---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking systems, where multiple users may try to access or update the same account balance at the same time. Synchronization is needed to prevent incorrect transactions.
**Example 2**: 
Operating systems scheduling processes on CPU, where only one process can use the CPU at a time, so synchronization is required to manage access.
---

### How I would explain synchronization to others:

[I would explain synchronization like a queue system. Imagine many people want to use the same machine, but only one person can use it at a time. So we use rules to make sure only one person enters, and others wait for their turn. In programming, threads are like those people, and shared resources are like the machine. Without synchronization, everyone tries to use it at the same time and things get messed up.]
---

## Part 6: GitHub Repository Information

**Repository URL**: 
https://github.com/ALJawharah-dosr/OS-Assignment3-Aljawharah-Aldosari/tree/main
**Number of commits**: 
8
**Commit messages**: 
add my id
Add synchronization mechanism using ReentrantLock
Add synchronization mechanism using semaphore
Protect shared counters
protect shared variable completecount usingreentrantlock
protect log ex
use semaphore to control cpu
Apply semaphore in runtocomplete
---

## Summary

**Total time spent on assignment**: 
19 hours
**Key takeaways**: 
1. Synchronization is necessary to avoid race conditions
2. Locks and semaphores serve different purposes
3. Proper placement of synchronization is critical
**Most challenging aspect**: 
Understanding where exactly to apply synchronization in the code
**What I'm most proud of**: 
Successfully implementing synchronization and getting consistent correct results
---

**End of Documentation**

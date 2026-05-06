# Assignment 3 - Complete Documentation

**Student Name**: [SARA ALBOQOMI]  
**Student ID**: [445052181]  
**Date Submitted**: [6/5/2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - April 30, 2026, 10:00 AM
**What I implemented**:
Forked the repository from the course GitHub page, renamed it, and set up the project
in Visual Studio Code. Changed the studentID variable to my actual student ID (445052181)
and made the first commit to establish the baseline.

**Challenges encountered**:
I needed to understand the existing code structure before making any changes.
I read through SchedulerSimulationSync.java carefully and identified the four TODO
comments that mark where synchronization code needs to be added.

**How I solved it**:
I read Chapter 3,5 of Silberschatz et al. (Operating System Concepts, 10th ed.)
to review mutex locks and semaphores before writing any code.

**Testing approach**:
Compiled and ran the original code to observe its baseline behavior and confirm
it runs without errors before modifications.

**Time spent**: 1.5 hours

---

### Entry 2 - April 30, 2026, 3:00 PM
**What I implemented**:
Completed Task 1. Added a ReentrantLock named `lock` to the SharedResources class
and wrapped the body of `incrementContextSwitch()`, `incrementCompletedProcess()`,
and `addWaitingTime()` with `lock.lock()` inside try-finally blocks.

**Challenges encountered**:
I initially placed the `lock.unlock()` call after the operation without a finally block.
I realized this is dangerous because if an exception occurs the lock would never
be released, causing a deadlock.

**How I solved it**:
Restructured each method to use try-finally, placing `lock.unlock()` inside
the finally block as recommended in Silberschatz et al. Chapter 6.

**Testing approach**:
Ran the program and verified that `contextSwitchCount` and `completedProcessCount`
showed correct, consistent values across multiple runs.

**Time spent**: 1 hour
---

### Entry 3 - May 1, 2026, 11:00 AM
**What I implemented**:
Completed Task 2 and Task 3. Added a separate `logLock` ReentrantLock to protect
the `executionLog` ArrayList in `logExecution()`. Then added a binary Semaphore
named `cpuSemaphore` with 1 permit. Added `cpuSemaphore.acquire()` at the start
of the `run()` method and `cpuSemaphore.release()` in the existing finally block.

**Challenges encountered**:
Understanding where exactly to place the semaphore acquire call in the `run()` method.
Since the acquire itself can throw `InterruptedException`, it needed its own
try-catch block before the main try-finally block.

**How I solved it**:
Added a separate try-catch block for the acquire call before the main execution
block, returning immediately if interrupted, as this means the process could not
access the CPU.

**Testing approach**:
Ran the program 5 times and confirmed no `ConcurrentModificationException` was
thrown and that all processes completed correctly every time.

**Time spent**: 1.5 hours
---

## Entry 4 - May 1, 2026, 4:00 PM
**What I implemented**:
Completed Task 4. Wrote the full ASSIGNMENT_DOCUMENTATION.md file, answering
all technical questions, filling in the synchronization analysis for all three
critical sections, and documenting all four test results.

**Challenges encountered**:
Formulating precise technical answers for the questions about deadlock prevention
and lock granularity required careful review of Chapter 6 terminology.

**How I solved it**:
Re-read Sections 6.5 and 6.6 of Silberschatz et al. to ensure my answers used
correct terminology and accurate definitions before writing the final answers.

**Testing approach**:
Reviewed all answers against the code to make sure every claim in the documentation
is directly supported by the actual implementation.

**Time spent**: 2 hours

---
### Entry 5 - May 2, 2026, 10:00 AM
**What I implemented**:
Final review pass. Verified the GitHub repository has minimum 4 meaningful commits
spread across different days, confirmed the repository is public, recorded and
uploaded the video demonstration, and made the final submission on Blackboard.

**Challenges encountered**:
Making sure the Google Drive video link was accessible in incognito mode before
submitting, as a private link would result in a -2 penalty.

**How I solved it**:
Opened the link in a private browser window to confirm anyone with the link
can view the video without needing to log in.

**Testing approach**:
Performed a final full run of the program and confirmed all statistics printed
correctly and all processes completed without errors.

**Time spent**: 1 hour
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

The first race condition occurs on the shared variable contextSwitchCount. Multiple threads may increment this variable simultaneously without synchronization. Since increment is not an atomic operation, one thread may overwrite the result of another, leading to incorrect counts.

The second race condition occurs in the executionLog ArrayList. Multiple threads may call executionLog.add() at the same time. Since ArrayList is not thread-safe, this may lead to data corruption or ConcurrentModificationException.

Concurrent access is a problem because threads interleave execution unpredictably. This can cause inconsistent program state and incorrect statistics such as total waiting time or number of completed processes.
---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

A ReentrantLock is used to provide mutual exclusion, ensuring that only one thread accesses a critical section at a time. In this assignment, it was used to protect shared counters and the execution log.

A Semaphore, on the other hand, controls access to a resource using permits. It allows a fixed number of threads to access a resource simultaneously.

In this code, ReentrantLock was used for protecting shared variables because we needed strict mutual exclusion. A Semaphore with one permit was used to simulate CPU access, ensuring that only one process executes at a time.
---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

Deadlock is a situation where two or more threads are waiting indefinitely for resources held by each other.

One prevention technique is using try-finally blocks to ensure that locks and semaphores are always released. This was implemented in the code to prevent threads from holding resources forever.

Another technique is avoiding nested locks or maintaining consistent lock ordering. In this assignment, only one lock is acquired at a time, which reduces the possibility of deadlock.
---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
I used a coarse-grained locking approach by applying one ReentrantLock for all three counters. This simplifies the design and ensures correctness with minimal complexity.

The advantage of coarse-grained locking is simplicity and lower risk of deadlocks. However, it reduces concurrency because threads must wait even when accessing independent variables.

Fine-grained locking would allow better concurrency by using separate locks for each variable. However, it increases complexity and the risk of deadlocks.

Although the counters are independent, I chose coarse-grained locking to prioritize correctness and simplicity in this assignment.
## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount, completedProcessCount, totalWaitingTime

**Why they need protection**: Multiple threads update these variables, causing race conditions.

**Synchronization mechanism used**: 
ReentrantLock

**Code snippet**:
```java
counterLock.lock();
try {
    contextSwitchCount++;
} finally {
    counterLock.unlock();
}```

**Justification**: Ensures mutual exclusion and prevents incorrect updates.

---

### Critical Section #2: Execution Log

**What resource**:executionLog (ArrayList)

**Why it needs protection**: ArrayList is not thread-safe.

**Synchronization mechanism used**: ReentrantLock

**Code snippet**:
```java
logLock.lock();
try {
    executionLog.add(message);
} finally {
    logLock.unlock();
}```

**Justification**: Prevents concurrent modification and ensures data integrity.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: Control CPU access

**Number of permits and why**: 1 → simulate single CPU

**Where implemented**: run() and runToCompletion()

**Code snippet**:
```java
SharedResources.cpuSemaphore.acquire();
try {
    // execution
} finally {
    SharedResources.cpuSemaphore.release();
}```

**Effect on program behavior**: Ensures only one process runs at a time, preventing conflicts.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: javac SchedulerSimulationSync.java
java SchedulerSimulationSync
(repeated 5 times)
```bash
# Commands used (run the program at least 5 times)
```

**Results**: All runs produced consistent statistics.
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 
Without locks, counters could produce incorrect values due to race conditions.
**Conclusion**: Synchronization ensures correctness.

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException when multiple threads access the shared execution log.

**Testing procedure**: 
I ran the program multiple times while observing the execution of threads that write to the shared executionLog. The test focused on scenarios where several processes were executing and logging messages concurrently.

Before adding synchronization, the executionLog (which is an ArrayList) was vulnerable to concurrent access issues. After implementing ReentrantLock around the log operations, I repeated the execution multiple times to verify stability. 

**Results**: No ConcurrentModificationException occurred.

**What this proves**: Execution log is properly synchronized.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: Completed processes = total processes

**Actual values**: Matched correctly in all runs

**Analysis**: Synchronization ensures correct final results.

---

### Test 4: Different Scenarios
**Scenario tested**: Different time quantum values

**Purpose**: Check stability

**Results**: Program remained stable

**What I learned**: Synchronization works under different conditions.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

Synchronization is essential in multithreaded systems. Locks and semaphores prevent race conditions and ensure data consistency. I learned how improper synchronization leads to unpredictable behavior.
---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Banking systems

**Example 2**: Database transactions

---

### How I would explain synchronization to others:

Synchronization ensures that shared resources are accessed safely, like allowing one person at a time to use a resource.
---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 6

**Commit messages**: 
1. Set my student ID
2. Add lock for counters
3. Add lock for execution log
4. Add semaphore
5.Use semaphore in run()
5.Use semaphore in runToCompletion()

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. Synchronization is essential in multithreaded systems to prevent race conditions and ensure data consistency.
2. Proper use of ReentrantLock and Semaphore helps control access to shared resources and simulate real CPU scheduling behavior.

**Most challenging aspect**:  The most challenging part was identifying all critical sections in the code and ensuring that every shared resource was properly protected without introducing deadlocks or unnecessary complexity.

**What I'm most proud of**: 
I am most proud of successfully implementing synchronization mechanisms that resulted in consistent and correct outputs across multiple runs, demonstrating a solid understanding of concurrency control concepts.
---

**End of Documentation**

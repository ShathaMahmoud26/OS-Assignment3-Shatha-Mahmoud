# Assignment 3 - Complete Documentation

**Student Name**: [Shatha Mahmoud Mohammed]  
**Student ID**: [444052900]  
**Date Submitted**: [Submission Date]

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

### Entry 1 - April 27, 2026

**What I implemented**: 

I cloned the assignment repository, opened it in Visual Studio Code, and changed the student ID in `SchedulerSimulationSync.java` to my actual student ID.

**Challenges encountered**: 

At first, Git required me to configure `user.name` and `user.email` before making a commit.

**How I solved it**: 

I configured Git using my name and university email, then committed the student ID change.

**Testing approach**: 

I checked that the student ID appeared correctly in the program output.

**Time spent**: 

20 minutes 

---

### Entry 2 - April 28, 2026

**What I implemented**: 

I added a `ReentrantLock` called `counterLock` to protect the shared counter variables: `contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime`.

**Challenges encountered**: 

I had a compile error because I used the variable name `waitingTime` inside the `addWaitingTime(long time)` method, but the correct parameter name was `time`.

**How I solved it**: 

I replaced `waitingTime` with `time`, then compiled and ran the program again successfully.

**Testing approach**: 

I compiled the program using `javac SchedulerSimulationSync.java` and ran it using `java SchedulerSimulationSync`.

**Time spent**: 

35 minutes

---

### Entry 3 - April 28, 2026

**What I implemented**: 

I added a separate `ReentrantLock` called `logLock` to protect the shared `executionLog` ArrayList.

**Challenges encountered**: 

The main challenge was identifying that `executionLog.add(message)` belongs to the execution log critical section, not the counter variables task.

**How I solved it**: 

I used a separate lock for the execution log and wrapped `executionLog.add(message)` inside a try-finally block.

**Testing approach**: 

I ran the program and verified that it completed without errors or `ConcurrentModificationException`.

**Time spent**: 

25 minutes

---

### Entry 4 - April 28, 2026

**What I implemented**: 

I added a binary `Semaphore` with one permit to control CPU access during process execution.

**Challenges encountered**: 

The semaphore needed to be released safely to avoid deadlock.

**How I solved it**: 

I used a `finally` block to release the semaphore after the process finished its CPU execution section.

**Testing approach**: 

I ran the program and checked that all processes completed successfully.

**Time spent**: 

30 minutes 

---

### Entry 5 - April 29, 2026

**What I implemented**: 

I completed the assignment documentation and checked the required commits.

**Challenges encountered**: 

I needed to make sure the documentation covered the technical questions, testing, reflection, and GitHub information.

**How I solved it**: 

I followed the provided documentation template and filled each required section.

**Testing approach**: 

I reviewed the repository, commit history, and program output before final submission.

**Time spent**: 

25 minutes 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:
One race condition in the original code was related to the shared counter variables such as `contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime`. These variables are updated by multiple threads, so two threads could read the same old value and write back an incorrect new value. For example, if two processes increment `completedProcessCount` at the same time, one update could be lost.

Another race condition was related to the shared `executionLog` ArrayList. ArrayList is not thread-safe, so if multiple threads add messages to it at the same time, the list could become inconsistent or cause a `ConcurrentModificationException`. This could make the execution log incomplete or incorrect.
---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
`ReentrantLock` is used to provide mutual exclusion for a critical section, meaning only one thread can enter that section at a time. I used `ReentrantLock` to protect the shared counters and the execution log because these resources are updated by multiple threads.

A `Semaphore` controls access to a limited resource using permits. I used a binary `Semaphore` with one permit to control CPU access. This means only one process can enter the CPU execution section at a time. The lock protects shared data, while the semaphore controls access to a shared resource.
---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
Deadlock happens when threads wait forever because each one is holding a resource and waiting for another resource that will not be released. One prevention technique is using `try-finally` blocks, because they make sure locks or semaphores are released even if an exception happens. Another prevention technique is avoiding unnecessary nested locks or always acquiring locks in a consistent order.

In my code, I used `try-finally` when using `ReentrantLock` and when releasing the semaphore. This helps prevent deadlocks because the lock or semaphore permit will not remain held forever.
---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
For Task 1, I used one lock called `counterLock` for all three counter variables. This is a coarse-grained locking approach. I chose this because the counter updates are simple and short, so using one lock makes the code easier to read and reduces the chance of synchronization mistakes.

The trade-off is that one lock can reduce concurrency because only one thread can update any of the three counters at a time. Separate locks would provide better concurrency because the three counters are independent. However, for this assignment, one lock is acceptable because the protected sections are very small and the design is easier to maintain. If the program were larger or had heavier counter operations, separate locks could be better.
---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
`contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime`.

**Why they need protection**: 
They are shared variables updated by multiple process threads. Without synchronization, updates can be lost or calculated incorrectly.

**Synchronization mechanism used**: 
`ReentrantLock` using `counterLock`.

**Code snippet**:
```java
private static final ReentrantLock counterLock = new ReentrantLock();

public static void incrementContextSwitch() {
    counterLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        counterLock.unlock();
    }
}

public static void incrementCompletedProcess() {
    counterLock.lock();
    try {
        completedProcessCount++;
    } finally {
        counterLock.unlock();
    }
}

public static void addWaitingTime(long time) {
    counterLock.lock();
    try {
        totalWaitingTime += time;
    } finally {
        counterLock.unlock();
    }
}

**Justification**: The lock ensures that only one thread can update the shared counters at a time, preventing lost updates and inconsistent final values.

---

### Critical Section #2: Execution Log

**What resource**: 
The shared `executionLog` ArrayList.

**Why it needs protection**: 
ArrayList is not thread-safe, so multiple threads adding log messages at the same time can cause inconsistent data or `ConcurrentModificationException`.

**Synchronization mechanism used**: 
A separate `ReentrantLock` called `logLock`.

**Code snippet**:
```java
private static final ReentrantLock logLock = new ReentrantLock();

public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}

**Justification**: The lock protects the ArrayList so only one thread can add to the log at a time.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
The semaphore controls access to the CPU execution section.

**Number of permits and why**: 
I used one permit because the assignment requires a binary semaphore, and only one process should access the CPU critical section at a time.

**Where implemented**: 
The semaphore was defined in `SharedResources` and used in the `run()` method of the `Process` class.

**Code snippet**:
```java
public static final Semaphore cpuSemaphore = new Semaphore(1);

boolean permitAcquired = false;

try {
    SharedResources.cpuSemaphore.acquire();
    permitAcquired = true;

    // process execution code

} finally {
    if (permitAcquired) {
        SharedResources.cpuSemaphore.release();
    }
}


**Effect on program behavior**: It ensures that only one process can execute in the CPU section at a time, which makes the simulation safer and more controlled.

---

## Part 4: Testing and Verification (2 marks)


## Test 1

**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

**Results**: 
The program completed successfully each time and displayed “ALL PROCESSES COMPLETED”. The output also showed synchronization statistics such as total context switches, completed processes, total waiting time, average waiting time, and total log entries.

**Why synchronization is necessary**: 
Synchronization is necessary because shared variables can be accessed by multiple threads at the same time. Without locks, counter updates could be lost. Without protecting the execution log, multiple threads could modify the ArrayList at the same time. Without the semaphore, more than one process could enter the CPU execution section at the same time.
**Conclusion**: 

---

### Test 2: Exception Testing

**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
I ran the program multiple times after adding `logLock` around `executionLog.add(message)`.

**Results**: 
No `ConcurrentModificationException` occurred, and the execution log summary appeared successfully at the end of the program.

**What this proves**: 
This proves that protecting the ArrayList with a lock prevents unsafe concurrent modifications.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values such as completed processes and statistics.

**Expected values**: 
The total completed processes should match the number of created processes. The program should also show total context switches, total waiting time, and average waiting time without incorrect or negative results.

**Actual values**: 
The program displayed “ALL PROCESSES COMPLETED” and showed the final statistics successfully.

**Analysis**: 
The final output shows that all processes finished execution. The counters were updated safely because each counter update was protected by `counterLock`. 

---

### Test 4: Different Scenarios
**Scenario tested**: Running the simulation multiple times using the same student ID seed.

**Purpose**: 
The purpose was to check that the scheduler simulation runs successfully under repeated execution.

**Results**: 
The program completed successfully and printed the process summary table and synchronization statistics.

**What I learned**: 
I learned that testing multithreaded programs requires running the program multiple times because synchronization bugs may not appear every time. 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

I learned that synchronization is very important in multithreaded programs. When multiple threads access shared variables at the same time, the final result can become incorrect. I learned that `ReentrantLock` can protect critical sections by allowing only one thread to update shared data at a time. I also learned that a `Semaphore` can control access to a limited resource, such as the CPU execution section. Another important lesson is that locks and semaphores should be released in a `finally` block. This prevents deadlock and makes the program safer. This assignment helped me understand how race conditions happen and how synchronization prevents them.
---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

Online banking systems need synchronization when two transactions try to update the same account balance at the same time.

**Example 2**: 

Ticket booking systems need synchronization when many users try to reserve the same seat at the same time.

---

### How I would explain synchronization to others:

Synchronization is like having one key for a shared room. If many people want to enter the room and change something important, only the person with the key can enter. After finishing, they return the key so another person can enter. In programming, the room is the critical section, the shared item is the shared resource, and the key is the lock or semaphore.
---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/ShathaMahmoud26/OS-Assignment3-Shatha-Mahmoud.git

**Number of commits**: 4

**Commit messages**: 
1. Set my student ID
2. Add lock for shared counters
3. Protect execution log with lock
4. Add semaphore for CPU access

---

## Summary

**Total time spent on assignment**: 
About 2.5 hours

**Key takeaways**: 
1. Race conditions happen when multiple threads access shared data without protection.
2. ReentrantLock is useful for protecting shared variables and critical sections.
3. Semaphore is useful for controlling access to limited resources.

**Most challenging aspect**: 
The most challenging part was understanding where each synchronization mechanism should be used.

**What I'm most proud of**: 
I am most proud that I was able to fix the race condition issues, run the program successfully, and understand how locks and semaphores improve thread safety. 

---

**End of Documentation**

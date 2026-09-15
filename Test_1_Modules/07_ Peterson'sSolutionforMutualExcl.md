07 - Peterson's Solution for Mutual Exclusion


The central chain of ideas is:

Shared Data → Interrupted Operations → Race Condition → Critical Section → Mutual Exclusion

1. The Main Idea of This Lesson

When multiple threads or processes execute concurrently, they may need to access the same data.

This creates a potential problem:

Thread A accesses shared data.
Thread A gets interrupted before finishing.
Thread B accesses and changes the same data.
Thread A resumes and finishes using information that may now be outdated.

The result can be incorrect and unpredictable.

Main concepts you should know

By the end of this topic, you should understand:

Shared data
Why threads and processes share data
Race conditions
Critical sections
Mutual exclusion
The four requirements of proper mutual exclusion
2. Why Do Threads and Processes Share Data?
Threads

Threads belonging to the same process automatically share the process's data and resources.

Important distinction:

Threads do not need a special mechanism to share the data belonging to their process.

The sharing is implicit (automatic).

Example

Imagine a banking application with two threads:

Thread 1: Withdraws money
Thread 2: Deposits money

Both threads access:

Account Balance

Therefore:

Thread 1 ───→ Shared Account Balance ←─── Thread 2

The account balance is considered shared data.

Processes

Processes can also share data, but unlike threads:

Processes require an explicitly created sharing mechanism.

For example:

Process A Output → Process B Input
Important comparison
Threads	Processes
Share data automatically within the same process	Must explicitly establish a sharing mechanism
Sharing is implicit	Sharing is deliberate
Same concepts of race conditions apply	Same concepts of race conditions apply
Key takeaway

The problem is not whether the programs are threads or processes.

The real problem is:

Multiple execution units accessing shared data.

3. Why Shared Data Can Cause Problems

A common mistake is thinking that an operation like:

balance = balance - 50

happens as one indivisible action.

According to this lesson, operations like withdrawing or depositing actually involve three steps:

The Load → Modify → Store Pattern
Withdrawal
1. LOAD the current balance
2. SUBTRACT the withdrawal amount
3. STORE the new balance
Deposit
1. LOAD the current balance
2. ADD the deposit amount
3. STORE the new balance

The important issue is:

A thread can be interrupted between these steps.

For example:

LOAD → MODIFY → [INTERRUPTED] → STORE

That interruption creates the opportunity for another thread to access the same data.

4. The Banking Example — Understanding the Race Condition

This is the most important example in the document.

Starting Situation

Initial account balance:

$100

Two threads operate on it:

Thread 1

Withdraw:

$50
Thread 2

Deposit:

$50
Expected result

Mathematically:

100 - 50 + 50 = 100

Therefore:

The final balance should be $100.

But because the threads execute concurrently, that may not happen.

5. Step-by-Step Race Condition

Let's follow the sequence carefully.

Step 1 — Thread 2 Starts

Thread 2 wants to deposit $50.

It loads the shared balance:

Shared Memory: $100
Thread 2 Register: $100
Step 2 — Thread 2 Modifies Its Temporary Value

Thread 2 adds $50:

Thread 2 Register:
100 + 50 = 150

Now:

Shared Memory: $100
Thread 2 Register: $150
Critical detail

Thread 2 has NOT stored the $150 back into shared memory yet.

Step 3 — Thread 2 Gets Interrupted

Before Thread 2 performs:

STORE 150

the operating system switches to Thread 1.

At this point:

Shared Memory = $100

Thread 2's $150 only exists in its temporary register.

Step 4 — Thread 1 Reads the Balance

Thread 1 loads the shared balance.

What does it see?

$100

Why?

Because Thread 2 never stored its updated value.

So now:

Thread 1 Register = $100
Step 5 — Thread 1 Performs the Withdrawal

Thread 1 calculates:

100 - 50 = 50

Now:

Thread 1 Register = $50
Step 6 — Thread 1 Stores Its Result

Thread 1 writes:

$50

back into shared memory.

Now:

Shared Memory = $50
Step 7 — Thread 2 Resumes

Thread 2 continues from where it was interrupted.

Remember:

Thread 2 Register = $150

It now performs its store operation:

STORE $150

Therefore:

Shared Memory = $150
6. The Final Problem

The final balance becomes:

$150

But it should have been:

$100
Why did this happen?

Not because the withdrawal calculation was wrong.

Not because the deposit calculation was wrong.

The problem happened because:

The threads were interleaved in an unfortunate order.

Thread 2 calculated its result based on an old value.

Thread 1 changed the shared value.

Then Thread 2 later overwrote Thread 1's change with its previously calculated result.

This is the core example of a race condition.

7. What Is a Race Condition?
Definition

A race condition occurs when:

The final result depends on the unpredictable timing or order in which threads execute.

The threads are essentially "racing" to access and modify shared data.

Important characteristic

The program might:

Run #1 → Correct
Run #2 → Incorrect
Run #3 → Correct
Run #4 → Incorrect

Even though the program code has not changed.

Why?

Because the timing and scheduling of threads may change every time the program runs.

Why Are Race Conditions Dangerous?
1. They don't always happen

The program may appear to work correctly during testing.

But under different timing:

Incorrect result!
2. They are difficult to reproduce

You might run the program:

10 times → Works
11th time → Fails

This makes debugging difficult.

3. They can silently corrupt data

The program might not crash.

Instead, it may simply produce:

Incorrect financial data
Incorrect account balances
Incorrect shared information

This is especially dangerous because the error may go unnoticed.

8. The Root Cause

The fundamental problem is:

A thread was interrupted while accessing shared data, and another thread was allowed to access that same data before the first thread finished.

The lesson's main rule is:

When one thread is accessing shared data:
Thread A accesses shared data
        ↓
Thread B must NOT access that same shared data
        ↓
Thread A finishes
        ↓
Thread B may access the data

This behavior is called:

Mutual Exclusion

9. Critical Section
Definition

A critical section (also called a critical region) is:

The specific section of code where a thread accesses shared data.

For example:

balance = balance - withdrawal;

The code responsible for:

Reading shared data
Modifying shared data
Writing shared data

would belong to the critical section.

Simple visualization
Thread Code

Non-Critical Code
       ↓
===================
  CRITICAL SECTION
  Access Shared Data
===================
       ↓
Non-Critical Code
10. Mutual Exclusion
Definition

Mutual exclusion means:

When one thread enters its critical section, another thread cannot enter its critical section if both are accessing the same shared data.

Example
Thread A:
[ ENTER Critical Section ]
         │
         │ Access Shared Data
         │
         ▼
[ LEAVE Critical Section ]


Thread B:
        WAITING...
        WAITING...
        WAITING...
              ↓
        Thread A Leaves
              ↓
[ ENTER Critical Section ]

Only one thread can access the protected shared data at a time.

11. Timeline Example: Thread A and Thread B

The document describes four important moments.

T1 — Thread A Enters
Thread A → ENTERS Critical Section

Thread A begins accessing the shared data.

T2 — Thread B Attempts to Enter
Thread A → Inside Critical Section
Thread B → Wants to Enter

Thread B cannot enter.

Therefore:

Thread B = BLOCKED / WAITING
T3 — Thread A Leaves
Thread A → FINISHED

Now Thread B is allowed to enter.

T4 — Thread B Leaves
Thread B → FINISHED

The important period is:

T2 ─────────────── T3

During this time:

Thread B is waiting.

This waiting prevents Thread B from interfering with Thread A's access to the shared data.

12. The Four Requirements of Mutual Exclusion

This is likely one of the most important sections to memorize.

Mutual exclusion is not just:

"Only one thread at a time."

A correct mutual exclusion system must satisfy four requirements.

Requirement #1: No Simultaneous Critical Section Access
Rule:

No two threads or processes may simultaneously be inside critical sections accessing the same shared data.

Meaning:
❌ NOT ALLOWED:

Thread A → Access Shared Data
Thread B → Access Shared Data
         AT THE SAME TIME

Instead:

✅ ALLOWED:

Thread A → Access
Thread A → Finish

Thread B → Access
Thread B → Finish
Purpose

This requirement directly prevents:

Race Conditions
Requirement #2: No Hardware Assumptions
Rule:

The solution must work regardless of the underlying hardware.

This means the mutual exclusion solution should not depend on:

The number of processors
Processor speed
Specific hardware configurations
Example

A solution should work on:

1 Processor
4 Processors
16 Processors
Different processor speeds

The algorithm should still provide correct mutual exclusion.

Main idea

A synchronization solution should be generally correct, not dependent on a particular machine setup.

Requirement #3: Threads Outside Their Critical Section Cannot Block Others

This requirement can be confusing, so let's simplify it.

Rule:

A thread that is NOT in its critical section should not prevent another thread from entering its critical section.

Example

Suppose:

Thread A → Doing unrelated work
Thread B → Wants shared data

Thread A is not currently accessing the shared data.

Therefore:

Thread A should NOT block Thread B.

Only a thread that is actually using the critical section should cause another thread to wait.

Incorrect behavior:
Thread A → Outside Critical Section
Thread B → WAITING anyway ❌
Correct behavior:
Thread A → Outside Critical Section
Thread B → May Enter Critical Section ✅
Requirement #4: No Indefinite Waiting
Rule:

No thread or process should have to wait forever to enter its critical section.

This is a fairness requirement.

Imagine:

Thread A → Wants Access
Thread B → Wants Access
Thread C → Wants Access

Thread A should not be permanently ignored:

Thread B → Access
Thread C → Access
Thread B → Access
Thread C → Access
Thread B → Access

Thread A → Still waiting forever ❌

That is unacceptable.

Every thread should eventually get an opportunity to enter.

The document describes indefinite unfair waiting as starvation.

13. The Four Requirements — Quick Memorization Version

Try remembering them in this order:

1. One at a Time

No simultaneous access to the same shared data.

2. Hardware Independent

The solution cannot depend on specific hardware assumptions.

3. Don't Block Unnecessarily

A thread outside its critical section cannot stop others.

4. Nobody Waits Forever

Every waiting thread must eventually get its opportunity.

Super-short memory phrase:

One — Any Hardware — Don't Block — Eventually Enter

Or conceptually:

SAFETY
↓
HARDWARE INDEPENDENCE
↓
PROGRESS
↓
FAIRNESS
14. Complete Concept Map
MULTIPLE THREADS / PROCESSES
            │
            ▼
       SHARED DATA
            │
            ▼
   LOAD → MODIFY → STORE
            │
            │ Can be interrupted!
            ▼
   THREAD EXECUTION INTERLEAVES
            │
            ▼
      RACE CONDITION
            │
            ▼
     INCORRECT RESULTS
            │
            ▼
    IDENTIFY CRITICAL SECTION
            │
            ▼
      MUTUAL EXCLUSION
            │
            ▼
 ┌──────────────────────────┐
 │  4 REQUIREMENTS          │
 │                          │
 │  1. No simultaneous      │
 │     critical access      │
 │                          │
 │  2. Hardware independent │
 │                          │
 │  3. No unnecessary       │
 │     blocking             │
 │                          │
 │  4. No indefinite        │
 │     waiting              │
 └──────────────────────────┘
15. Important Terminology
Shared Data

Data that can be accessed by more than one thread or process.

Race Condition

A problem where:

The program's final result depends on the unpredictable timing or execution order of threads.

Critical Section / Critical Region

The part of a program where shared data is accessed.

Mutual Exclusion

The rule that:

Only one thread or process can access the same shared data in its critical section at a time.

Blocked

A thread is temporarily prevented from entering its critical section and must wait.

Preempted / Interrupted

A thread stops executing temporarily because the system switches execution to another thread.

Starvation

When a thread waits indefinitely and repeatedly does not get an opportunity to enter its critical section.

16. The Most Important Difference to Understand
Race Condition

This is the problem.

Threads interfere with each other
        ↓
Incorrect result
Critical Section

This is the location where the danger exists.

The part of the code accessing shared data
Mutual Exclusion

This is the rule used to prevent the problem.

Only one thread enters at a time
Think of it like this:

Race Condition = The Problem
Critical Section = Where the Problem Happens
Mutual Exclusion = The Protection

17. Banking Example — Final Summary
Initial State
Balance = $100
Thread 1
Withdraw $50
Thread 2
Deposit $50
Correct Mathematical Answer
100 - 50 + 50 = 100
What Goes Wrong?
Thread 2 reads $100
Thread 2 calculates $150
Thread 2 gets interrupted

Thread 1 reads $100
Thread 1 calculates $50
Thread 1 stores $50

Thread 2 resumes
Thread 2 stores its old calculation of $150
Final Result
$150 ❌
Cause
RACE CONDITION
Solution Concept
MUTUAL EXCLUSION
18. On-the-Go Review Sheet
Remember This Sequence:
Shared Data

⬇

Multiple threads need the same information.

Load → Modify → Store

⬇

These operations can be interrupted.

Thread Interleaving

⬇

Another thread accesses the same data before the first finishes.

Race Condition

⬇

The final answer becomes dependent on unpredictable timing.

Critical Section

⬇

Identify the code accessing shared data.

Mutual Exclusion

⬇

Allow only one thread into the critical section at a time.

Four Requirements

⬇

No simultaneous access.
No hardware assumptions.
Don't block threads unnecessarily.
No indefinite waiting.
Final Exam-Level Understanding

If you can explain this statement in your own words, you understand the lesson:

A race condition occurs when multiple threads access shared data and the result depends on the unpredictable order in which their operations execute. Because operations such as load, modify, and store can be interrupted, one thread can interfere with another's update. The shared-data code is called a critical section, and mutual exclusion ensures that only one thread accesses that shared data in its critical section at a time. A proper mutual exclusion solution must prevent simultaneous access, avoid hardware assumptions, avoid unnecessary blocking, and guarantee that no thread waits forever.
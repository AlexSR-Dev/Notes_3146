08 - Mutual Exclusion Solutions: Stric Alternation

This document introduces Strict Alternation, the first software-based solution for controlling access to shared resources. The most important thing to understand is not just how it works, but why it succeeds at mutual exclusion and why it still fails as a practical solution.

1. Central Idea of the Document

When two threads share a resource, they must not enter the relevant critical regions at the same time.

Strict alternation solves this by enforcing:

Thread 0 goes → Thread 1 goes → Thread 0 goes → Thread 1 goes → ...

The threads must strictly take turns.

It uses a shared variable called:

turn

to determine which thread is currently allowed to enter its critical region.

Big picture
Shared Resource
      ↓
Need Mutual Exclusion
      ↓
Strict Alternation
      ↓
Shared variable: turn
      ↓
Only the thread whose turn it is may enter
2. Race Condition
Definition

A race condition occurs when multiple threads access a shared resource in an unsafe overlapping manner, causing:

corrupted data
unpredictable behavior
incorrect results

The underlying issue is that one thread can be interrupted while working with shared data, allowing another thread to interfere.

Connection to Part 5

You should connect this immediately to the previous document:

Race condition = the problem
Mutual exclusion = the protection

Strict alternation is one possible attempt at providing that protection.

3. Critical Region
Definition

The critical region is the section of code where a thread accesses the shared resource.

It is equivalent to the critical section terminology from Part 5.

Important

When studying this topic, mentally connect:

Critical region
      =
Critical section

The document uses critical region, but the underlying idea is the same: this is the code that must be protected from simultaneous access.

4. Mutual Exclusion
Definition

Mutual exclusion is the guarantee that:

Only one thread can be inside its critical region at a time.

That means:

Thread 0 → Critical Region ✅
Thread 1 → Critical Region ❌

Thread 1 must wait until Thread 0 leaves.

This is the property Strict Alternation is designed to guarantee.

5. Strict Alternation
Definition

Strict alternation is a software-based mutual exclusion solution in which two threads are forced to enter their critical regions in alternating order.

The method:

uses ordinary program logic
uses a shared variable
does not depend on special hardware instructions
does not require special operating-system support

Central rule
Thread 0
   ↓
Thread 1
   ↓
Thread 0
   ↓
Thread 1
   ↓
...

Neither thread is allowed to go twice in a row.

6. The Turn Variable

The entire mechanism revolves around one shared variable:

turn

The value identifies which thread currently has permission to enter its critical region.

Meaning of each value
turn == 0
    ↓
Thread 0 may enter

turn == 1
    ↓
Thread 1 may enter

This is the central mechanism behind Strict Alternation.

7. Structure of Each Thread

Each thread repeatedly performs three conceptual parts:

1. Waiting

The thread checks:

"Is it my turn?"

2. Critical region

If it is the thread's turn, it accesses the shared resource.

3. Non-critical region

Afterward, it performs ordinary work that does not require the shared resource.

Pattern
WAIT
 ↓
CRITICAL REGION
 ↓
HAND OVER TURN
 ↓
NON-CRITICAL REGION
 ↓
REPEAT

This pattern is extremely important for understanding the code.

8. Thread 0's Code

The document gives this structure:

while (1) {
    while (turn != 0);
    critical_region_0();
    turn = 1;
    non_critical_region_0();
}

Let's break it down.

Line 1
while (1)

The thread repeats indefinitely.

Line 2
while (turn != 0);

Thread 0 waits until:

turn == 0

Only then can it proceed.

Line 3
critical_region_0();

Thread 0 accesses the shared resource.

Line 4
turn = 1;

Thread 0 gives permission to Thread 1.

Think:

"I'm finished. Now it's your turn."

Line 5
non_critical_region_0();

Thread 0 performs work unrelated to the shared resource.

The document gives the corresponding Thread 1 structure, where Thread 1 waits for turn == 1 and then changes it back to 0 when finished.

9. Thread 1's Code
while (1) {
    while (turn != 1);
    critical_region_1();
    turn = 0;
    non_critical_region_1();
}

The logic is the mirror image of Thread 0.

Thread 1:
Wait for turn == 1
       ↓
Enter critical region
       ↓
Set turn = 0
       ↓
Do non-critical work
Important pattern
Thread 0:
wait for 0 → work → set 1

Thread 1:
wait for 1 → work → set 0

That is exactly what forces the strict back-and-forth behavior.

10. Busy Waiting

This is one of the most important terms in this document.

Consider:

while (turn != 0);

Notice:

;

There is no body.

The thread simply keeps asking:

"Is turn 0 yet?"
"Is turn 0 yet?"
"Is turn 0 yet?"
"Is turn 0 yet?"
...

It checks repeatedly until the condition becomes false.

Definition

Busy waiting means:

A thread repeatedly checks a condition while waiting, consuming processor resources instead of sleeping or yielding.

Visualize it
CPU
 ↓
Check turn
 ↓
Not my turn
 ↓
Check again
 ↓
Check again
 ↓
Check again
 ↓
...

The thread is waiting, but it is actively using CPU cycles while waiting.

11. Walking Through Strict Alternation

Assume:

turn = 0
Step 1

Thread 0 checks:

turn == 0

So Thread 0 enters its critical region.

Step 2

Thread 0 gets interrupted while still inside its critical region.

The operating system switches to Thread 1.

Step 3

Thread 1 checks:

turn == 0

But Thread 1 needs:

turn == 1

Therefore Thread 1 cannot enter.

It busy-waits.

Step 4

Thread 0 gets scheduled again.

It finishes its critical region and executes:

turn = 1;

Now Thread 1's condition becomes true.

Step 5

Thread 1 is eventually scheduled.

It sees:

turn == 1

and enters its critical region.

Result

At no point did both threads occupy their critical regions simultaneously.

Therefore:

Strict Alternation successfully maintains mutual exclusion.

12. Main Advantage of Strict Alternation
Mutual Exclusion Works

This is the major strength of the method.

Strict Alternation actually prevents both threads from being inside their critical regions simultaneously.

Even if Thread 0 gets interrupted:

Thread 0 → inside critical region
Thread 0 → interrupted
Thread 1 → tries to enter
Thread 1 → blocked

Thread 1 cannot proceed until Thread 0 changes turn.

Therefore:

No simultaneous critical-region access
        ↓
Race condition prevented

13. Major Disadvantage #1 — Busy Waiting

The first major problem is:

It wastes processor time.

Suppose:

turn = 1

but Thread 0 is scheduled.

Thread 0 cannot enter its critical region.

So it does:

while (turn != 0);

over and over.

It is:

using CPU cycles
accomplishing no useful work
repeatedly checking the same variable

Meanwhile, Thread 1 is the thread that could actually make progress.

Key term

Busy waiting = waiting while continuously consuming CPU resources.

14. Major Disadvantage #2 — Strict Alternation Is Too Strict

This is arguably the most important conceptual weakness.

Strict Alternation forces:

Each thread to wait for the other thread's turn, even when the other thread doesn't need its turn.

This can cause unnecessary blocking.

15. Example of Unnecessary Blocking

Suppose:

Thread 0 gets its turn
    ↓
Thread 0 enters critical region
    ↓
Thread 0 sets turn = 1

Now Thread 0 goes and performs its non-critical work.

Suppose Thread 1 eventually takes its turn:

Thread 1 enters critical region
Thread 1 sets turn = 0

Now Thread 1 wants to enter its critical region again.

But:

turn == 0

So Thread 1 must wait.

Why?

Because Thread 0 has not yet taken its next turn.

But what if Thread 0 is:

busy doing non-critical work

or:

doesn't need to enter its critical region yet

Thread 1 is still forced to wait.

That means:

Shared resource = AVAILABLE
Thread 1 = READY
Thread 0 = NOT INTERESTED
              ↓
Thread 1 still must WAIT ❌

This is extremely inefficient.

16. Connection to the Four Mutual Exclusion Requirements

This is where this document connects directly to Part 5.

A good mutual exclusion solution must satisfy several requirements.

Strict Alternation succeeds at one major requirement:

✅ No simultaneous critical-section access

It guarantees mutual exclusion.

But it violates an important requirement:

❌ A non-critical thread can still block another thread

Remember the Part 5 rule:

A thread that isn't in its critical section should not be able to prevent another thread from entering theirs.

Strict Alternation fails here.

Why?

Because turn determines access based on whose turn it is, rather than whether that thread actually needs access.

So:

Thread A = not interested
Thread B = needs shared resource

Strict Alternation:
Thread B → WAIT ❌

This is the fundamental design flaw.

17. Why Strict Alternation Is Not Practical

The method looks attractive at first:

Simple
+
Software-only
+
Mutual exclusion works

But then:

Busy waiting
+
Unnecessary blocking
=
Poor practical solution

The result is that Strict Alternation is useful for understanding synchronization concepts, but not a practical real-world mutual exclusion strategy.

18. What Happens With More Threads?

Strict Alternation is already awkward with two threads.

Now imagine:

Thread 0
Thread 1
Thread 2
Thread 3
...

Maintaining a strict, fixed order becomes increasingly complicated.

The existing problems also become worse:

More threads
     ↓
More coordination
     ↓
More waiting
     ↓
More unnecessary blocking
     ↓
Greater complexity

The document emphasizes that busy waiting and unnecessary blocking become more pronounced as the number of competing threads increases.

19. Important Definitions — Fast Reference
Term	Definition
Race condition	When overlapping thread execution causes unpredictable or corrupted results
Mutual exclusion	Guarantee that only one thread enters the relevant critical region at a time
Critical region	Code where a thread accesses a shared resource
Strict alternation	A software-based method forcing two threads to take turns entering critical regions
turn	Shared variable indicating which thread may enter
Busy waiting	Continuously checking a condition while waiting, consuming CPU resources
Non-critical region	Code that does not access the shared resource
20. The Three Most Important Code Ideas

Memorize these patterns:

Thread 0
while (turn != 0);
critical_region_0();
turn = 1;
non_critical_region_0();
Thread 1
while (turn != 1);
critical_region_1();
turn = 0;
non_critical_region_1();
Interpretation
WAIT
 ↓
ENTER
 ↓
USE RESOURCE
 ↓
HAND TURN TO OTHER THREAD
 ↓
DO OTHER WORK
21. Exam-Oriented Mental Model

When you see Strict Alternation, immediately think:

What is it?
Software solution
What does it use?
Shared variable: turn
What does turn control?
Which thread may enter
What does it guarantee?
Mutual exclusion
How does a thread wait?
Busy waiting
What is its first major flaw?
Wastes CPU cycles
What is its second major flaw?
Unnecessarily forces alternation
What requirement does it violate?
A thread outside its critical region
should not block another thread
Is it practical?
No
22. Part 5 → Part 6 Connection

This is especially important for your course progression.

Part 5 taught:
Shared Data
    ↓
Race Condition
    ↓
Critical Section
    ↓
Mutual Exclusion
Part 6 asks:

"Okay, how can we actually enforce mutual exclusion?"

The first answer is:

STRICT ALTERNATION

But then we discover:

Strict Alternation
       ↓
✅ Mutual exclusion works
       ↓
❌ Busy waiting
       ↓
❌ Unnecessary blocking
       ↓
❌ Not practical

So this document is essentially teaching you:

The first solution can solve the core problem while still failing the broader requirements of a good synchronization mechanism.

23. The Entire Document in One Flow
MULTIPLE THREADS
       ↓
SHARED RESOURCE
       ↓
RACE CONDITION POSSIBLE
       ↓
NEED MUTUAL EXCLUSION
       ↓
STRICT ALTERNATION
       ↓
Shared variable: turn
       ↓
Thread 0 waits for 0
Thread 1 waits for 1
       ↓
One enters critical region
       ↓
Changes turn
       ↓
Other enters
       ↓
MUTUAL EXCLUSION ✅
       ↓
BUT...
       ↓
BUSY WAITING ❌
       ↓
CPU WASTED
       ↓
AND...
       ↓
FORCED ALTERNATION ❌
       ↓
UNNECESSARY BLOCKING
       ↓
NOT PRACTICAL
24. On-the-Go Final Notes
Strict Alternation

A software-based mutual exclusion technique where threads are forced to take turns.

turn

Shared variable determining who is allowed to enter.

Busy Waiting

A thread repeatedly checks turn instead of sleeping, wasting CPU cycles.

Main Advantage

It does guarantee mutual exclusion.

Main Disadvantage #1

It wastes CPU time through busy waiting.

Main Disadvantage #2

It forces strict turn-taking, even when the other thread doesn't need the resource.

Most Important Failed Requirement

A thread that is outside its critical region can still block another thread.

Bottom Line

Strict Alternation is correct for mutual exclusion, but inefficient and overly restrictive, making it impractical for real systems.
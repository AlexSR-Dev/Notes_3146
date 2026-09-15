Processes and Threads - Part 8:
- Avoiding Busy Waiting with Sleep, Wake Up, and Mutex Locks:

1. The Problem: Busy Waiting
- A thread/process that cannot enter its critical region repeatedly checks the lock instead of giving up the CPU. This wastes CPU resources because the thread performs no useful work while waiting.

Simplified Form:
while (lock == locked) {
    keep checking...
}
- Thus, as the thread is waiting it STILL consumes the CPU time and blocked from running.


EX:
• Thread A is insdie the critical region.
• Thread B want to enter.
• Thread B cannot enter yet.

- Busy Waiting:
Thread B:
    "Can I enter?"
    No.
    "Can I enter?"
    No.
    "Can I enter?"
    No.
    ...

- Thread B isn't doing anything useful but wasting the CPU resources on checking/polling rather than productive execution.

---------------------------------------------------------------------------------------------------------------------------------

2. Alternative: Sleep and Wake Up
- If a thread/process cannot enter its critical region, it can be blocked/suspended instead of continuously checking. When it becomes eligible to proceed, it is awakened and resumes execution.

Basic sequence EX:
Thread wants critical region
          ↓
     Is it available?
       /        \
     YES         NO
      ↓           ↓
   Enter       Sleep/block
                 ↓
          Critical region
             becomes free
                 ↓
             Wake up
                 ↓
          Try to proceed

- "sleep" refers the thread as blocked/suspended from exeuction.
- Thus, no longer actively consumes CPU cycles waiting for the resource.

---------------------------------------------------------------------------------------------------------------------------------

3. Why Sleep/Wake is Better Than Busy Waiting:
- Sleep/wake changes "how the waiting happens", and not decision whether the resource is available.

---------------------------------------------------------------------------------------------------------------------------------

4. Mutex:
- Mutual Exclusion
- A shared variable used to enforce mutual exclusion. It has two states: 0 = unlocked and 1 = locked. A thread must acquire the mutex before entering the critical region and release it afterward.
- Unlocked (0)
- Locked (1)


- Serves the same purpose as enter_region() and leave_region() functions.
However, the mutex approach does not rely on continuously busy-waiting.

---------------------------------------------------------------------------------------------------------------------------------

5. mutux_lock()
- Before entering the critical region mutex_lock() is called.

pseudocode:
mutex_lock:
    TSL REGISTER, MUTEX
    CMP REGISTER, #0
    JZE ok
    CALL thread_yield
    JMP mutex_lock

ok:
    RET

EXP:
TSL REGISTER, MUTEX

- TSL (Test-and-Set Lock) performs two operations atomically (as a single step):
    1. Copy the current value of MUTEX into REGISTER.
    2. Set MUTEX to 1.

TSL (Test-and-Set Lock) atomically copies the mutex's old value into a register and sets the mutex to 1. Atomicity prevents another thread/process from intervening between the check and the lock operation.

EX:
Understanding the TSL Result:
If MUTEX = 0, before TSL.

After: TSL REGISTER, MUTEX

Result:
REGISTER = 0
MUTEX = 1

- Thus, the mutex was unlocked and sucessfully claimed.



EXP:
CMP REGISTER, #0

- Reads "Was the mutex's previous value 0?"
- Thus, determines whether the mutex was unlocked before TSL performed its operation.

THUS:
REGISTER == 0
means:
Lock was available

OR

REGISTER == 1
means:
Lock was already occupied



EXP:
JZE ok

- Reads "Jump if Zero"
If the comparison determines that: REGISTER == 0
Then execution jumps to:
ok:
    RET

- The thread has successfully acquired the mutex.
Then, mutex = 1 and the thread can enter the critical region.

- If mutex = 1, then the thread calls: CALL thread_yield.



EXP:
thread_yield()

- The key to avoiding busy waiting.
- Means that the current thread voluntarily gives up the CPU.

The scheduler can then run another thread/process.

THUS:
Sleep/wake is the general strategy; this mutex implementation uses thread_yield() to avoid continuously consuming CPU while waiting.



EXP:
JMP mutex_lock

- When the thread eventually gets scheduled again, it starts the lock acquisition process again.
Overall cycle:
Attempt lock
     ↓
Is it free?
   /     \
 YES      NO
 ↓         ↓
Acquire   yield CPU
lock       ↓
 ↓       rescheduled
Enter      ↓
critical ← retry
region


"Isn't retrying the lock still busy waiting?"
- Not necessarily, as the thread eventually retry, but isn't continuously executing the checking loop while waiting.
Thus Yielding approach:
check
→ yield
→ other thread runs
→ get scheduled later
→ check again
→ yield if necessary

- Doesn't continuously consume CPU resources for the waiting thread.



EXP:
ok: RET

- If mutex was found unlocked, execution reaches this label and returns to the caller.
Thus the thread acquires the lock and safely proceed into its critical region.

---------------------------------------------------------------------------------------------------------------------------------

6. mutex_unlock()

- Once the thread finishes using the critical region it calls:
mutex_unlock:
    MOVE MUTEX, #0
    RET


This just changes:
MUTEX = 1
To:
MUTEX = 0

FOR: MOVE MUTEX, #0

RET
- The function returns control to the caller

- Thus the critical region is now available.


Complete Critical-region Pattern:
mutex_lock()

      ↓

Critical Region

      ↓

mutex_unlock()


Explicitly:
             mutex_lock()
                  ↓
        ┌─────────────────┐
        │ acquire mutex   │
        └─────────────────┘
                  ↓
        ┌─────────────────┐
        │ CRITICAL REGION │
        └─────────────────┘
                  ↓
        ┌─────────────────┐
        │ release mutex   │
        └─────────────────┘
                  ↓
             continue

---------------------------------------------------------------------------------------------------------------------------------

7. Everything Together:

1.
Initial state
MUTEX = 0

Thread A wants to enter.

Thread A
TSL REGISTER, MUTEX

Result:

REGISTER = 0
MUTEX = 1

Since:

REGISTER == 0

Thread A enters the critical region.


2.
Thread B arrives

Thread A is still inside.

Therefore:
MUTEX = 1

Thread B executes TSL:
REGISTER = 1
MUTEX = 1

Since:
REGISTER != 0

Thread B cannot enter.

So:
thread_yield()

Thread B gives up the CPU.


3.
Thread A finishes

Thread A calls:
mutex_unlock()

which does:
MUTEX = 0

Now the critical region is available.



4.
Thread B gets scheduled

Thread B jumps back to:
mutex_lock

and tries again.

Now:
MUTEX = 0

TSL produces:
REGISTER = 0
MUTEX = 1

Thread B successfully acquires the mutex and enters the critical region.


---------------------------------------------------------------------------------------------------------------------------------

7. Conceptual Chain:

Busy waiting → wastes CPU → alternative is sleep/block → thread waits without consuming CPU → mutex provides mutual exclusion → TSL 
atomically tests and sets the mutex → if available, thread enters critical region → if locked, thread yields CPU → later retries → after 
critical region, mutex is reset to 0.


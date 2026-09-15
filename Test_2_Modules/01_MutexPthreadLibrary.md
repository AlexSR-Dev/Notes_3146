Processes and Thread - Part 9:

Using Mutexes with the Pthread Library:
Race condition → critical section → mutex → lock → perform shared-data operation → unlock



1. Why Do We Need a Mutex?

- When multiple threads execute concurrently and access the same shared data, they can interfere with one another.

Resulting in:
Race condition — a situation where the result depends on the timing/order in which threads access shared data.

Shared data: Data that multiple threads can access/modify.
Critical section: Code that accesses or modifies shared data and therefore must be protected from simultaneous execution by multiple threads.


- Mutex ensures that only one thread at a time can enter the critical section.
Patttern:
Thread wants shared data
        ↓
   lock mutex
        ↓
   critical section
        ↓
  unlock mutex


- Mutex provides mutual exclusion.

---------------------------------------------------------------------------------------------------------------------------------

2. Basic Mutex Strategy:

- Create a mutex shared by the threads.
- Lock the mutex before entering the critical section.
- Access/modify the shared data.
- Unlock the mutex immediately afterward.

NOTE the following pattern to RECALL:
pthread_mutex_lock(&mutex);

// critical section
// access shared data here

pthread_mutex_unlock(&mutex);

---------------------------------------------------------------------------------------------------------------------------------

3. Declaring a Pthread Mutex

The pthread library provides a special data type:
pthread_mutex_t

A mutex can be declared and initialized using:
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;



EXP:
pthread_mutex_t
- Data type specifically used for pthread mutex variables.


EXP:
mutex
- Name of the variable.


EXP:
PTHREAD_MUTEX_INITIALIZER
- pthread-provided macro that initializes the mutex with its default attributes.

---------------------------------------------------------------------------------------------------------------------------------

4. Why Does the Mutex Need Initialization?

- Mutex isn't just an ordinary integer varuiable that be used immediately.
It has an internal state that must be properly established before threads use it.

- Thus initialization ensures the pthread library knows the mutex's initial state and configuration.

A pthread mutex must be initialized before use. PTHREAD_MUTEX_INITIALIZER provides the standard/default initialization for a mutex variable.

---------------------------------------------------------------------------------------------------------------------------------

5. pthread_mutex_lock()

int pthread_mutex_lock(pthread_mutex_t* p_mutex);
- Is called before entering the critical section.

Two important possibilities:

Case 1 - Mutex is free
If the mutex is unlocked:
Thread calls lock
       ↓
Mutex available
       ↓
Thread acquires mutex
       ↓
Enter critical section

The thread proceeds immediately.



Case 2 - Mutex is already locked
If another thread already owns the mutex:
Thread calls lock
       ↓
Mutex already locked
       ↓
Thread blocks/waits
       ↓
Other thread unlocks
       ↓
Waiting thread eventually acquires mutex
       ↓
Enter critical section


- The pthread implementation therefore blocks the waiting thread rather than allowing it to continuously consume CPU checking the mutex.


pthread_mutex_lock(&mutex);
- If the mutex isn't available, the calling thread blocks.

---------------------------------------------------------------------------------------------------------------------------------

6. Why Does pthread_mutex_lock() Take &mutex?

The function expects a pointer to the mutex:
pthread_mutex_t*

Therefore:
&mutex'

- Means "The memory address of the mutex variable."
- Every thread must operate on the same mutex object.

Conceptually:
                SAME MUTEX
                    ↓
       ┌────────────┴────────────┐
       ↓                         ↓
 Deposit thread            Withdraw thread
       ↓                         ↓
 lock(&mutex)               lock(&mutex)
       ↓                         ↓
 shared account_balance

 ---------------------------------------------------------------------------------------------------------------------------------

7. Return Value of pthread_mutex_lock()

- pthread_mutex_lock() returns an integer status code. 0 indicates successful completion; a nonzero value indicates an error.

 ---------------------------------------------------------------------------------------------------------------------------------

8. pthread_mutex_unlock()

- The second major operation:

int pthread_mutex_unlock(pthread_mutex_t* p_mutex);

- Is called after the critical section is finished.
Its purpose is to release the mutex so another waiting thread can acquire it.


Basic Pattern:
pthread_mutex_lock(&mutex);

// critical section

pthread_mutex_unlock(&mutex);


NOTE:
Lock before the critical section. Unlock after the critical section.

---------------------------------------------------------------------------------------------------------------------------------

9. Lock/Unlock Summary:

Function	                    When called	                        Purpose
pthread_mutex_lock(&mutex)	    Before critical section	            Acquires mutex; blocks if unavailable
pthread_mutex_unlock(&mutex)	After critical section	            Releases mutex

Both functions:
- Receive a pointer/address to the mutex.
- Return an int status code.
- 0 indicates success.

---------------------------------------------------------------------------------------------------------------------------------

10. Bank Account Example:

There are two threads:
Deposit thread
      +
Withdraw thread
      ↓
same account_balance



Starting Balance:
$100

Deposit:
+$50

Withdrawal:
-$60

Expected Final Balance:
$90

---------------------------------------------------------------------------------------------------------------------------------

11. Global Variables

Program:
int account_balance;
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;


account_balance
- This is shared data.
Both threads need access to the same variable, so it is global.

mutex
- A mutex protecting access to "account_balance".


Both threads must use this same mutex:
account_balance
      ↑
 protected by
      ↓
    mutex

---------------------------------------------------------------------------------------------------------------------------------

12. The main() Function

The main thread creates and manages the worker threads.

Structure:
int main() {
    pthread_t id1, id2;
    int d, w;
    int* pa;

    account_balance = 100;

    d = 50;
    pa = &d;
    pthread_create(&id1, NULL, deposit, (void*)pa);

    w = 60;
    pa = &w;
    pthread_create(&id2, NULL, withdraw, (void*)pa);

    pthread_join(id1, NULL);
    pthread_join(id2, NULL);

    cout << "Final balance: $" << account_balance << endl;

    pthread_exit(NULL);
}

---------------------------------------------------------------------------------------------------------------------------------

13. pthread_t

- These variables hold the identifiers for the two threads.
Conceptually:
id1 → deposit thread
id2 → withdraw thread

- Later used with pthread_join().

---------------------------------------------------------------------------------------------------------------------------------

14. pthread_create()

The deposit thread is created with:
pthread_create(&id1, NULL, deposit, (void*)pa);

The withdrawal thread is created similarly:
pthread_create(&id2, NULL, withdraw, (void*)pa);

- pthread_create() launches a new thread that executes the specified thread function.
Thus, program passes the amount to desposite/withdraw through the function's argument.

---------------------------------------------------------------------------------------------------------------------------------

15. Passing Arguments to a Thread

The program does:
int d, w;
int* pa;

d = 50;
pa = &d;
pthread_create(..., (void*)pa);


Then:
w = 60;
pa = &w;
pthread_create(..., (void*)pa);

- "pa" points to the amount that the thread should use.
Inside the thread function, the generic "void *" argument is converted back to an "int *"

Leading to:
int amount = *((int*)arg);

---------------------------------------------------------------------------------------------------------------------------------

16. Understanding void*

The pthread thread-function requirement is:
void* function(void* arg)

So the argument arrives as:
void* arg

- A void * is a generic pointer.
The program knows that the actual object being pointed to is an int, so it converts it:
(int*)arg

Then dereferences it:
*((int*)arg)

Therefore:
int amount = *((int*)arg);


MEANS:
Take the generic pointer, treat it as a pointer to an integer, follow that pointer, and retrieve the integer value.

void* arg
   ↓
(int*)arg
   ↓
pointer to int
   ↓
*((int*)arg)
   ↓
actual integer value

---------------------------------------------------------------------------------------------------------------------------------

17. The Deposit Function

The deposit thread:
void* deposit(void* arg) {
    int amount = *((int*)arg);

    cout << "Depositing $" << amount << endl;

    pthread_mutex_lock(&mutex);

    account_balance += amount;

    pthread_mutex_unlock(&mutex);

    pthread_exit(NULL);
}

Important part:
pthread_mutex_lock(&mutex);

account_balance += amount;

pthread_mutex_unlock(&mutex);

---------------------------------------------------------------------------------------------------------------------------------

18. Identifying the Critical Section

In the deposit function:
account_balance += amount;

- Is the critical section because it modifies shared data.


The mutex surrounds it:
pthread_mutex_lock(&mutex);

account_balance += amount;

pthread_mutex_unlock(&mutex);


Therefore:
LOCK
  ↓
modify shared data
  ↓
UNLOCK

---------------------------------------------------------------------------------------------------------------------------------

19. The Withdraw Function

Follows the same structure:
void* withdraw(void* arg) {
    int amount = *((int*)arg);

    cout << "Withdrawing $" << amount << endl;

    pthread_mutex_lock(&mutex);

    account_balance -= amount;

    pthread_mutex_unlock(&mutex);

    pthread_exit(NULL);
}


With the slight difference:
account_balance -= amount;


Instead of:
account_balance += amount;

---------------------------------------------------------------------------------------------------------------------------------

20. Why Must Both Function Use the Same Mutex?

The deposit function uses:
pthread_mutex_lock(&mutex);

The withdraw function also uses:
pthread_mutex_lock(&mutex);


They are protecting the same shared variable:
account_balance

- Thus they must use the same mutex.
Shared mutex is what connects the two functions and prevents both critical sections from occurring simultaneously.



If both threads had separate mutexes, then the locks wouldn't coordinate with one another.
Both could modify: account_balance, at the same time, so the protection would fail.


NOTE:
Threads accessing the same shared resource must use the same mutex if that mutex is intended to protect that resource.

---------------------------------------------------------------------------------------------------------------------------------

21. What Happens If Both Threads Start at the Same Time?

Suppose deposit and withdrawal both attempt to execute their critical sections at nearly the same time.
Only ONE can successfully acquire the mutex first:
Deposit thread
      ↓
pthread_mutex_lock()
      ↓
gets mutex
      ↓
deposit $50


Meanwhile:
Withdraw thread
      ↓
pthread_mutex_lock()
      ↓
mutex already locked
      ↓
BLOCKED


After desposit finishes:
pthread_mutex_unlock()

The withdrawal thread can then acquire the mutex and perform its update.

---------------------------------------------------------------------------------------------------------------------------------

22. Why Does the Final Balance Always Equal $90?

The initial balance is:
$100

Deposit:
+$50

Withdrawal:
-$60

Therefore:
$100 + $50 - $60 = $90

- The important note isn't that deposit happens first.
The mutex guarantees that the two modifications cannot overlap/interleave in a way that corrupts the shared balance.

The operating system can decide:
Deposit first

or:
Withdraw first

The final result is still:
$90
because the operations are serialized by the mutex.

---------------------------------------------------------------------------------------------------------------------------------

23. What the Mutex Guarantees vs. What it Does NOT Guarantee

Mutex guarantees
For the protected critical section:
- Only one thread can hold the mutex and execute that protected section at a time.

Mutex does NOT guarantee
- That the deposit thread always runs first.
- Thread scheduling remains under the operating system.


NOTE:
Thread order ≠ guaranteed
Critical-section exclusion = guaranteed

---------------------------------------------------------------------------------------------------------------------------------

24. pthread_join()

The main thread executes:
pthread_join(id1, NULL);
pthread_join(id2, NULL);

- This tells the main thread to wait until the corresponding worker threads have completed.

IMPORTANT, as without the joins, the main thread could potentially reach:
cout << "Final balance: $" << account_balance;
- Before the worker threads have finished updating the balance.


Conceptually
Without join:
Main
 ↓
creates threads
 ↓
prints final balance ← potentially too early


With join:
Main
 ↓
creates threads
 ↓
waits for thread 1
 ↓
waits for thread 2
 ↓
prints final balance


- pthread_join() is not the mutex.
- It doesn't protect the critical section.
- Its purpose is to make one thread wait for another thread to finish.

---------------------------------------------------------------------------------------------------------------------------------

25. Output Order vs. Final Result

The program may produce something like:
Initial balance: $100
Depositing $50
Withdrawing $60
Final balance: $90

But it could also display:
Initial balance: $100
Withdrawing $60
Depositing $50
Final balance: $90


- The order of the worker-thread messages can vary because thread scheduling is not necessarily deterministic.

However: Final balance: $90
remains consistent because access to account_balance is protected by the mutex.

---------------------------------------------------------------------------------------------------------------------------------

26. Compilation: -lpthread

- Because the program uses the pthread library, it needs to be linked against that library when compiling.
- Thus "-lpthead" is the mist common compiler option, depending on the system.

WHY?
Functions like:
pthread_create
pthread_mutex_lock
pthread_mutex_unlock
pthread_join
pthread_exit

Come from the pthread library.
So the compiler/linker needs access to that library.

---------------------------------------------------------------------------------------------------------------------------------

27. Full Conceptual Flow:
Shared data exists
       ↓
Multiple threads can access it
       ↓
Potential race condition
       ↓
Identify critical section
       ↓
Create shared mutex
       ↓
Initialize mutex
       ↓
Thread calls pthread_mutex_lock()
       ↓
Is mutex available?
   /             \
 YES              NO
 ↓                 ↓
Acquire          Block/wait
mutex               ↓
 ↓              Eventually
critical         acquire
section             ↓
 ↓              critical
unlock              section
 ↓
continue

- Mutex converts potentially simultaneous access into controlled, mutually exclusive acceess.

---------------------------------------------------------------------------------------------------------------------------------

26. 00_MutexIntro -> 01_MutexPhtreadLibrary Connection

00_MutexIntro taught:
Busy waiting
     ↓
CPU wasted
     ↓
Sleep/wake or yielding
     ↓
Mutex concept
     ↓
lock / unlock


01_MutexPhtreadLibrary taught:
pthread_mutex_t
     ↓
PTHREAD_MUTEX_INITIALIZER
     ↓
pthread_mutex_lock()
     ↓
critical section
     ↓
pthread_mutex_unlock()

---------------------------------------------------------------------------------------------------------------------------------

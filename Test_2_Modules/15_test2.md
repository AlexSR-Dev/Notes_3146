15 - Test 2
{
(Arrival time)
DIFFERENT
(Order time)
}

{
    I/O Wait Times (DRAW IT OUT)
}

Module 5-2 (Data Sharing + Mutex):
- File 00
1) Problem: Busy Waiting
- When a thread/process cannot enter its critical region and repeatedly checks the lock, thus wasting CPU resources, as the thread isn't performing anything while waiting.

EX:
while (lock == locked) {
    keep checking...
}


2) Alternative: Sleeop and Wake Up
- Instead of waiting, it can be blocked/suspended (sleep). Once it becomes eligible to proceed, it awakens and enter critical region.
- THUS, doesn't consume CPU cycles waiting.


2.1) Mutex (Exclusion):
- A shared variable with two states 0 = unlocked and 1 = locked.
- A thread must have the mutex before entering critical region and release it after.
- THUS ONLY ONE MUTEX EXISTS and shared for MANY THREADS.


2.2) mutex_lock()
- Called before entering the critical region.

- Within the same function, a key function to avoid busy waiting:
thread_yield()
- In which the current thread voluntarily gives up the CPU.


2.3) mutex_unlock()
- Called once the thread finishes with the critical region.

- The mutex changes to 0 = unlocked.




Module 5-2 (Mutex Pthread Library)
- File 01
1) Problem: Race Condition
- When the result depends on the timing/order in which threads access shared access. (Every thread gains acesss at the same time.)

2) Solution: Mutex (exclusion)
- Ensures one thread at a time can enter the critical section:
Thread wants shared data -> Lock Mutex -> Critical Section -> Unlock mutex


3) Mutex Implementation:

3.1) Declaring a Pthread Mutex:
- The pthread library provides a specialized data type: (pthread_mutex_t)
- The initialization is to the mutex with default attributes (states and configs).
- Declare and initialize a mutex, (variable name (mutex) can change):

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER


CHECK:
3.2) pthread_mutex_lock()
- Initialize function before entering critical region, passes the mutex pointer.
int pthread_mutex_lock(pthread_mutex_t* p_mutex);

- The actual call:
pthread_mutex_lock(&mutex)

- Since the function expects a pointer to mutex, it is passed the memory address of the mutex variable

3.3) pthread_mutex_unlock()
- Basically the same, initialization:
int pthread_mutex_unlock(pthread_mutex_t* p_mutex);

- Call:
pthread_mutex_unlock(&mutex);
- Happens after the critical section.


3.4) Global Variables:
- The shared data, that threads access:
EX: int account_balance;
REMAINDER: Global vars are declared outside MAIN.

- The mutex that protects access to the shared data:
EX: pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;


CHECK:
3.5) pthread_create()
- Remainder, to create a thread you pass the:
1) Memory address of pthread
2) NULL for attributes
3) The function
4) The function's arguments as void pointers, (SO change the data type like int into a pointer version and pass a memory address to it)
EX:
int* pa;
pa = &d;

EX: pthread_create(&id1, NULL, deposit, (void*)pa);


REMAINDER:
- Since void * is a generic pointer
void* arg -> (int*)arg -> pointer to int -> *((int*)arg) -> Actual integer value
Void pointer ->     points to int        -> dereference  ->


4) Sleep/Wake Mutex Implementation:
EX:
void* deposit(void* arg) {
    int amount = *((int*)arg);

    print statements...

    // Lock mutex before critical region.
    pthread_mutex_lock(&mutex);

    // NOW you can access the shared data the mutex protects.
    account_balance += account;

    // Unlock mutex to leave critical region.
    pthread_mutex_unlock(&mutex);

    // This thread exits
    pthread_exit(NULL);
}

REMINDER:
- Both the lock and unlock functions NEED to USE the SAME MUTEX, as there is ONLY ONE.
- ELSE, if there are separate mutexes, race condition will HAPPEN.


5) Mutex Guarentees:
- Protected Critical Region.

- DOES NOT:
: Schedule thread order (its controlled by the OS).


6) pthread_join()
- Tells the main thread to wait until the threads have been completed:
EX: pthread_join(id1, NULL);

7) Compile
g++ ok.cpp -o now.cpp -lpthread

: -lpthread
- REQUIRED to link to the pthread library.



---------------------------------------------------------------------------------------------------------------------------------


Module 6-1 (Data Sharing + Producer Consumer)
- File02
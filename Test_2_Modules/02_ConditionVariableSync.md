Processes and Threads - Part 10:

Dividing Work Among Threads & Synchronizing with Condition Variables:
- Builds directly on the mutex concepts from part 9.
Major new idea, is that a mutex can protect shared data, but a mutex by itself does not provide an efficient way for a thread to wait
until the shared data reaches a particular state.

- Thus, the introduction to "condition variables".

---------------------------------------------------------------------------------------------------------------------------------

1. Three Ways to Divide Work Among Threads:
- Three general strategies for dividing work.

1. Data Decomposition
- Same function + different data.

- Each thread performs the same operation, but each thread works on a different portion of the data.
EX: Suppose we have a large array and want to calculate its sum.

Instead of: One thread → entire array

We could do:
Thread 1 → first portion
Thread 2 → second portion
Thread 3 → third portion

- All threads perform the same "sum my portion" function.

NOTE:
Data decomposition = divide the data among threads, doesn't have to be even/fairly distributed.




2. Task Decomposition
- Different functions + often the same data

EX: Given the same array:
Thread 1 → calculate sum
Thread 2 → calculate product
Thread 3 → calculate average

- The threads may operate on the same shared data, but they perform different functions.

NOTE:
Task decomposition = divide the tasks/functions among threads.




3. Data Flow Decomposition
- One Thread produces data, and another thread consumes that data.

EX:
Thread A
   ↓
produces data
   ↓
shared buffer
   ↓
Thread B
   ↓
consumes/processes data

- There is a flow of data from one thread to another.

NOTE:
Data-flow decomposition = one thread's output becomes another thread's input.


FINAL NOTE:
Data decomposition → same function, different data
Task decomposition → different functions
Data-flow decomposition → one thread produces data and another consumes it.

---------------------------------------------------------------------------------------------------------------------------------

2. Producer-Consumer Problem
- Is an example of data-flow decomposition.

There are two threads:
Produce - Creates data and puts it into a shared buffer.

Consumer - Removes data from the shared buffer and processes it.

Conceptually:
Producer
   ↓
[ Shared Buffer ]
   ↓
Consumer


- Buffer is usually "fixed sized", thus a limited capacity.
But, in turn is a problem called:
Bounded buffer problem
- "Bounded" refers to the buffer's fixed, limited size.

---------------------------------------------------------------------------------------------------------------------------------

3. Why Does the Producer-Consumer Problem Need Synchronization?

- Two rules MUST NEVER be VIOLATED.

Rule 1 - Producer cannot add to a full buffer.

If the buffer is full:
Producer → STOP
- Else, it could overwrite data that the consumer has not processed yet or access memory outside the buffer.


Rule 2 - Consumer cannot remove from an empty buffer.

If the buffer is empty:
Consumer → STOP
- Else, it could read invalid/stale data.

---------------------------------------------------------------------------------------------------------------------------------

4. Why Can't We Just Use Busy Waiting?
- Busy waiting refers to possibly the producer or consumer repeatedly checking if the buffer is full or empty respectively.
Thus, wastes CPU time from doing useful operations.

THUS:
If a thread cannot proceed, it should sleep and be awakened when it makes sense for it to continue.

---------------------------------------------------------------------------------------------------------------------------------

5. The count Variable
- The basic solution keeps track of how many items are currently in the buffer:
int count = 0;

if the buffer has capacity N:
count = 0     → buffer empty
count = N     → buffer full
0 < count < N → buffer has some items


Producer
Prior to producing:
Is count == N?

- If yes, Buffer is full --> producer sleeps.
- If not, There is room --> producer can add an item.



Consumer
Prior to consuming:
Is count == 0?

- If yes, Buffer is empty --> consumer sleeps.
- If no, There is an item --> consumer can remove one.

---------------------------------------------------------------------------------------------------------------------------------

6. The New Concept: Condition Variables
- Main concept of Part 10.

A condition variable allows a thread to efficiently wait for a particularr condition involving shared data.

EX:
Producer waits for:
"buffer is NOT FULL"

Consumer waits for:
"buffer is NOT EMPTY"

- Another thread can then "signal" that the condition has become true.


Conceptually:
Condition is false
       ↓
     WAIT
       ↓
     SLEEP
       ↓
another thread changes shared data
       ↓
    SIGNAL
       ↓
     WAKE UP

---------------------------------------------------------------------------------------------------------------------------------

7. Condition Variable Declaration

The pthread data type is:
pthread_cond_t

A condition variable can be initialized using:
pthread_cond_t cond_var = PTHREAD_COND_INITIALIZER;

This is analogous to the mutex declaration:
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;



EXP:
pthread_cond_t
- Data type

EXP:
cond_var
- Variable name

EXP:
PTHREAD_COND_INITIALIZER
- Initializes it with default attributes.

---------------------------------------------------------------------------------------------------------------------------------

8. Give Condition Variables Meaningful Names:

Instead of:
pthread_cond_t cond_var;

Utilize names such as:
pthread_cond_t not_empty;
pthread_cond_t not_full;

- Indicates the developer what the condition represents.


not_empty
- Signifies the buffer contains at least one item.

not_full
- Signifies the buffer has at least one avaiable slot.

Useful when a program has multiple condition variables.

---------------------------------------------------------------------------------------------------------------------------------

9. The Two Core Condition Variable Functions

The two functions:
pthread_cond_wait()
AND
pthread_cond_signal()

They work toegether.

---------------------------------------------------------------------------------------------------------------------------------

10. pthread_cond_wait()

Syntax:
int pthread_cond_wait(
    pthread_cond_t* p_cond,
    pthread_mutex_t* p_mutex
);

- It takes two addresses:
    1. Address of the condition variable.
    2. Address of the mutex protecting the shared data.


EX: pthread_cond_wait(&not_full, &mutex);

Means "I am waiting for the not_full condition, and mutex protects the shared data involved in that condition."

---------------------------------------------------------------------------------------------------------------------------------

11. Why does pthread_cond_wait() Need a Mutex?
- A condition variable doesn't protect the shared data itself.
The mutex protects the shared data.

EX:
buffer
count
   ↓
protected by
   ↓
mutex

The condition variable is concerned with the state of that data.

EX: 
count == 0
- May Represent:
buffer is empty

Thus:
Mutex → protects shared data
Condition variable → lets threads wait for a condition involving that data

---------------------------------------------------------------------------------------------------------------------------------

12. The Most Important Property of pthread_cond_wait()

When: pthread_cond_wait(&condition, &mutex);
- Is called, the system:
    1. Unlocks the mutex.
    2. Puts the calling thread to sleep.

Happens in one indivisible operating.
When the thread is eventually awakened:
    3. The mutex is automatically reacquired.
    4. pthread_cond_wait() returns.
    5. The thread continues execution while holding the mutex.

IMPORTANT NOTE:
WAIT = release mutex + sleep → wake up + reacquire mutex

---------------------------------------------------------------------------------------------------------------------------------

13. Why Must the Mutex Be Released Before Sleeping?

Imagine:
Thread A
   ↓
locks mutex
   ↓
checks condition
   ↓
condition is false
   ↓
goes to sleep

- What if Thread A kept the mutex while sleeping?
Then:
Thread A -> sleeping -> holding mutex

Thread B would try to acquire the mutex:
Thread B → cannot acquire mutex

- But Thread B may be the thread that needs to change the shared data and make the condition true!


Thus:
Thread A
   ↓
releases mutex
   ↓
sleeps

Thread B
   ↓
gets mutex
   ↓
changes shared data
   ↓
signals A

- This is why the automatic unlock performed by pthread_cond_wait() is critical.

---------------------------------------------------------------------------------------------------------------------------------

14. pthread_cond_signal()

Syntax:
int pthread_cond_signal(pthread_cond_t* p_cond);
- It takes the address of the condition variable.

EX: pthread_cond_signal(&not_empty);
- Its purpose is to notify a thread waiting on that condition.

- Thus waking the waiting thread; the waiting thread will then reaquire the associated mutex before continuing.

---------------------------------------------------------------------------------------------------------------------------------

15. Wait/Signal Example

Suppose:
Thread A needs x == 5
Thread B can change x



Thread A:
lock M
   ↓
check x
   ↓
x != 5
   ↓
wait on C
   ↓
mutex automatically released
   ↓
Thread A sleeps



Thread B:
lock M
   ↓
change x to 5
   ↓
signal C
   ↓
unlock M



Thread A:
wakes up
   ↓
reacquires M
   ↓
pthread_cond_wait() returns
   ↓
continues

- This entire process is the core mechanism behind the producer-consumer solution.

---------------------------------------------------------------------------------------------------------------------------------

16. The Three Synchronization Pieces
- Distinguish the three concepts encountered across Part 8-10.

Mutex
- "Who is allowed to access the shared data right now?"
mutex → mutual exclusion


Condition Variable
- "When should I sleep until the shared data reaches the state I need?"
condition variable → waiting/signaling


count
- "What is the current state of the buffer?"
count → shared state


Altogether:
              shared state
                  count
                    ↓
              ┌──────────┐
              │  mutex   │
              └──────────┘
               ↙        ↘
          not_empty    not_full
              ↓            ↓
          consumer       producer
            waits          waits

---------------------------------------------------------------------------------------------------------------------------------

17. Complete Producer-Consumer Setup

EX:
#include <pthread.h>
#include <iostream>

#define N 10

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

pthread_cond_t not_empty = PTHREAD_COND_INITIALIZER;
pthread_cond_t not_full = PTHREAD_COND_INITIALIZER;

int buffer[N];

int count = 0;



What each variable does
Variable	        Purpose
buffer[N]	        Shared fixed-size buffer
count	            Number of items currently in buffer
mutex	            Protects buffer and count
not_empty	        Consumer waits here when buffer is empty
not_full	        Producer waits here when buffer is full

---------------------------------------------------------------------------------------------------------------------------------

18. Why Does One Mutex Protect Both buffer and count?
- Both are shared data.

The producer changes:
buffer
count

The consumer changes:
buffer
count


- Thus, both need protection,
The following uses one mutex: pthread_mutex_t mutex,
to protect both.

---------------------------------------------------------------------------------------------------------------------------------

19. The Broken Version

Without synchronization:

Producer
buffer[curr_pos++] = rand();
++count;


Consumer
cout << "Consuming " << buffer[curr_pos++] << endl;
--count;

Both threads are accessing:
buffer
count

wihtout a mutex or condition variables.

---------------------------------------------------------------------------------------------------------------------------------

20. Why the Unsynchronized Version is Broken
- Several possible problems.

1. Producer overwires unconsumed data
- If the buffer is full, the producer could write over an item that the consumer hasn't processed.

2. Consumer read unwritten data
- If the buffer is empty, the consumer could read an entry that the producer hasn't acutally produced.

3. count can become incorrect
- Both threads modifer: count,
without protectiom, THUS creates a race condition.

---------------------------------------------------------------------------------------------------------------------------------

21. Correct Producer Logic
The synchronized producer follows this pattern:
pthread_mutex_lock(&mutex);

if (count == N)
    pthread_cond_wait(&not_full, &mutex);

buffer[curr_pos++] = rand();

++count;

pthread_cond_signal(&not_empty);

pthread_mutex_unlock(&mutex);




EXP: Producer - Step 1: Lock
pthread_mutex_lock(&mutex);

Before touching:
buffer
count

- The producer obtains the mutex.
Beacuse both are shared data.




EXP: Producer - Step 2: Check Full
if (count == N)

If: count == N
Then: buffer is full
- The producer cannot safely add another item.


Thus:
pthread_cond_wait(&not_full, &mutex);

The producer:
releases mutex
      ↓
goes to sleep
      ↓
waits for not_full




EXP: Producer - Step 3: Produce
Once the producer can proceed:
buffer[curr_pos++] = rand();

- Places an item into the buffer.

Then: ++count;
- Because the buffer now contains on additional item.




EXP: Producer - Step 4: Signal "not_empty"
After adding an item:
pthread_cond_signal(&not_empty);

Why "not_empty"?
- Because before the producer added the item, the buffer could have been empty.

After adding it:
buffer definitely has at least one item

Thus:
producer → signals not_empty

- Tells the waiting consumer that it may now be able to proceed.




EXP: Producer - Step 5: Unlock
Lastly:
pthread_mutex_unlock(&mutex);

- The producer releases the shared resource.
Now another thread can acquire the mutex.

---------------------------------------------------------------------------------------------------------------------------------

22. Consumer Logic
- The consumer is essentionally the mirror image of the producer.

pthread_mutex_lock(&mutex);

if (count == 0)
    pthread_cond_wait(&not_empty, &mutex);

cout << "Consuming " << buffer[curr_pos++] << endl;

--count;

pthread_cond_signal(&not_full);

pthread_mutex_unlock(&mutex);



Concumer - Step-by-Step

1. Lock
pthread_mutex_lock(&mutex);
- Protect shared data.



2. Check empty
if (count == 0)

- If true: buffer is empty
- Thus: pthread_cond_wait(&not_empty, &mutex);

The consumer releases the mutex and sleeps.



3. Consume
- Once it can proceed:
buffer[curr_pos++]
- is consumed.



4. Decrement count
--count;
- Beacuse one item was removed.



5. Signal "not_full"
pthread_cond_signal(&not_full);

- There is now at least one more available slot.



6. Unlock
pthread_mutex_unlock(&mutex);

---------------------------------------------------------------------------------------------------------------------------------

23. The most Important Producer/Consumer Relationship

Producer
Adds item
   ↓
count increases
   ↓
buffer is not empty
   ↓
signal not_empty



Consumer
Removes item
   ↓
count decreases
   ↓
buffer is not full
   ↓
signal not_full


NOTE:
Producer signals not_empty.
Consumer signals not_full.

---------------------------------------------------------------------------------------------------------------------------------

24. Why Does the Producer Singal "not_empty"?

Suppose: count = 0

The consumer tries to consume: count == 0
- so it sleeps on: not_empty

Then the producer adds an item: count = 1
Now the condition: buffer is not empty
- is true.

Thus: pthread_cond_signal(&not_empty);
- wakes the consumer.

---------------------------------------------------------------------------------------------------------------------------------

25. Why Does the Consumer Signal "not_full"?

Suppose: count = N

The producer tries to add an item.
The buffer is full, so the producer sleeps on: not_full

Then the consumer removes an item: count = N - 1
- Now the buffer is no longer full.

Thus:
pthread_cond_signal(&not_full);
- Wakes the producer.

---------------------------------------------------------------------------------------------------------------------------------

26. The Entire Cycle
- The entire producer-consumer relationship:
             PRODUCER
                 │
                 ▼
          Is buffer full?
             /       \
           YES        NO
            │          │
            ▼          ▼
          WAIT       Add item
       on not_full       │
            │            ▼
            │         count++
            │            │
            │            ▼
            │      signal not_empty
            │            │
            └────────────┘
                         │
                         ▼
                       unlock


             CONSUMER
                 │
                 ▼
         Is buffer empty?
             /       \
           YES        NO
            │          │
            ▼          ▼
          WAIT       Remove item
      on not_empty      │
            │            ▼
            │         count--
            │            │
            │            ▼
            │       signal not_full
            │            │
            └────────────┘
                         │
                         ▼
                       unlock

---------------------------------------------------------------------------------------------------------------------------------

27. main()

The example creates two threads:
int main() {
    pthread_t id1, id2;

    pthread_create(&id1, NULL, producer, NULL);
    pthread_create(&id2, NULL, consumer, NULL);

    pthread_exit(NULL);
}

Thus:
id1 → producer
id2 → consumer

Both operate on the same global:
buffer
count
mutex
not_empty
not_full

---------------------------------------------------------------------------------------------------------------------------------

28. Circular Buffer

The example uses:
if (curr_pos == N)
    curr_pos = 0;

- Makes the fixed array behave like a circular buffer.
Conceptually:
0 → 1 → 2 → ... → 8 → 9
↑                   ↓
└───────────────────┘

- After position 9, the next position becomes 0.
Thus, reusing the same fixed-size array rather than allowing the biffer to grow indefinitely.

---------------------------------------------------------------------------------------------------------------------------------

29. Important Connection to Part 9

Part 9 taught:
pthread_mutex_lock(&mutex);

AND

pthread_mutex_unlock(&mutex);


Part 10 does not replace the mutex.
Instead, it adds another mechanism:
pthread_cond_wait(...)
pthread_cond_signal(...)


Thus the relationship:
Part 9:
Mutex → protects shared data

Part 10:
Mutex + Condition Variable
       ↓
Protect shared data
+
Wait efficiently for a condition

- Emphasizing that condition variables are need with a mutex, because the shared data defining the condition still needs mutual exclusion.

---------------------------------------------------------------------------------------------------------------------------------

30. Connection to Part 8: Busy Waiting

Part 8, problem: busy waiting
- A thread repeatedly checks a condition.

Solutio: sleep/wake


Part 9, introduces pthread mutexes:
pthread_mutex_lock()
pthread_mutex_unlock()



Part 10, combines these ideas:
mutex
+
condition variable

Thus a thread can:
check condition
     ↓
condition false
     ↓
release mutex + sleep
     ↓
wait for signal
     ↓
wake + reacquire mutex
     ↓
continue

---------------------------------------------------------------------------------------------------------------------------------

31. Important Distinction:

Mutex
Protect access
"Only one thread at a time can access this critical section."


Condition variable
Controls waiting
"I cannot proceed until this condition becomes true."


pthread_cond_signal()
Notifies a waiting thread.
"The state may now allow you to proceed."


THUS
mutex → protection
condition variable → waiting
signal → notification

---------------------------------------------------------------------------------------------------------------------------------

32. Another Important Distinction: Condition Variable != Condition
- Condition variable itself isn't the actual state.

EX: pthread_cond_t not_empty

- Doesn't itself contain the information:
count > 0

The actual state is represented by:
count

- The condition variable provides the mechanism for sleeping and waking based on that state.

---------------------------------------------------------------------------------------------------------------------------------

33. Why "if" is used in here?

The sycnhronized example uses:
if (count == N)
    pthread_cond_wait(&not_full, &mutex);

AND:

if (count == 0)
    pthread_cond_wait(&not_empty, &mutex);

lock
→ check state
→ wait if unable to proceed
→ perform operation
→ update state
→ signal
→ unlock

---------------------------------------------------------------------------------------------------------------------------------

34. Return Values
- Like the pthread mutex function from part 9, these condition-variable functions return an integer status.

0 → success
nonzero → error

For:
pthread_cond_wait()
AND
pthread_cond_signal()

---------------------------------------------------------------------------------------------------------------------------------

35. What the Synchronization Guarantees

Producer cannot safely add to a full buffer.
Consumer cannot safely remove from an empty buffer.
Shared count remains protected.
Shared buffer access is protected.
Threads sleep instead of continuously polling.

Therefore:
The solution avoids both race conditions and busy waiting for the producer-consumer scenario described.

---------------------------------------------------------------------------------------------------------------------------------

36. Entire Part 10:

                SHARED BUFFER
                     │
               ┌─────┴─────┐
               │           │
           PRODUCER     CONSUMER
               │           │
          adds items    removes items
               │           │
               └─────┬─────┘
                     │
                   count
                     │
                  protected
                     by
                     ↓
                   MUTEX
                     │
            ┌────────┴────────┐
            │                 │
         not_full          not_empty
            │                 │
       producer waits    consumer waits
            │                 │
       consumer signals  producer signals

- Mutexes protect the shared state; condition variables allow threads to sleep until that shared state reaches a useful condition.

---------------------------------------------------------------------------------------------------------------------------------

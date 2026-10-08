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

1) Division of Work Among Threads:

1.1) Data Decomposition:
- Same Function + Different Data.
- Divides the data among threads, doesn't have to be even/fairly distributed.


1.2) Task Decomposition:
- Different functions + often the same data.
- Divides the tasks/functions among threads.


1.3) Data Flow Decomposition:
- Once thread produces data, then placed into a shared buffer, and another thread consumes that data from the shared buffer.
- One thread's output becomes another thread's input.



2) Data-flow Decomposition Issue:
- The buffer is a "fixed size", thus limited capacity.
Resulting in: Bounded Buffer Problem.


2.1) Synchronization Required
- Two rules MUST NEVER be VIOLATED.

1. Producer cannot add to a full buffer.
- Thus, producer must stop, else data can be overwrite prior to the consumer's processing.

2. Consumer cannot remove from an empty buffer.
- Thus, consumer stops, else reads invalid/stale data.
NOTE: Busy Waiting is out of the options.



2.2) Solution: Count Variable
- Keep track of the amount of items in the buffer.
int count = 0;
0 < count < N

Producer:
- If the count == N, "full buffer" then the producer sleeps.
- Else, the producer adds items.

Consumer:
- If the count == 0, "empty buffer" then the consumer sleeps.
- Else, items are removed.


2.3) Condition Variables
- Allows a thread to efficiently wait for the particular condition involving shared data.

- The pthread data type:
pthread_cond_t

- Condition variable initialization and declaration:
pthread_cond_t cond_var = PTHREAD_COND_INITIALIZER;
NOTE: The initialization are default attributes.


- Providing Useful Variable names:
pthread_cond_t not_empty;
- At least one slot is available.
pthread_cond_t not_full;
- At least one item is in the buffer.

NOTE:
- ARE the MECHANISMS for SLEEP and WAKING based on the if statement STATE.



2.4) pthread_cond_wait()
Syntax:
int pthread_cond_wait(
    pthread_cond_t* p_cond,
    pthread_mutex_t* p_mutex
);

1. Address of the condition variable.
2. Address of the mutex protecting the shared data.

EX: pthread_cond_wait(&not_full, &mutex);
- "I am waiting fo the not_full condition, and mutex protects the shared data involved in that condition."

- Mutex is unlocked and puts the calling thread to sleep, then when the thread awakens the mutex is acquired to continue execution.
- Happens as one single operation.
WAIT = Unlock mutex + sleep -> wake up + reacquires mutex.


2.5) Explanations:
- The mutex is unlocked prior to sleep, to allow other mutexes to acquire the mutex, to then make the condition for the initial thread to be true.


2.6) pthread_cond_signal()
Syntax:
int pthread_cond_signal(pthread_cond_t* p_cond);

EX: pthread_cond_signal(&not_empty)
- To notify a thread waiting on that condition.
- Allowing the waiting thread to wake up and reacquire the mutex before exeuction.


3) Producer-Consumer Setup
#include <pthread.h>
#include <iostream>

#define N 10

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER:

pthread_cond_t not_empty = PTHREAD_COND_INITIALIZER;
pthread_cond_t not_full = PTHREAD_COND_INITIALIZER:

int buffer[N];

int count = 0;


3.1) Producer Logic
- Follow a synchronized producer:
pthread_mutex_lock(&mutex);

// The producer must check if the buffer is full, prior to safely adding another item.
// If so, mutex is released, and thread goes to sleep.
if (count == N)
    pthread_cond_wait(&not_full, &mutex);

buffer[curr_pos++] = rand();

++count;

// When the producer awakens and adds an item from above, it signals to the consumer theres an item in the buffer now and releases mutex.
// Also wakes up the consumer.
pthread_cond_signal(&not_empty);

pthread_mutex_unlock(&mutex);


3.2) Consumer Logic
- Almost the same as the producer
pthread_mutex_lock(&mutex);

// Checks if the buffer is empty, if so the consumer release the mutex and goes to sleep.
if (count == 0)
    pthread_cond_wait(&not_empty, &mutex);

// Once Awakened
cout << "Consuming " << buffer[curr_pos++] << endl;
-count;

// Signals to the producer there is one more slot available, thus wakes it up.
pthread_cond_signal(&not_full);

pthread_mutex_unlock(&mutex);


3.3) Producer-Consumer Diagram:
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

3.4) The Producer/Consumer Signal
- When either thread signals their respective state of the buffer, whether not_empty after producing an item, or not_full after
consuming an item from the buffer.
- The corresponding signal function with the memory address of the buffer state with WAKE UP the inverse thread.


4) Implementation:
int main() {
    // Create two threads, one for producer and another for consumer.
    pthread_t id1, id2;

    pthread_create(&id1, NULL, producer, NULL);
    pthread_create(&id2, NULL, consumer, NULL);

    pthread_exit(NULL);
}

// Use Circular Buffer to reset the buffer position and reuse the same fixed-size array:
if (curr_pos == N)
    curr_pos = 0;



NOTE:
Mutex
Protect access
"Only one thread at a time can access this critical section."

Condition variable
Controls waiting
"I cannot proceed until this condition becomes true."

pthread_cond_signal()
Notifies a waiting thread.
"The state may now allow you to proceed."


---------------------------------------------------------------------------------------------------------------------------------


Module 6-2 (CPU Scheduling)
File03, File04, File05

1) CPU Scheduling
- Allows multiple processes to be in the ready state concurrently.

Decision-making Process:
: Scheduler - Component to select which process runs next.
: Scheduling Algorithm - Rule/logic the scheduler uses to make the dicision.


2) Scheduling Policy
- The rule used to decides when and which process to run, prioitized by the CPU scheduling.

2.1) Batch System (Efficiency)
- A scheduling policy, that focuses on overall system perforamnce (processes batches), rather than waiting for a person.
CONCERN: Less importance on which job finishes first, but just the overall workload efficiency.

2.2) Interactive System (Responsiveness)
- A scheduling policy, that a user interactes(issue commands) directly to the system and waits for its results.
CONCERN: The system's performance may be perceived slow by the user.

2.3) Real-Time Systems (Predictability)
- A scheduling policy, in which jobs have specific deadlines to be accomplished by.
CONCERN: Predictability, as the system must be consistent to deadlines, unexpected delays become serious (hard real-line) or harmless (soft real-time).


3) Objectives to Scheduling Algorithms

3.1) In General:
Fairmess:
: Give processes a fair use to the CPU, to prevent indefinite starvation.

Efficiency and Balance:
: Scheduler should minimize CPU idel time, to keep it busy as possible with avaliable ready work.

Policy Enforcement:
: With the scheduling policy selected, the system should enforce it correctly and consistenly, else becomes unreliable to trust.


3.2) Batch Scheduling Objs
1. Maximize throughput
- Number of jobs completed during a given amount of time.
EX: 100 jobs/hour > 50 jobs/hour, in terms of throughput.

2. Minimize turnaround time
- The total time between: Job submitted -> Completed.

3. Maximize CPU Utilization
- Keep CPU busy with work rather than idle.


3.3) Interactive Scheduling Objs
1. Minimize Response Time
- The interval between when the command is issued and when it it completed.

2. Maintain Proportionality
- Matching user expections to the job size. Thus large/complex job requires longer wait timer and vice versa.


3.4) Real-Time Scheduling Objs
1. Predictability
- System should be consistent that timing is reliable.

2. Meeting Deadlines
- Scheduler makes the decision for jobs to be done prior to its deadline.

3. Considering Priorities
- Every job varies in importawnce.


3.5) Scheduling Decision
- Is triggered by:
1) A new process is created.
2) A process terminates.
3) A process becomes blocked.
4) An interrupt occurs. 

Types:
Preemptive: The OS forcibly takes the CPU.
Non-Preemptive: Process gives up CPU voluntarily or until it stops running.

3.6) Job Types:
Compute-Bound Jobs:
- Spends relatively long periods(bursts) performing CPU work before waiting for I/O.

I/O-Bound Jobs:
- Short periods(bursts) of CPU activity separated by frequent I/O waits.




File05

4) Interactive systems with Scheduling Policies for multiple jobs

4.1) Round Robin Scheduling
- Preemptive type, that gives each ready job with a small amount of CPU time, then moves on to the next job.
- THUS: Ready queue, and quantum (time slice like 20 ms).
PROS: Simple implementation and fair to every active job to use the CPU (in turns NOT TIME).
CONS: Treats jobs equally than priorities.

Quantam Size:
- Too short: The system focuses more on swithcing than working.
- Too long: Jobs wait longer every time.

EX:
| Job | CPU Burst |
| --- | --------: |
| J1  |        53 |
| J2  |        17 |
| J3  |        68 |
| J4  |        24 |


All jobs arrive at: t = 0
Quantum: 20 time units

Initial Queue: J1 → J2 → J3 → J4


Round 1:
J1 needs 53 units.
Quantum = 20.

Thus: 0 -> 20: J1
Remaining: 53 - 20 = 33
J1 goes to the back.
Queue: J2 -> J3 -> J4 -> J1


J2
J2 needs only 17 units.
Thus: 17 < 20
J2 finishes before the quantum expires: 20 → 37: J2
J2 leaves the queue.


J3
J3 needs 68 units.
It receives a full quantum: 37 → 57: J3\
Remaining: 68 − 20 = 48
J3 goes to the back.


J4
J4 needs 24 units.
It receives: 57 → 77: J4
Remaining: 24 − 20 = 4
J4 goes to the back.


Round 2:
Status::
J2 = finished
Queue:
J1 → J3 → J4


J1
J1 has 33 units remaining.
It receives another 20: 77 → 97: J1
Remaining: 33 − 20 = 13


J3
J3 has 48 remaining.
It receives: 97 → 117: J3
Remaining: 48 − 20 = 28


J4
J4 has only 4 units remaining.
Since: 4 < 20
it runs only for 4 units: 117 → 121: J4
J4 finishes.


Round 3:
Theres only: J1 -> J3 remaining.

J1
J1 has 13 units remaining: 121 → 134: J1
J1 finishes.


J3
J3 is now the only job left.
It has 28 units remaining.
Because there is no other job waiting for a turn, it simply runs: 134 → 162: J3
J3 finishes.


Round Robin Total CPU Time:
Job Reqire:
J1 = 53
J2 = 17
J3 = 68
J4 = 24

Total: 53 + 17 + 68 + 24 = 162 
Schedule finishes at: 162.


4.2) Priority-Based Scheduling
- Each job gets a priority value and the scheduler chooses based on the highest-priority.
- Both Preemptive or Non-Preemptive Type:
: Preemptive - If a higher priority job arrives, the current job can be interrupted immediately.
: Non-Preemptive - The current jobs continues until it finishes.


Priorities are Assigned Based on:
- Internal Priority
: Based on the job's behavior (waiting) or history (usage).

- External Priority
: Based on its environment like conditions.


5) Multilevel Scheduling
- A hybird apporach for mixed type jobs.
- Thus divides jobs into separate queues that may use different scheduling policies.




File04

6) Scheduling Policies for Batch Systems:
: CPU Burst
- The period of time a job is actively using the CPU.

: I/O Burst / I/O Wait
- The period that a job is waiting for input/output, does not use CPU.

Preemptive Scheduling - A job can be interrupted prior to finishing its CPU burst finishing
Non-Preemptive Scheduling - A job starts running and continues with the CPU until: Blocks for I/O, terminates, voluntarily yeilds.


6.1) First Come, First Served (FCFS)
- A non-preemptive scheduling policy that execute jobs based on the order of the queue.
THUS: As the name implies, the first on the queue occurs then the next after that and so-on.

- The Scheduler maintains a ready queue.
NOTE: IF a job returns from I/O it goes to the BACK of the ready queue. (NEVER ITS previous position)

PROS: Simple
CONS: Long jobs can delay short jobs, thus arrival order is priortized rather than job length.


6.2) Shortest Job First (SJF)
- Selects ready jobs with the shortest CPU burst.
- Is both Non-preemptive and preemptive.

Non-preemptive: Job cannot be interrupted until its CPU burst finishes.
- Looks aonly ath the CPU burst length of ready jobs.

Preemptive: Scheduler interrupts the current running job it a shorter job arrives less than the current job's remaining CPU time.
- Known as Shortest Remaining Time First (SRTF).
- Looks atL Remainnig CPU time.

PROS: Performance for Short Jobs is minimized in waiting/response, thus less likely to be stuck.
CONS: Knowledge of the CPU Burst Length is needed (SJF). Starvation of long jobs can be postponed iundefinitely.


7) Trade-offs
FCFS:
- Prioritizes: Arrival Order.
- Simple and predictable.
CONS: Long job -> Delay short jobs


SJF / SRTF:
- Prioritizes: Short CPU requirements.
- Short jobs finish quickly.
CONS: Delay of long jobs


8) Application of These Scheduling Policies
NOTE:
CpuUtilization = TotalCPUTime / TotalEcapsedTime x 100
- The percentage of time the CPU is actively executing jobs. With the total sum of all CPU bursts among all jobs divided by
the number of CPU cycles until the last job is finished.

Throughput = NumberOfJobs / TotalElapsedTime
- The number of jobs completed per unit of time.

TurnaroundTime = CompletionTime - ArrivalTime + 1
- The total time used to execute a particulare job, with waiting and execution time.

WaitingTime = TurnaroudTime - ExecutionTime
- The total time a job spends waiting in the ready queue.

RepsonseTime = FirstResponseTime - ArrivalTime
- Time taken from the submission of request until the first response is produced.



+-----+--------------+----------------+---------------+

| Job | Arrival time | CPU burst time | I/O wait time |
+-----+--------------+----------------+---------------+

|  A  |       4      |        2       |               |
|     |              |                |       4       |
|     |              |        4       |               |
+-----+--------------+----------------+---------------+

|  B  |       1      |        4       |               |
|     |              |                |       2       |
|     |              |        3       |               |
+-----+--------------+----------------+---------------+

|  C  |       3      |        1       |               |
+-----+--------------+----------------+---------------+

|  D  |       0      |        5       |               |
+-----+--------------+----------------+---------------+


Q1) FCFS
Time | Job
0   :   D
1   :   D
2   :   D
3   :   D
4   :   D
5   :   B
6   :   B
7   :   B
8   :   B
9   :   C
10  :   A
11  :   A
12  :   B
13  :   B
14  :   B
15: :   - (Nothing)
16  :   A
17  :   A
18  :   A
19  :   A

NOTE: 
- The queue starts based on arrival time thus, D->B->C->A
- Then Job's from I/O wait time go to the back of the queue after finishing like A or B.

Q2)
• Time 0–5: Job D arrives at \(0\) and runs its entire \(5\)-unit CPU burst.
• Time 5–9: Job B (arrived at \(1\)) runs its first CPU burst (\(4\) units). At time \(9\), it goes to I/O wait for \(2\) units (ready again at time \(11\)).
• Time 9–10: Job C (arrived at \(3\)) runs its \(1\)-unit CPU burst and finishes completely.
• Time 10–12: Job A (arrived at \(4\)) runs its first CPU burst (\(2\) units). At time \(12\), it goes to I/O wait for \(4\) units (ready again at time \(16\)).
• Time 12–15: Job B returns from I/O at time \(11\) and waits until the CPU is free at \(12\). It runs its second CPU burst (\(3\) units) and finishes completely at time \(15\).
• Time 15–16: CPU is Idle (waiting for Job A to finish I/O).
• Time 16–20: Job A returns from I/O at time \(16\) and runs its final CPU burst (\(4\) units), finishing at time \(20\).


1. What is the waiting time and turnaround time for each job?
Turnaround Time = Completion Time - Arrival Time
Waiting Time = Turnaround Time - Total CPU Burst Time - I/O Wait Time

Job D:
: Completion Time: 5
: Turnaround Time: 5 - 0 = 5
: Waiting Time: 5 - 5 - 0 = 0

Job C:
: Completion Time: 10
: Turnaround Time: 10 - 3 = 7
: Waiting Time: 7 - 1 - 0 = 6

Job A:
: Completion Time: 20
: Turnaround Time: 20 - 4 = 16
: Waiting Time: 16 - (2 + 4) - 4 = 10

Job B:
: Completion Time: 15
: Turnaround Time: 15 - 1 = 14
: Waiting Time: 14 - 7 - 2 = 5



Q3) SRTF

Time    Scheduled Job	Reason / Remaining CPU Bursts
0	    D	            Only D is available (Remaining: D=5).
1	    D	            B arrives. Tie between D=4 and B=4; D wins via earlier arrival.
2	    D	            D=3, B=4. D has the shortest remaining time.
3	    C	            C arrives (C=1). Preempts D (D=2, B=4).
4	    D	            C finished. A arrives (A=2). Tie between D=2 and A=2; D wins via earlier arrival.
5	    D	            D=1, A=2, B=4. D executes and finishes at time 6.
6	    A	            A=2, B=4. A has the shortest remaining time.
7	    A	            A=1, B=4. A executes and enters I/O at time 8 (Ready again at 12).
8	    B	            Only B is ready (B=4).
9	    B	            B=3.
10	    B	            B=2.
11	    B	            B=1. B executes and enters I/O at time 12 (Ready again at 14).
12	    A	            A returns from I/O (A=4). B is in I/O.
13	    A	            A=3.
14	    A	            B returns from I/O (B=3). A has 2 units left, so A wins (A=2 vs B=3).
15	    A	            A=1 vs B=3. A executes and finishes at time 16.
16	    B	            Only B remains (B=3).
17	    B	            B=2.
18	    B	            B=1. B executes and finishes at time 19.
19	    -	            All jobs are finished; the CPU is Idle.


Q4)
1. What is the waiting and turnaround time for each job?
Turnaround Time = Completion Time - Arrival Time
Waiting Time = Turnaround Time - Total CPU Burst Time


Job A (Arrival = 4, Total CPU = 6)
• Timeline: Arrives at 4 -> Waits until 6 -> Runs CPU 1 (6–8) -> I/O Wait (8–12) -> Runs CPU 2 (12–16).
• Completion Time: 16
• Turnaround Time: 16 - 4 = 12
• Waiting Time: 12 - 6 = 6 (2 units waiting in ready queue + 4 units in I/O)

Job B (Arrival = 1, Total CPU = 7)
• Timeline: Arrives at 1 -> Waits until 8 -> Runs CPU 1 (8–12) -> I/O Wait (12–14) -> Waits until 16 -> Runs CPU 2 (16–19).
• Completion Time: 19
• Turnaround Time: 19 - 1 = 18
• Waiting Time: 18 - 7 = 11 (9 units waiting in ready queue + 2 units in I/O)

Job C (Arrival = 3, Total CPU = 1)
• Timeline: Arrives at 3 -> Immediately runs and finishes at 4.
• Completion Time: 4
• Turnaround Time: 4 - 3 = 1
• Waiting Time: 1 - 1 = 0

Job D (Arrival = 0, Total CPU = 5)

• Timeline: Arrives at 0 -> Runs (0–3) -> Preempted by C and waits (3–4) -> Runs to finish (4–6).
• Completion Time: 6
• Turnaround Time: 6 - 0 = 6
• Waiting Time: 6 - 5 = 1 (1 unit spent preempted in the ready queue)


Q5) Round Robin
Time	Scheduled Job	Reason / Queue State
0	    D	            D arrives and runs (Remaining: D=3). Queue: []
1	    D	            D continues. B arrives at 1. Queue: [B]
2	    B	            D's quantum expires; moves to back. Queue: [B, D]. B runs (Remaining: B=2).
3	    B	            B continues. C arrives at 3. Queue: [D, C]
4	    D	            B's quantum expires. A arrives at 4. By Convention 2, B is placed ahead of A. Queue: [D, C, B, A]. D runs 
                        (Remaining: D=1).
5	    D	            D continues.
6	    C	            D's quantum expires. Queue: [C, B, A, D]. C runs (Remaining: C=0).
7	    B	            C finishes at 7. By Convention 1, next job starts immediately. Queue: [B, A, D]. B runs (Remaining: B=0 for burst 
                        1).
8	    B	            B continues.
9	    A	            B finishes CPU burst 1 and enters I/O for 2 units (ready at 11). Queue: [A, D]. A runs (Remaining: A=0 for burst 
                        1).
10	    A	            A continues.
11	    D	            A finishes CPU burst 1 and enters I/O for 4 units (ready at 15). B returns from I/O. Queue: [D, B]. D runs its 
                        last 1 unit.
12	    B	            D finishes at 12. Queue: [B]. B starts CPU burst 2 (Remaining: B=1).
13	    B	            B continues.
14	    B	            B's quantum expires and goes back to the queue. Queue: [B]. B runs its final unit.
15	    A	            B finishes at 15. A returns from I/O. Queue: [A]. A starts CPU burst 2 (Remaining: A=2).
16	    A	            A continues.
17	    A	            A's quantum expires. Queue: [A]. A runs its last 2 units.
18	    A	            A continues.
19	    -	            All jobs are finished; the CPU becomes Idle.


Q6)
1. What is the waiting time and turnaround time for each job?
Turnaround Time (TAT): Completion Time - Arrival Time
Waiting Time (WT): Turnaround Time - Total CPU Burst Time


Job A (Arrival = 4, Total CPU = 6)
• Timeline: Arrives at 4 -> Waits until 9 -> Runs CPU 1 (9–11) -> I/O Wait (11–15) -> Runs CPU 2 (15–19) to finish.
• Completion Time: 19
• Turnaround Time: 19 - 4 = 15
• Waiting Time: 15 - 6 = 9 (5 units waiting in ready queue + 4 units in I/O)

Job B (Arrival = 1, Total CPU = 7)
• Timeline: Arrives at 1 -> Waits until 2 -> Runs (2–4) -> Waits until 7 -> Runs (7–9) -> I/O Wait (9–11) -> Waits until 12 -> Runs CPU 2 (12–15) to finish.
• Completion Time: 15
• Turnaround Time: 15 - 1 = 14
• Waiting Time: 14 - 7 = 7 (5 units waiting in ready queue + 2 units in I/O)


Job C (Arrival = 3, Total CPU = 1)
• Timeline: Arrives at 3 -> Waits in queue from 3 to 6 -> Runs (6–7) to finish.
• Completion Time: 7
• Turnaround Time: 7 - 3 = 4
• Waiting Time: 4 - 1 = 3 (3 units waiting in the ready queue)


Job D (Arrival = 0, Total CPU = 5)
• Timeline: Arrives at 0 -> Runs (0–2) -> Waits until 4 -> Runs (4–6) -> Waits until 11 -> Runs (11–12) to finish.
• Completion Time: 12
• Turnaround Time: 12 - 0 = 12
• Waiting Time: 12 - 5 = 7 (7 units total spent waiting in the ready queue between time slices)


---------------------------------------------------------------------------------------------------------------------------------

Module 7-1 (Partitioning)
File06

1) Process Requires Two Resources:
1.1) Active Resources
- CPU cycles, I/O devices controlled by the process scheduling and used actively while executing.

1.2) Memory Resources
- Process Control Block, memory that makes up the process data.

2) Process Data in Main memory (RAM/Violatile):
1. Process data is stored on non-volatile storage.
2. The OS loads the process data into main memory.
3. The process can execute.
4. The process's memory contents can eventually be stored back on disk.

2.1) Why Only One Process in Memory?
1) Load one process from disk.
2) Give it all of main memory.
3) Let it run until it finishes.
4) Store its contents back on disk.
5) Load the next process.

Problem #1 - Poor CPU Utilization
- A process does not continuously use the CPU.

Problem #2 - Poor Memory Utilization
- A process usually does not need all of the computer's main memory.

2.2) Solution: Memory Multiplexed
- With memory, multiple processes can physically occupy memory simultaneously, which creates additional problems.

- Its Requirements
1 - Multiple processes should reside in memory
2 - Processes should not collide in memory
3 - A process should not access another process's memory
4 - Processes should be able to share memory when desired

THUS, the OS needs: "Controlled overlap"
Memory protection - Prevents unauthorized memory access.
Controlled overlap - Allow sharing when the system deliberately permits it.

3) Memory Partitioning
- A basic manner to allow multiple processes to occupy memory is "memory partitioning."

1) Divide main memory into multiple regions.
2) These regions are called partitions.
3) Allocate processes to partitions.
4) Protect the boundaries between partitions.
- Essentially: Divide memory, Allocate processes, and Ensure protection.

3.1) Static, Equal-Sized Partitions
Rule 1 - Divide memory into equal-sized partitions.
- Partitions are created statically, and its boundaries are established at system startup and do not change afterward.
Every partition has the same size.

Rule 2 - Map each process to one partition
- Each process is assigned to a partition.

3.2) Allocation
- Suppose every partition is: 1000 KB
But processes require:
P1 = 800 KB
P2 = 100 KB
P3 = 600 KB

- Allocation can become:
Partition 1
+----------------+
| P1 = 800 KB    |
|                |
| 200 KB unused  |
+----------------+

Partition 2
+----------------+
| P2 = 100 KB    |
|                |
|                |
| 900 KB unused  |
+----------------+

Partition 3
+----------------+
| P3 = 600 KB    |
|                |
| 400 KB unused  |
+----------------+

3.3) ISSUE: Internal Fragmentation
- Is unused memory inside an allocated partition because the process occupying that partition does not need all of it.
Key Word: Interal
- As wasted memory is inside a region that has already been allocated to a process.

NOTE: Internal fragmentation = wasted space inside an allocated memory region.

4) Swapping
- Moving a process's data betweem main memory and disk to allow another process to use the same memory partition.

- The system can map multiple processes to the same partition, but:
Only one of those processes can occupy that partition at a time.
- Meanwhile the others remain on disk.

ISSUE: Its expensive, as it involves the transfer of process's data from memory -> disk and disk -> memory.

4.1) Memory Protection with Based Address and Bounds Checking
The OS needs to ensure: A process can only access addresses inside its assigned partition.
- The scheme uses a "base address (BA)."
Base Address - Is the starting memory address of the process's assigned partition.

4.2) The Bounds-Checking Formula
- An address is legal if:
BA <= Address < BA + PartitionSize

So if:
BA = 1000
Partition size = 1000

Then: 1000 <= Address < 2000
Valid Addresses: 1000 through 1999
Invalid: 2000

5) Static Equal-Sized Partitioning
PROS: Simple and manageable.
CONS: Memory Under-utilization, Large processes cannot run (Partition size = 1 MB, Process requires = 1.5 MB)

// Complete Process:
          MAIN MEMORY
              ↓
     Divide into partitions
              ↓
      Equal-sized partitions
              ↓
     Assign processes
              ↓
      +-------------+
      | Process 1   |
      +-------------+
      | Process 2   |
      +-------------+
      | Process 3   |
      +-------------+
              ↓
       Protect boundaries
              ↓
   Base + bounds checking
              ↓
   If more processes exist
              ↓
          SWAPPING
       RAM ↔ Disk



file07
6) Static Unequal-Sized Partitions
1. Create partitions of different sizes at system startup.
2. Assign each process to a partition large enough to hold it.

GOAL: Reduce internal fragmentation by matching partition sizes more closely to process sizes.

7) Allocation
Suppose there are three partitions:
Partition 1 = Small
Partition 2 = Medium
Partition 3 = Large

And three processes:
P1 = Large
P2 = Small
P3 = Medium

The allocation becomes:
+--------------------+
| P2                 | ← Small process
| small unused space |
+--------------------+
| P3                 | ← Medium process
| small unused space |
+--------------------+
| P1                 | ← Large process
| small unused space |
+--------------------+

- Instead of arbitrary equal-sized regions, the processes are matched to partitions.

7.1) Internal Fragmentation Still Exists
- Unequal-sized partitions REDUCE internal fragmentation; they DO NOT eliminate it.
BECAUSE: The partitions are still fixed.

Smaller amount of wasted space
- Thus improves memory utilization, but does not completely solve fragmentation.

8) Swapping Still Works

When there are more processes than available partitions:
1. Store the current process's data on disk.
2. De-allocate its partition.
3. Allocate the partition to another process.
4. Load the new process's data into memory.

8.1) Constraint
- A process can only be swapped into a partition that is large enough to hold it.
- Thus MATCHING requirement applies NOT ONLY during original allocation, but also during SWAPPING.

9) Protection: Why Base Address Alone Is No Longer Enough
Part 1 - Equal-sized partitions
- The OS only needed to store: Base Address (BA)
Because every partition had the same size.

Part 2 - Unequal-sized partitions
Now Consider:
Partition 1 = 500
Partition 2 = 1000
Partition 3 = 1500

Knowing only: BA = 1000
- This does not tell the OS where that particular partition ends.
THUS, the OS needs another value.

9.1) Base Address + Limit
Base Address(BA)
- Starting address of the process's partition.

Limit
- The size of that particular partition.

EXAMPLE:
BA = 2000
Limit = 1000

The process can access: 2000 - 2999

9.2) Base-and-Limit Bounds Checking
The formula is: BA <= Address < BA + Limit

Part 1 was: BA <= Address < BA
- When partition size was the same for everyone.

Part 2 is: BA <= Address < BA + Limit
- Where Limit is specific to the process's partition.

9.3) Why Limit?
The limit provies the missing information:
BA → where it starts
Limit → how large it is

- THUS: Base + Limit completely describes the process's legal memory range.

10) Partition Placement Policy
- A rule used to determine which partition a process should be assigned to.

WHY: - Placing small processes into huge partition, would prevent future large process from using the huge partition and even function.
Partition placement affects both current memory usage and future allocation possibilities.

11) Static unequal Sized Partitioning
PROS: Better Support for Different Process Sizes, Less internal fragmentation.
CONS: Increased Complexity (decision-making), Partitions Are Still Static, The Largest Partition Still Sets a Limit



File08
12) Dynamic, Variable-Sized Partitioning
- Unlike static partitioning, in which partition sizes are decided before the system knows the processes that will run.
Dynamic, variable-sized partitioning begins with all of main memory as one large block of unused memory called FREE SPACE.

When a process needs to enter memory:
1) The OS finds enough free space.
2) A partition is created specifically for that process.
3) The partition is sized according to the process's memory requirement.
4) The remaining memory continues to be free space.

THUS:
- The number of partition changes as processes enter and leave memory.
- Partitions can have different sizes.
- Partition sizes are determined when the process is loaded, rather than beforehand.

Dynamic Partitioning = Partitions are created on demand and sized to the process.
- Flexible, but new memory-management issues.

- An issues arises when processes are swapped out and replaced by processes of different sizes.
- If a smaller process replaces P1, part of P1's old region may remain unused.
Creating multiple separate regions of free memory, called HOLES.


13) External Fragmentation
- Occurs when free memory is divided into multiple separate holes between allocated partitions instead of existing as one
contiguous block.
EX:
┌──────────────┐
│      P1      │
├──────────────┤
│ FREE 300     │
├──────────────┤
│      P2      │
├──────────────┤
│ FREE 300     │
├──────────────┤
│      P3      │
├──────────────┤
│ FREE 300     │
└──────────────┘
- There are 900 units of free memory, but if a new process requires 800 units of contiguous memory, it cannot be loaded.

NOTE:
Total free memory can be large enough for a process, but the process still cannot be loaded because the free memory is fragmented into separate holes.

14) Internal Vs. External
With dynamic partitioning, partitions are sized exactly according to the process.
- Thus internal fragmentation is eliminated.
- External fragmentation becomes a major issue.

15) Tracking Free Space
- The OS must keep track of where the free memory is.
Additionally a new manner to determine which free space should be used when a new process arrives.

One Method is a "linked list" of free spaces.
- Each element in the list represents one hole.

Each element stores:
1) Base address
- Where the free space begins.

2) Size
- How large the free space is.

3) Pointer
- Points to the next free-space entry in the linked list.

15.1) Updating the Free List After Allocation
Suppose a process requires 500 units and is placed into:
Base = 100
Size = 600

The Remaining free space:
600 - 500 - 100

New free-space entry:
Base = 100 + 500 = 600
Size = 100

Thus:
Before:
(100, 600)

After:
(600, 100)


General Rule:
IF Process size < hole size
THEN:
new base = old base + process size
new size = old size - process size

IF Process size = hole size
- Then the hole is completely consumed and the entry is removed from the free list.


16) Parition Placement Policies
- The OS needs a policy for deciding: "Which free hole should receive the next process?"

16.1) First Fit(FF)
RULE - Start at the beginning of the free list and choose the first hole large enough.
EX:
Free List:
(100, 600)
(850, 1300)
(3000, 800)
(4200, 600)

Process requires: 800
Check:
- 600 -> too small
- 1300 -> large enough
THUS: Place process in the 1300-unit hole.

PROS: Simple, fast, Time Complexity Low
CONS: Early memory becomes croweded with small fragments


16.2) Next Fit (NF)
Rule - Works like First Fit, except the search does not restart from the beginning.
INSTEAD: Start searching from where the previous search ended.

PROS: Distributed allocations evenly, addresses the crowd at the begining.


16.3) Best Fit (BF)
RULE - Search the entire free list and choose the smallest hole that is still large enough.
EX:
Suppose the available holes are:
600
1300
800
600

Process requires: 800
Best Fit Chooses: 800
- Since it is the smallest hole that can hold the process.

CONS: Slower (Time Complexity higher), leave unusable holes, contribute to external fragementation.


16.4) Worst Fit (WF)
RULE - Search the entire free list and choose the largest hole that can hold the process.
EX:
Holes:
600
1300
800
600

Process requires: 800
The Largest suitable hole is: 1300
Worst Fit Chooses: 1300

PROS: Space leftover is useful for other processes.
CONS: Slower (Time Complexity Higher).

17) Advantages and Disadvantages of Dynamic Partitioning
PROS: Flexibility (Partitions are sized according to the processes that actually arrive.)
CONS: More complicated management (The OS must maintain the free-space structure during: Allocation, Deallocation, Swapping).
- External Fragmentation (Free memory scattered as holes, holes are large enough, depends on placement policy).

18) Absolute Addresses won't Work
- An absolute address refers to an actual physical location in main memory.
The program CANNOT know in advance where its partition will be placed.

THUS: Absolute addressing is not partical for dynamicaly partitioned memory.
- Reducing portability under static partitioning, since the program would depend on a particular physical memory layout.

19) Logical Addresses
- Instead of providing the program an absolute physical address, the program uses an offset relative to the beginning of
its own partition.
EX:
Logical address = 16
- Signifies: Access the memory location 16 units from the beginning of my partition.
- The program does NOT need to know where its partition physically begins.

19.1) Address Translation
- Basic translation is: Physical Address = Base Addres + Logical Address
Then the system checks whether the resulting address is within the process's permitted limit.

Supose:
Base = 100
Logical Address = 16

Calculate:
Physicall address = 100 + 16 = 116

THUS, the process accessses: Physical address 116

MEMORIZE:
Physical Address = Base + Logical Address

19.2) Translation + Protection
- Base and Limit information helps with "memory protection."

The system:
1) Takes the logical address.
2) Adds the base.
3) Produces the physicall address.
4) Checks whether the requested location is within the process's allowed partition.

20) Relocation
- Since programs use relative/logical addresses, they DON'T depend on a particular physical locatio.
Allowing for "relocation".

Suppose P1 originally has:
Base = 1000

P1 generates:
Logical address = 50

Physical address:
1000 + 50 = 1050

Later, P1 is moved to:
Base = 3000

The program still generates:
Logical address = 50

The system now calculates:
3000 + 50 = 3050

- The program itself does not need to change.


Q1) Consider a memory of size 8KB (8192 bytes) that allows dynamic, variable sized partitioning among processes and uses a linked list to keep track of free spaces (hereafter referred to as the free list) in the memory at any given time. Assume that there are 6 processes and assume that their memory size requirements (in bytes) are as given below:

P1: 500,  P2: 600,  P3: 1300,  P4: 2000,  P5: 100,  P6: 200

Assume that the initial state of the free list is as shown below (BA is the base address and Sz is the size of each free space):

BA: 0; Sz: 1100 → BA: 1200; Sz: 600 → BA: 2000; Sz: 1800 → BA: 6000; Sz 400


Q2) Free list starts in its initial state, use the next fit policy. Identify the base address of the region.
• Block 1: BA: 0, Sz: 1100
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 2000, Sz: 1800
• Block 4: BA: 6000, Sz: 400

1. Allocation of P1 (Size: 500)
It fits 1100 >= 500
Allocation BA: 0
- Remaining Free List Update: Block 1 becomes BA = 0 + 500 = 500 and Sz = 1100 - 500 = 600
UPDATE:
• Block 1: BA: 500, Sz: 600
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 2000, Sz: 1800
• Block 4: BA: 6000, Sz: 400

2. Allocation of P3 (Size: 1300)
It fits 1800 >= 1300
Allocation BA: 2000
UPDATE:
• Block 1: BA: 500, Sz: 600
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 3300, Sz: 500
• Block 4: BA: 6000, Sz: 400

3. Allocation of P2 (Size: 600)
It fits 600 >= 600 (HOWEVER, we always START from the BEGINNING)
Allocation: 500
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 3300, Sz: 500
• Block 4: BA: 6000, Sz: 400

3. Allocation of P6 (Size: 200)
It fits 600 >= 200
Allocation: 1200
• Block 2: BA: 1400, Sz: 400
• Block 3: BA: 3300, Sz: 500
• Block 4: BA: 6000, Sz: 400


Q3) Free List starts in it initial state, use the worst fit policy/ Identify the base address of the region.
• Block 1: BA: 0, Sz: 1100
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 2000, Sz: 1800
• Block 4: BA: 6000, Sz: 400

1. Allocation of P4 (Size: 2000)
The Biggest 1800 >= 2000 doesn't fit, thus:
Allocation: cannot be accommodated
• Block 1: BA: 0, Sz: 1100
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 2000, Sz: 1800
• Block 4: BA: 6000, Sz: 400

2. Allocation of P2 (Size: 600)
The Biggest: 1800 >= 600
Allocation 2000
• Block 1: BA: 0, Sz: 1100
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 2600, Sz: 1200
• Block 4: BA: 6000, Sz: 400

3. Allocation of P6 (Size: 200)
The Biggest: 1200 >= 200
Allocation: 2600
• Block 1: BA: 0, Sz: 1100
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 2800, Sz: 1000
• Block 4: BA: 6000, Sz: 400

4. Allocation of P1 (Size: 500)
The Biggest: 1100 >= 500
Allocation: 0
• Block 1: BA: 500, Sz: 600
• Block 2: BA: 1200, Sz: 600
• Block 3: BA: 2800, Sz: 1000
• Block 4: BA: 6000, Sz: 400

---------------------------------------------------------------------------------------------------------------------------------

Module 8-1 (Paging)
File09, File10, File11

1) Pages

Issue:
- External Fragmentation reduces memory utilization because some free memory cannot be used when a process requires
one contiguous region.
SOLUTION: Compaction, Non-Contiguous Allocation

1.1) Compaction
- Moving allocated partitions so that they become adjacent to one another.
GOAL: To combine the separate holes into one larger contiguous free space.

: Require Relocation
- Partitions have to be move from its physical location.
THUS, the system needs partition relocation.
REASON: Due to relative/logical addressing.
CONS: Becomes expensive, to repeatedly move partitions and their physical location, to maintain address info.

1.2) Non-Contiguous Allocation
- Changing the requirement that a process occupy one contiguous partition.
IDEA: Split a process's memory footprint into multiple pieces.
Then place those pieces into different free regions of memory.

Suppose:
P1 = 90 units

Available Holes:
50 units
20 units
40 units

- No single hole is large enough.
But: 50 + 40 = 90

- The process is potentially divided into:
Part 1 = 50
Part 2 = 40

- And placed into two separate locations. Allowing for non-contiguous memory.

ISSUE: There is no longer a single base address or limit instead (multiple):
Process
   │
   ├── Part 1 → Base + Limit
   │
   ├── Part 2 → Base + Limit
   │
   └── Part 3 → Base + Limit

- THUS a more sophisticated memory-management mechanism is needed: (Paging).


2) Paging
- Allows a process to be divided into smaller pieces and allow those pieces to be mapped to different locations
in physical memory.

Structure:
Logical Memory
      ↓
   Pages
      ↓
Mapped to
      ↓
Physical Memory
      ↓
Page Frames


- Page -> Block of process's logical memory.
- Page Frame -> Equal-sized block of physical memory.

2.1) Page Frames
- Dividing physical memory into equal-sized blocks.

2.2) Pages
- The process's logical memory
Physical memory → Page Frames
Logical process memory → Pages

NOTE: Pages and page frames are the same size.

2.3) Mapping Pages to Page Frames:
1. The process is divided into pages.
2. Physical memory is already divided into page frames.
3. Each page is mapped to a free page frame.
- Pages do not have to be placed next to one another.

3) Contiguous vs. Non-contiguous Paging
C- Pages happens to be next to each other.
NonC- Pages are scattered across memory.

Which one occurs depends on:
- The "state" of main memory when the process arrives.
- The "policy" used to allocate the page frames.

3.1) Page Tables
- Pages can be mapped to different physical frames, THUS, the OS needs to keep track of those mappings using: PAGE TABLES.
Page Table - Records the relationship between: Logical Page -> Physical Page Frame
EX:
Logical Page 0 → Frame 1
Logical Page 1 → Frame 4
Logical Page 2 → Frame 10

- One Page Table Per Process
Since different processes can use the same logical page numbers.

3.2) Tracking Free Page Frames
- The OS maintains the physical page frames available through a: Linked list of free page frames


4) Paging and Internal Fragmentation
- Paging does not completely eliminate internal fragmentation.
Because page frames have a fixed size.
- As a process's memory footprint might be an exact multiple of the page size.

- Internal fragmentation can occur in the last page frame of a process.
Because the process's memory footprint may not be an exact multiple of the page size. (In 20.)

THUS: Paging can still have internal fragmentation, but it is generally limited to unused space in the final page/frame of a process.

4.1) Paging Difference From Static Partitioning
- Both divides physical memory into equal-sized regions.

Static partitioning
A partition needs to be large enough to contain the entire process.

Paging
The process itself is divided into multiple pages.




File10
5) Address Translation
- In paging, a logical address has two parts:
: Page Number (pn) - identifies which logical page.
: Offset (off) - identifies the location within that page.

A physical address also has two parts:
: Page frame number (fn) - idnetifies the physical frame.
: Offset (off)
 - location within that frame.

 NOTE: Page number -> Page table -> Page frame number.
 - THe offset stays the same because pages and page frames are the same size.

 6) Basic Translation Process
 Logical address:
[Page Number | Offset]

↓ Page table

Physical address:
[Frame Number | Offset]

6.1) EX
Suppose a process generates an address with:
- Page number = 1
- Offset = some value

The page table says:
Page 1 → Frame 4

Therefore:
- Frame number becomes 4
- Offset remains unchanged

So:
Logical: [1 | offset]
Physical: [4 | offset]

Common trap
Do not change the offset during translation.

7) Binary Addresses
Computer memory addresses are represented in binary.
With n bits:
Number of possible values = 2ⁿ

Examples:
2 bits → 4 values
3 bits → 8 values
4 bits → 16 values

8) Determining Address Size
The number of bits depends on how many possible memory locations must be represented.
2 KB of physical memory:
32 KB = 2¹⁵ bytes

Therefore, you need:
15-bit physical addresses
because 15 bits can represent 2¹⁵ different addresses.


8.1) Page Size Determines the Offset
Suppose the page size is:
4 KB = 2¹² bytes

Therefore, the offset requires:
12 bits

Important
The least significant 12 bits are the offset.

So:
[Page/Frame Number | 12-bit Offset]
The same offset size is used for both logical and physical addresses.


8.2) Finding the Number of Page Frames
Formula:
Physical memory ÷ Page-frame size

Example:
Physical memory = 32 KB = 2¹⁵
Page size = 4 KB = 2¹²

Therefore:
2¹⁵ ÷ 2¹² = 2³ = 8 page frames

Since there are 8 frames:
Frame number requires 3 bits.


8.3) Finding the Number of Logical Pages
Formula:
Logical address space ÷ Page size

Example:
Logical address space = 16 KB = 2¹⁴
Page size = 4 KB = 2¹²

Therefore:
2¹⁴ ÷ 2¹² = 2² = 4 pages

Since there are 4 pages:
Page number requires 2 bits.

Address structure

Logical address:
[2-bit Page Number | 12-bit Offset]

Physical address:
[3-bit Frame Number | 12-bit Offset]

8.4) Concrete Address Translation
Suppose the logical address contains:
- Page number = 1
- Offset = 101001001010

The page table gives:
Page 1 → Frame 3

Convert frame 3 to the required 3-bit representation:
3 = 011

Therefore:
Logical:
01 | 101001001010

Physical:
011 | 101001001010

The offset is copied exactly; only the page number is replaced by the corresponding frame number.

9) Solvng Address-Translation Problems:
Step 1 — Find offset bits
Look at the page size.
Example:
4 KB = 2¹² → 12 offset bits


Step 2 — Find number of pages
Logical memory ÷ page size


Step 3 — Find page-number bits
Use:
2ⁿ = number of pages


Step 4 — Find number of frames
Physical memory ÷ page size


Step 5 — Find frame-number bits
Use:
2ⁿ = number of frames


Step 6 — Split the logical address
[Page Number | Offset]


Step 7 — Look up the page number
Use the page table.


Step 8 — Replace page number with frame number
Keep the offset unchanged.



File11
10) Page Table Entry Structure
Possible information includes:
- Page frame number — identifies the physical frame.
- Present/absent bit — indicates whether the mapping is valid.
- Protection bits — control read, write, and execute permissions.
- Modified bit — indicates whether the page has been changed.
- Referenced bit — indicates whether the page has been accessed.
- Caching bit — controls whether the page can be cached.

The exact structure depends on the operating system.

11) Page Table Performance Problem:
The page table is stored in main memory.

This creates an issue: every memory request may require: Accessing the page table to find the frame, Accessing the actual memory location. Thus two memory addresses needed to acquire.

12) Translation Lookaside Buffer (TLB)
- is a small, fast cache containing frequently used page → frame mappings.
The TLB reduces the need to repeatedly access the page table in main memory.

13) TLB Hit
A TLB hit occurs when the requested page's mapping is already in the TLB.

Logical address
→ Check TLB
→ Page-to-frame mapping found
→ Use frame number
→ Create physical address

14) TLB Miss
A TLB miss occurs when the requested page is not in the TLB.

Logical address
→ Check TLB
→ Mapping not found
→ Check page table
→ Find frame number
→ Create physical address

The offset remains unchanged during the translation.
TLB miss does NOT automatically mean page fault.
A TLB miss simply means the mapping wasn't found in the TLB.

15) Demand Paging Is Needed
Previously, we assumed that all pages belonging to a process had to be in main memory before the process could execute.
Limitations: Fewer process fit in memory, process data size is limited by phyiscal memory.

Demand paging allows only the pages currently needed by a process to be loaded into main memory.
: Load what is needed → Run → Load additional pages when needed

16) Page Fault
A page fault occurs when a process requests a page that is not currently in main memory.

1) Process requests a page.
2) System checks whether the page is in memory.
3) Page is missing → page fault.
4) System loads the missing page into memory.
5) The mapping may be added to the TLB.
6) The instruction that caused the fault is restarted.
7) The memory access can now succeed.


Important distinction
| Situation      | Meaning                             |
| -------------- | ----------------------------------- |
| **TLB hit**    | Mapping found in TLB                |
| **TLB miss**   | Mapping not found in TLB            |
| **Page fault** | Requested page isn't in main memory |

17) Virtual Memory
Demand paging allows a process to have a logical address space larger than physical main memory.

Suppose:
- Physical memory = 32 KB
- Process logical memory = 64 KB

The process is larger than physical memory.
This is possible because only a subset of its pages needs to be in physical memory at one time.

18) Logical Pages vs. Physical Frames
With virtual memory, there can be:
More logical pages than physical page frames.

- Logical page number can require more bits.
- Physical frame number can require fewer bits.
64 KB logical memory:
2¹⁶ bytes

4 KB pages:
2¹² bytes

Number of logical pages:
2¹⁶ ÷ 2¹² = 2⁴ = 16 pages

Therefore:
4-bit logical page number

Physical memory:
32 KB = 2¹⁵

Page size:
4 KB = 2¹²

Number of frames:
2¹⁵ ÷ 2¹² = 2³ = 8 frames

Therefore:
3-bit physical frame number


Address structures
Logical/virtual address:
[4-bit Page Number | 12-bit Offset]

Physical address:
[3-bit Frame Number | 12-bit Offset]


Q1) For the following questions, consider a paged memory system that has a physical main memory size of 64KB (2^16) and a page frame size of 8KB (2^13). Consider a process P whose logical address space is 64KB (2^16).

Important Note: If an answer requires an exponent, use the ^ character.  For example: 2^16 would be entered as 2^16.

Q2) How many physical page frames are there in the above paged memory system?
Physical Page Frams: Physical Main Memory Size / Page Frame Size
: 2^16 / 2^13 = 2^16-13  = 2^3

How many bits are needed to represent a physical page frame number in the system?
: 3 bits

Q3) Given the logical address 0x969C, what will be the logical page number issued by a process P?
What is the corresponding physical frame number

STEP 1: CONSTANTS & SYSTEM PARAMETERS
============================================================
* Logical Address Space Size  = 64 KB (2^16 Bytes) -> 16-bit addresses
* Page / Frame Size           = 8 KB  (2^13 Bytes) -> 13-bit offset
* Logical Page Number Bits    = 16 bits - 13 bits  = 3 bits

============================================================
STEP 2: BINARY BREAKDOWN OF THE LOGICAL ADDRESS
============================================================
Target Logical Address: 0x969C

Hex Digit to Binary Conversion:
  9 -> 1001
  6 -> 0110
  9 -> 1001
  C -> 1100

Combined 16-bit Address String:
  1001011010011100

============================================================
STEP 3: EXTRACTING BITS (3 Upper Bits = Page, 13 Lower = Offset)
============================================================
Binary Isolate:
  [100] [1011010011100]
   ^^^   ^^^^^^^^^^^^^
   Page     Offset

Convert Page Bits (100) back to Hexadecimal:
  Binary 100_2 -> Decimal 4 -> Hexadecimal: 0x4

============================================================
STEP 4: PAGE TABLE LOOKUP
============================================================
Look up Page index '0x4' inside the Page Table mapping:
  [Page 0x0] -> Frame 0x1
  [Page 0x1] -> Frame 0x6
  [Page 0x2] -> Frame 0x3
  [Page 0x3] -> Frame 0x5
  [Page 0x4] -> MATCH FOUND -> Frame 0x2

============================================================
FINAL OUTPUT VALUES
============================================================
* Logical Page Number: 0x4
* Physical Frame Number: 0x2


Q4) For the following questions, consider a paged memory system that has a physical main memory size of 1MB (2^20) and a page frame size (and hence page size) of 32KB (2^15). Consider a process P whose logical address space is 512KB (2^19).
How many physical page frames are there in the above paged memory system?
How many bits are needed to represent a physical page frame number in the system?
============================================================
QUESTION 4: PHYSICAL FRAME CALCULATIONS
============================================================
* Formula for Number of Physical Frames:
  Physical Main Memory Size / Page Frame Size
  = 2^20 / 2^15 = 2^(20 - 15) = 2^5

* Formula for Physical Frame Number Bits:
  log2(Number of Physical Frames)
  = log2(2^5) = 5 bits

------------------------------------------------------------
>> Answer 1 (Exponential form): 2^5
>> Answer 2 (Number of bits):    5
============================================================

============================================================
QUESTION 5: LOGICAL PAGE CALCULATIONS
============================================================
* Formula for Number of Logical Pages:
  Logical Address Space Size / Page Size
  = 2^19 / 2^15 = 2^(19 - 15) = 2^4

* Formula for Logical Page Number Bits:
  log2(Number of Logical Pages)
  = log2(2^4) = 4 bits

------------------------------------------------------------
>> Answer 1 (Exponential form): 2^4
>> Answer 2 (Number of bits):    4
============================================================

Q5)
Given the logical address 0x 69656, what will be the logical page number issued by a process P?
What is the corresponding physical frame number?
What is the hexadecimal physical address?

============================================================
STEP 1: CONSTANTS & SYSTEM PARAMETERS
============================================================
* Page / Frame Size           = 32 KB (2^15 Bytes) -> 15-bit offset
* Logical Address Space Size  = 512 KB (2^19 Bytes) -> 19-bit addresses
* Logical Page Number Bits    = 19 bits - 15 bits  = 4 bits

============================================================
STEP 2: BINARY BREAKDOWN OF THE LOGICAL ADDRESS
============================================================
Target Logical Address: 0x69656

Hex Digit to Binary Conversion:
  6 -> 0110
  9 -> 1001
  6 -> 0110
  5 -> 0101
  6 -> 0110

Combined 19-bit Address String (omitting the leading 0 bit):
  1101001011001010110

============================================================
STEP 3: EXTRACTING BITS (4 Upper Bits = Page, 15 Lower = Offset)
============================================================
Binary Isolate:
  1101 | 001011001010110
  ^^^^   ^^^^^^^^^^^^^^^
  Page       Offset

* Convert Page Bits (1101) to Hexadecimal:
  Binary 1101_2 -> Decimal 13 -> Hexadecimal: 0xD

* Convert Offset Bits (001011001010110) to Hexadecimal:
  Binary 001011001010110_2 -> Hexadecimal: 0x1656

============================================================
STEP 4: PAGE TABLE LOOKUP
============================================================
Look up Page index '0xD' inside the Page Table mapping:
  ...
  [Page 0xC] -> Frame 0x12
  [Page 0xD] -> MATCH FOUND -> Frame 0x15
  [Page 0xE] -> Frame 0x3
  ...

============================================================
STEP 5: PHYSICAL ADDRESS GENERATION
============================================================
* Physical Frame Number (PFN) = 0x15 (Binary: 10101)
* Shift PFN left by 15 offset bits: 
  10101_2 << 15 = 1010 1000 0000 0000 0000_2 = 0xA8000
* Combine shifted PFN with the Page Offset (0x1656):
  0xA8000 + 0x1656 = 0xA9656

============================================================
FINAL OUTPUT VALUES
============================================================
* Logical Page Number:      0xD
* Physical Frame Number:    0x15
* Physical Address (Hex):   0xA9656
============================================================


Q6)
For the following questions, consider a system that has a physical main memory size of 32KB (2^15) and a page frame size (and hence page size) of 4KB (2^12). Assume that the system uses pure demand paging and that the system supports up to 16-bit logical/virtual addresses
============================================================
QUESTION 7: PHYSICAL FRAME CALCULATIONS
============================================================
* Formula for Number of Physical Frames:
  Physical Main Memory Size / Page Frame Size
  = 2^15 / 2^12 = 2^(15 - 12) = 2^3 (8 frames total)

* Formula for Physical Frame Number Bits:
  log2(Number of Physical Frames)
  = log2(2^3) = 3 bits

------------------------------------------------------------
>> Answer 1 (Exponential form): 2^3
>> Answer 2 (Number of bits):    3
============================================================

============================================================
QUESTION 8: LOGICAL PAGE CALCULATIONS
============================================================
* Formula for Number of Logical Pages:
  Logical Address Space Size / Page Size
  = 2^16 / 2^12 = 2^(16 - 12) = 2^4 (16 pages total)

* Formula for Logical Page Number Bits:
  log2(Number of Logical Pages)
  = log2(2^4) = 4 bits

------------------------------------------------------------
>> Answer 1 (Exponential form): 2^4
>> Answer 2 (Number of bits):    4
============================================================

============================================================
QUESTION 9: MAXIMUM VALID ENTRIES
============================================================
* Core Concept: 
  A page table entry is only valid if it points to a physical 
  frame in main memory. Because the entire system only has 
  8 physical frames (2^3), the page table can hold at most 
  8 valid mappings at any given time.

------------------------------------------------------------
>> Answer: 8
============================================================

============================================================
QUESTION 10 & 11: ADDRESS TRANSLATION & MEMORY LOOKUP
============================================================
STEP 1: BINARY BREAKDOWN OF THE LOGICAL ADDRESS
* Target Logical Address: 0xA87A

* Hex Digit to Binary Conversion:
  A -> 1010
  8 -> 1000
  7 -> 0111
  A -> 1010

* Combined 16-bit Address String:
  1010100001111010

STEP 2: EXTRACTING BITS (4 Upper Bits = Page, 12 Lower = Offset)
* Binary Isolate:
  1010 | 100001111010
  ^^^^   ^^^^^^^^^^^^
  Page      Offset

* Convert Page Bits (1010) to Hexadecimal:
  Binary 1010_2 -> Hexadecimal: 0xA

* Convert Page Bits (1010) to Decimal:
  Binary 1010_2 -> Decimal: 10

STEP 3: DEMAND PAGING PAGE TABLE LOOKUP
* Look up Page index 10 (0xA) inside the provided Page Table:
  ...
  [Page 9]  -> Frame 3
  [Page 10] -> MATCH FOUND -> Frame '-' (Empty / Invalidation Entry)
  [Page 11] -> Frame '-'
  ...

* Result: 
  The frame entry contains a dash '-', which indicates a Page Fault. 
  The page is not currently resident in physical main memory.

------------------------------------------------------------
>> Q10 (Logical Page in Hex): 0xA
>> Q11 (Logical Page in Dec): 10
>> Q11 (Resident in memory?): no
============================================================


---------------------------------------------------------------------------------------------------------------------------------


Q1) FIFO
The sequence of data pages requested by the hyper-drive process is as follows:
9, 2, 5, 8, 3, 9, 3, 5, 2, 9, 5, 8, 9, 3, 9, 2, 5, 8

For each reference in the sequence, identify whether it will cause a page fault (F) or a page hit (H). Assume that all page frames are initially empty and that you must assign pages to frames in Greek alphabetical order (Alpha, Beta, Gamma, and Delta).

Protocol Reminder: Evict the page that has been residing in the main memory the longest.

Sequence	Status (H/F)	Page Frame Loaded Into
9	        F	            ALPHA
2	        F	            BETA
5	        F	            GAMMA
8	        F	            DELTA
3	        F	            ALPHA
9	        F	            BETA
3	        H	            ALPHA
5	        H	            GAMMA
2	        F	            GAMMA
9	        H	            BETA
5	        F	            DELTA
8	        F	            ALPHA
9	        H	            BETA
3	        F	            BETA
9	        F	            GAMMA
2	        F	            DELTA
5	        F	            ALPHA
8	        F	            BETA

Sequence  | Status (H/F) | Page Frame Loaded Into | Active Memory State
----------+--------------+------------------------+---------------------
    9     |      F       |        ALPHA           | [9, -, -, -]
    2     |      F       |        BETA            | [9, 2, -, -]
    5     |      F       |        GAMMA           | [9, 2, 5, -]
    8     |      F       |        DELTA           | [9, 2, 5, 8]
----------+--------------+------------------------+---------------------
    3     |      F       |        ALPHA           | [3, 2, 5, 8]  (Evicted 9)
    9     |      F       |        BETA            | [3, 9, 5, 8]  (Evicted 2)
    3     |      H       |        ALPHA           | [3, 9, 5, 8]
    5     |      H       |        GAMMA           | [3, 9, 5, 8]
----------+--------------+------------------------+---------------------
    2     |      F       |        GAMMA           | [3, 9, 2, 8]  (Evicted 5)
    9     |      H       |        BETA            | [3, 9, 2, 8]
    5     |      F       |        DELTA           | [3, 9, 2, 5]  (Evicted 8)
    8     |      F       |        ALPHA           | [8, 9, 2, 5]  (Evicted 3)
----------+--------------+------------------------+---------------------
    9     |      H       |        BETA            | [8, 9, 2, 5]
    3     |      F       |        BETA            | [8, 3, 2, 5]  (Evicted 9)
    9     |      F       |        GAMMA           | [8, 3, 9, 5]  (Evicted 2)
    2     |      F       |        DELTA           | [8, 3, 9, 2]  (Evicted 5)
----------+--------------+------------------------+---------------------
    5     |      F       |        ALPHA           | [5, 3, 9, 2]  (Evicted 8)
    8     |      F       |        BETA            | [5, 8, 9, 2]  (Evicted 3)


Q2) Second Chance
Sequence  | Status (H/F) | Page Frame Loaded Into | Active Memory State
----------+--------------+------------------------+---------------------
    9     |      F       |        ALPHA           | [9*, -, -, -]
    2     |      F       |        BETA            | [9*, 2*, -, -]
    5     |      F       |        GAMMA           | [9*, 2*, 5*, -]
    8     |      F       |        DELTA           | [9*, 2*, 5*, 8*]
----------+--------------+------------------------+---------------------
    3     |      F       |        ALPHA           |  (Evicted 9)
    9     |      F       |        BETA            |  (Evicted 2)
    3     |      H       |        ALPHA           |
    5     |      H       |        GAMMA           |
----------+--------------+------------------------+---------------------
    2     |      F       |        DELTA           |  (Evicted 8)
    9     |      H       |        BETA            |
    5     |      H       |        GAMMA           |
    8     |      F       |        DELTA           |  (Evicted 2)
----------+--------------+------------------------+---------------------
    9     |      H       |        BETA            |
    3     |      H       |        ALPHA           |
    9     |      H       |        BETA            |
    2     |      F       |        GAMMA           |  (Evicted 5)
----------+--------------+------------------------+---------------------
    5     |      F       |        DELTA           |  (Evicted 8)
    8     |      F       |        ALPHA           |  (Evicted 3)


Q3) LRU
Step  | Request  | Status (H/F) | Target Frame Loaded Into | Active Memory State (A, B, G, D)
------+----------+--------------+--------------------------+-----------------------------
 1    |    9     |      F       |        ALPHA             | [9, -, -, -]
 2    |    2     |      F       |        BETA              | [9, 2, -, -]
 3    |    5     |      F       |        GAMMA             | [9, 2, 5, -]
 4    |    8     |      F       |        DELTA             | [9, 2, 5, 8]
 5    |    3     |      F       |        ALPHA             | [3, 2, 5, 8]  (Evicted 9)
 6    |    9     |      F       |        BETA              | [3, 9, 5, 8]  (Evicted 2)
 7    |    3     |      H       |        ALPHA             | [3, 9, 5, 8]  (Hit)
 8    |    5     |      H       |        GAMMA             | [3, 9, 5, 8]  (Hit)
 9    |    2     |      F       |        DELTA             | [3, 9, 5, 2]  (Evicted 8)
 10   |    9     |      H       |        BETA              | [3, 9, 5, 2]  (Hit)
 11   |    5     |      H       |        GAMMA             | [3, 9, 5, 2]  (Hit)
 12   |    8     |      F       |        ALPHA             | [8, 9, 5, 2]  (Evicted 3)
 13   |    9     |      H       |        BETA              | [8, 9, 5, 2]  (Hit)
 14   |    3     |      F       |        DELTA             | [8, 9, 5, 3]  (Evicted 2)
 15   |    9     |      H       |        BETA              | [8, 9, 5, 3]  (Hit)
 16   |    2     |      F       |        GAMMA             | [8, 9, 2, 3]  (Evicted 5)
 17   |    5     |      F       |        ALPHA             | [5, 9, 2, 3]  (Evicted 8)
 18   |    8     |      F       |        DELTA             | [5, 9, 2, 8]  (Evicted 3)
-----------------------------------------------------------------------------------------



File12, File13, File14
1) Page Replacement Policies
For allocating a new page, the choice is straightforward because all page frames are the same size. Any available frame can be used.
The more important policy question is what to do when no empty frame exists.

2) Evicting a Page
When memory is full and a new page must be loaded:
1) Select an existing page to evict from main memory.
2) Remove that logical page from its physical page frame.
3) Write the page back to disk if it has been modified since being loaded.
4) Place the newly requested page into the now-available frame.


3) First-In, First-Out (FIFO) Page Replacement
It follows the same basic principle as a queue:
First page in → First page out

PROS: Simple, Fair, based on time in memory.


4) Second Chance Page Replacement
A a modification of FIFO designed to overcome FIFO's main limitation: FIFO may remove a page that has been referenced recently.

When a page needs to be evicted:
1) Look at the oldest page.
2) Check its reference bit.
3) If the bit is 0 → evict the page.
4) If the bit is 1 → give the page a second chance:
: Reset its reference bit to 0.
: Move it to the end of the list as the newest page.
: Examine the next oldest page.

The process continues until a page with a reference bit of 0 is found.

- FIFO + Reference Bit
Instead of automatically removing the oldest page:
Oldest + reference bit 0 → Replace

Oldest + reference bit 1 → Reset bit, move to back, give second chance
This allows recently referenced pages to remain in memory longer than they would under ordinary FIFO.

5) Least Recently Used (LRU)
Replaces the page that was used least recently.

LRU asks:
"Which page has gone the longest without being used?"
- Considering recent page usage, unlike FIFO.

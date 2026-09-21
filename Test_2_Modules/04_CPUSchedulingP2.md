04 - CPU Scheduling

Part 2: Scheduling Policies for Batch Systems:


1. Overview: Scheduling Policies for Batch Systems
Two classic scheduling policies for batch systems:
1) First Come, First Served (FCFS)
2) Shortest Job First (SJF)
    - Non-preemptive SJF
    - Preemptive SJF, also caled Shortest Remaining Time First (SRTF)

- Different scheduling policies make different decisions about which ready job should receive the CPU next.

Part 1 introduced why scheduling is necessary and the different objectives of scheduling. 
Part 2 now focuses on specific policies used to make those scheduling decisions.

---------------------------------------------------------------------------------------------------------------------------------

2. CPU Bursts and I/O Bursts
Two major types of activity a job performs.

CPU Burst
- Is a period during which a job is actively using the CPU.
EX: CPU burst = 20 cycles.
- The job needs the CPU Continuously for 20 cycles during that portion of its execution.


I/O Burst / I/O Wait
- Occurs when a job is waiting for input/output like:
Reading a file
Waiting for a device
Performing another I/O operation

- During this period, the job does not need the CPU.



Jobs Alternate Between Them
- Becomes particularly important with FCFS because a job that blocks for I/O leaves the ready queue and later returns to
the back of the queue.

---------------------------------------------------------------------------------------------------------------------------------

3. Preemptive vs. Non-Preemptive Scheduling
- Distinction becomes important as SJF has both versions.
Meanwhile, FCFS is non-preemptive.


Non-Preemptive
- Once a job starts running, it continues using the CPU until it:
• Blocks for I/O
• Terminates
• Voluntarily yields the CPU
The scheduler cannot interrupt it because another job arrives.


Preemptive
- Running job can be interrupted before its CPU burst finishes.
The CPU can then be given to another job.

---------------------------------------------------------------------------------------------------------------------------------

4. First Come, First Served (FCFS)
- Is a non-preemptive scheduling policy that executes jobs according to the order in which they enter the ready queue.

EX:
First person in line → served first
Second person → served second
Third person → served third

Scheduling EX:
First job in queue → CPU first
Second job → CPU second
Third job → CPU third

---------------------------------------------------------------------------------------------------------------------------------

5. How FCFS Works
The scheduler maintains a ready queue.

When a job becomes ready:
1) It enters the queue.
2) It is placed at the end.
3) The job at the front/head is selected.
4) Beacause FCFS is non-preemptive, the job continues until it:
    - Blocks,
    - Terminates,
    - Or yields.


IMPORTANT Rule: Returning from I/O
- When a process finishes its I/O: It goes to the BACK of the ready queue.
DOES NOT return to its old position.

---------------------------------------------------------------------------------------------------------------------------------

6. FCFS Examples

| Job | Arrival | CPU/I/O Pattern                           |
| --- | ------: | ----------------------------------------- |
| J1  |       0 | CPU 20 → I/O 4 → CPU 15 → I/O 10 → CPU 10 |
| J2  |       3 | CPU 4 → I/O 4 → CPU 4                     |
| J3  |       6 | CPU 3                                     |

Step 1 - Time 0
0 → 20: J1
- Only J1 has arrived and runs for its entire 20-cycle CPU burst.
Even if J2 and J3 arrive during this period, FCFS is non-preemptive, and cannot interrupt J1.


Step 2 - Time 20
J1: I/O from 20 → 24
- J1 finishes its CPU burst and enters I/O.
- J2 has been waiting since time 3, so J2 runs:
20 → 24: J2


Step 3 - Time 24
J2 finishes its first CPU burst and enters I/O:
J2: I/O from 24 → 28
J3 is waiting, so:
24 → 27: J3
- J3 finishes completely at time 27.


Step 4 - Time 27
- J1 finished its I/O at time 24 and is ready again.
J1 runs its second CPU burst:
27 → 42: J1

Then J1 enters another I/O wait:
42 → 52


Step 5 - Time 42
- J2 finished its I/O at time 28 and has been waiting in the ready queue.
THUS: 42 → 46: J2
J2 then finishes completely.


Step 6 - Time 46
- J1 is still performing I/O until time 52.
No other job is ready, thus:
46 → 52: CPU IDLE


Step 7 - Time 52
- J1 finishes its I/O and returns to the ready queue.
It is the only remaing job: 52 -> 62: J1
J1 finishes.

Final Timeline
0    20   24   27        42   46      52        62
| J1 | J2 | J3 |    J1    | J2 | idle |    J1    |

---------------------------------------------------------------------------------------------------------------------------------

7. Main Strength of FCFS

Advantage: Simplicity
Easy to Understand, Implement, and Predict.

The Scheduler asks: "Who has been waiting the longest?"

---------------------------------------------------------------------------------------------------------------------------------

8. Main Weakness of FCFS

Problem: Long Jobs can Delay Short Jobs
EX:
J1 = 100 CPU cycles
J2 = 2 CPU cycles
J3 = 3 CPU cycles

If J1 is first: J1 -> J2 -> J3
Since J2 and J3 are shorter, they must still wait for J1's 100 cycles first.

FCFS priortizes arrival order, not job length.

---------------------------------------------------------------------------------------------------------------------------------

9. Why FCFS Works Well for Batch Systems

In a batch system:
• Jobs are submitted as a group.
• Users are not necessarily waiting for immediate results.
• The overall completion of the workload matters more than giving one particular short job an immediate response.

---------------------------------------------------------------------------------------------------------------------------------

10. Shortest Job First (SJF)
- Selects the ready job requiring the shortest CPU burst.

SJF asks: "Which ready job requires the least CPU time?"

---------------------------------------------------------------------------------------------------------------------------------

11. Two Version of SJF

V1 - Non-Preemptive SJF
- Once a job runs, it cannot be interrupted until its CPU burst finishes. Even if a short job arrives while running, it must wait.

V2 - Preemptive SJF
- Called "Shortest Remaining Time First (SRTF)", the scheduler can interrupt the currently running job if another job has a shorter
CPU burst than the current job's remaining CPU time.

---------------------------------------------------------------------------------------------------------------------------------

12. Non-Preemptive SJF
The decision happens when the CPU needs to select another job.

EX Scheduler looks at all currently ready jobs:
Ready:
J1 = 7 cycles
J2 = 4 cycles
J3 = 1 cycle
J4 = 4 cycles

SJF chooses: J3 = 1 cycle.
- Once J3 starts, another shorter job arriving later cannot interrupt it.

THUS, SJF can only choose among jobs that have already ARRIVUED.

---------------------------------------------------------------------------------------------------------------------------------

13. Non-Preemptive SJF Worked Example

| Job | Arrival | CPU Burst |
| --- | ------: | --------: |
| J1  |       0 |         7 |
| J2  |     0.5 |         4 |
| J3  |       4 |         1 |
| J4  |       5 |         4 |


Step 1 - Time 0
- Only J1 has arrived.
Thus: 0 -> 7: J1
- Even though J2, J3, and J4 arrive during this period, J1 cannot be preempted.


Step 2 - Time 7
- Now all three other jobs are available:
J2 = 4
J3 = 1
J4 = 4

Shortest is J3, thus: 7 -> 8: J3


Step 3 - Time 8
The remaining jobs are tied in CPU burst.
- Thus earlier arrival is used as the tie-breaker, so J2 runs first:
8 -> 12: J2


Step 4 - Time 12
Only J4 remains: 12 -> 16: J4


Final Timeline
0       7   8        12       16
|  J1   | J3 |   J2   |   J4   |

---------------------------------------------------------------------------------------------------------------------------------

14. Important Lession From Non-Preemptive SJF

J1 is the longest job, but it runs first, Why?
- Because at time 0: only J1 exits.

Thus SJF cannot choose a shorter job that hasn't arrived yet.
SJF chooses the shortest job among the jobs currently available.

---------------------------------------------------------------------------------------------------------------------------------

15. Preemptive SJF - SRTF
Shortest Remaining Time First (SRTF)
- Compares the remaining time of the currently running job against newly available jobs.

EX:
J1:
Original burst = 7
Already executed = 2
Remaining = 5

J2:
Burst = 4


SRTF compares:
J1 remaining = 5
J2 = 4

Since: 4 < 5

J1 is preempted, and J2 runs.

---------------------------------------------------------------------------------------------------------------------------------

16. Preemptive SJF / SRTF Worked Example

| Job | Arrival | CPU Burst |
| --- | ------: | --------: |
| J1  |       0 |         7 |
| J2  |       2 |         4 |
| J3  |       4 |         1 |
| J4  |       5 |         4 |

Step 1 - Time 0
- Only J1 exists: 0 -> 2: J1
J1 has now used 2 of its 7 cycles.
Remaing: J1 = 5.


Step 2 - Time 2
J2 arrives: J1 remaining = 5, J2 = 4
Since 4 < 5, J1 is preempted: 2 -> 4: J2
J2 has now used 2 of its 4 cycles.
Remaining: J2 = 2


Step 3 - Time 4
J3 arrives:
J1 = 5 remaining
J2 = 2 remaining
J3 = 1

Shortest = J3.
Thus: 4 -> 5: J3
J3 finishes.


Step 4 - Time 5
J4 arrives.
J1 = 5 remaining
J2 = 2 remaining
J4 = 4

Shortest = J2
Thus: 5 -> 7: J2
J2 finishes.


Step 5 - Time 7
Remaining:
J1 = 5
J4 = 4

J4 is shorter: 7 -> 11: J4
J4 finishes.


Step 6 - Time 11
Only J1 remains:
J1 = 5 remaining
Thus: 11 -> 16: J1
J1 finishes.


Complete Timeline
0    2    4   5    7       11       16
| J1 | J2 | J3 | J2 |  J4   |   J1   |

---------------------------------------------------------------------------------------------------------------------------------

17. Why SRTF Is Different From SJF

Non-Preemptive SJF
Looks at: CPU burst length of ready jobs.
Once one starts: It cannot be interrupted.


SRTF
Looks at: Remaining CPU time.
Once one starts: A running job can be interrupted.

---------------------------------------------------------------------------------------------------------------------------------

18. Strength of SJF / SRTF

Major advantage: Good Performance for Short jobs.

Short jobs are less likely to become stuck behind long jobs, thus can receive CPU time relatively quickly.
Resulting in SJF / SRTF attractive when minimizing waiting/response for shorter jobs is important.

---------------------------------------------------------------------------------------------------------------------------------

19. Weakness

#1 - Requires Knowledge of CPU Burst Length
- For SJF to make a decision the CPU burst length is needed.
Often difficult in real systems to idenfity the length of a program before it actually runs.

SJF needs advance knowledge or an estimate of CPU burst length.


#2 - Starvation
- When a job waits for a long time because other jobs continue to receive priority over it.
In SJF/SRTF: If a short job continue arriving, a long job can be postponed indefinitely.

---------------------------------------------------------------------------------------------------------------------------------

20. The Fundamental Scheduling Trade-Off
FCFS:
Prioritizes: Arrival order.
Simple and predicatble
BUT: Long job -> delays short jobs.

SJF / SRTF
Prioritizes: Short CPU requirements.
Short jobs finish quickly
BUT: risks the delay of long jobs with repeated short jobs arrival.

---------------------------------------------------------------------------------------------------------------------------------

21. Important Vocabulary

CPU Burst

A period when a job is actively using the CPU.

I/O Burst

A period when a job is waiting for I/O and does not need the CPU.

FCFS

First Come, First Served — execute jobs according to ready-queue arrival order.

SJF

Shortest Job First — select the ready job with the shortest CPU burst.

SRTF

Shortest Remaining Time First — preemptive version of SJF that chooses the job with the shortest remaining CPU time.

Preemptive

A running job can be interrupted and replaced by another job.

Non-Preemptive

A running job continues until it blocks, terminates, or voluntarily yields.

Starvation

A job waits indefinitely because other jobs repeatedly receive CPU time instead.
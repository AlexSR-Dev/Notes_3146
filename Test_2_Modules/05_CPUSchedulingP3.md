05 - CPU Scheduling

Part 3: Scheduling Policies for Interactive Systems, Multilevel Scheduling


1. Overview: Scheduling For Interactive Systems
- Interactive systems are systems where a user is actively waiting for the computer to respond.
EX:
- laptops, phones, desktop applications, interactive programs.

CONCERN: Responsiveness
If one job were allowed to use the CPU for a long time, other activities could appear frozen.

Thus, interactive systems need scheduling policies that allow multiple jobs to make progress regularly.
Major Apporaches:
1) Round Robin
2) Priority-Based Scheduling
3) Multilevel Scheduling

---------------------------------------------------------------------------------------------------------------------------------

2. Round Robin Scheduling
- Is a preemptive scheduling policy designed particularly for interactive systems.

IDEA: Give each ready job a small amount of CPU time, then move to the next job.
TWO fundamental components:
1) A ready queue.
2) A quantum, called a time slice.

---------------------------------------------------------------------------------------------------------------------------------

3. The Ready Queue in Round Robin
- All jobs that are ready to execute are placed in a queue.

EX:
Ready Queue:
J1 → J2 → J3 → J4

The job at the front receives the CPU, after its turn, the scheduler moves into the next job.
- Creating a round-robin behavior. Keeping active jobs in the ready queue and giving each one a small unit of CPU time.

---------------------------------------------------------------------------------------------------------------------------------

4. Quantum / Time Slice
- The fixed amounf of CPU time a job receives during one turn.

Typical quantum: 10-100 milliseconds.

EX: Quantim = 20 ms
- Then a job can run for at most 20 ms during that turn.
If it has work remaining:
1) It is preempted.
2) It goes to the back of the ready queue.
3) The next job gets the CPU.

---------------------------------------------------------------------------------------------------------------------------------

5. Round Robin Process

RR algorithm:
1. Take job from front.
       ↓
2. Run for one quantum.
       ↓
3. Did job finish?
       ↓
   YES → Remove job.
       ↓
   NO → Preempt job.
       ↓
4. Put job at back of queue.
       ↓
5. Run next job.
       ↓
6. Repeat.

- A job is not required to use its entire quantum and may leave the system if finished early.

---------------------------------------------------------------------------------------------------------------------------------

6. Why Round Robin Is Good for Interactive Systems

Imagine three programs running.
- If the browser allowed to run continuouslty for a long time, the other programs might appear frozen.

RR instead gives them repeated opportunities:
Browser → Music → Background
    ↓
Browser → Music → Background
    ↓
Browser → Music → Background
- Resulting in the system experience as responsive.

NOTE:
Round Robin prioritizes regular access to the CPU rather than allowing one job to run for a long time uninterrupted.

---------------------------------------------------------------------------------------------------------------------------------

7. Advantages of Round Robin

1. Simple to implement.
2. Fair to every active job to receive an opportunity to use the CPU, in terms of turns NOT time amount.

---------------------------------------------------------------------------------------------------------------------------------

8. Disadvantages of Round Robin

RR treats jobs as: Equally Important.
- Thus, does not inherently distinguish priorities.

This limiation motivates the need for priority-based scheduling.

---------------------------------------------------------------------------------------------------------------------------------

9. Choosing the Quantum Size

The most important design decision in RR>
TWO major problems:

Quantum Too Short:
- IF the quantum is extremely short.
The system must constanly switch between jobs. Producing "switching overhead / context-switch overhead." 
THUS, instead of productive work, the CPU spends more time switching between jobs.


Quantum Too Long:
- IF the quantum is extremely long, a job can keep the CPU for a long time.
THUS, other jobs wait longer and the system becomes "Less responsive".

---------------------------------------------------------------------------------------------------------------------------------

10. Quantum Trade-Off

| Quantum     | Result                                         |
| ----------- | ---------------------------------------------- |
| Too short   | Too many context switches / switching overhead |
| Too long    | Poor responsiveness                            |
| Appropriate | Balance between responsiveness and overhead    |

RR Requires careful quantum selection.

---------------------------------------------------------------------------------------------------------------------------------

11. Round Robin Worked Example

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


Complete Round Robin Timeline:
0–20     J1
20–37    J2
37–57    J3
57–77    J4
77–97    J1
97–117   J3
117–121  J4
121–134  J1
134–162  J3

Completion Order:
J2 → J4 → J1 → J3

NOTE:
Once only one job remains: It does not need to keep being preempted every quantum.



Round Robin Total CPU Time:
Job Reqire:
J1 = 53
J2 = 17
J3 = 68
J4 = 24

Total: 53 + 17 + 68 + 24 = 162 
Schedule finishes at: 162.
---------------------------------------------------------------------------------------------------------------------------------

12. Priority-Based Scheduling
- Each job receives a priority value, and the scheduler selects the highest-priority job.
- Priority represented by an integer usually.

Asks "Which job currenly has the highest priority."

---------------------------------------------------------------------------------------------------------------------------------

13. Priority Scheduling Can Be Preemptive or Non-Preemptive
Preemptive Priority Scheduling:
- If the high-priority job arrives while the lower-priority job is running: The current job can be interrupted immediately.

Non-Preemptive Priority Scheduling:
- It continues until it finishes, even if a higher-priority job arrives.

---------------------------------------------------------------------------------------------------------------------------------

20. How Priorities are Assigned

Two Board Categories:
1) Internal Factors - Based on the job/system behavior.
2) External Factors - Based on something outside the job.

---------------------------------------------------------------------------------------------------------------------------------

21. Internal Priority Factors
- On the job's behavior or history.
System can update these values as conditions change. Resulting in dynamic priorities.

EX:
A) Aging
The longer a job waits, is designed to prevent starvation( a job waits indefinitely because other jobs continually receive CPU time.)
- Allow low-priority job to gain enough priority to run.

B) Recent CPU Usage
The system tracks how much CPU time a job has recently consumed.
- A job with less CPU can become high priority. Thus prevents monopolization of the CPU.

---------------------------------------------------------------------------------------------------------------------------------

22. External Priority Factors
- Comes from outside the job itself.
Thus priority is determined by a external factor (people, conditions) on each jobs.
- Typically static and do not automatically change over time.


Issue with Static Priority
Suppose:
J1 = High priority
J2 = Low priority

If high priority jobs keep arriving: J2 may be pushed aside repeatedly.
Worst case; J2 may never run.
- Thus, low-priority jobs can suffer from starvation.

---------------------------------------------------------------------------------------------------------------------------------

23. Why Mutlilevel Scheduling Is Needed

Real computer often runs multiple types of jobs simultaneously.
EX: system processes, interactive foreground applications, and background batch jobs.
THUS, Using one scheduling policy for every job may not be ideal. Leading in multilevel scheduling.

---------------------------------------------------------------------------------------------------------------------------------


24. Multilevel Scheduling
- Divides the ready queue into multiple separate queues.
Each queue can have different types of job and can use its own "scheduling algorithm".

EX:
              Ready Jobs
                  │
       ┌──────────┼──────────┐
       ↓          ↓          ↓
   System      Foreground  Background
   Queue        Queue       Queue
                  │
                RR


Possible configuration is:
System queue
Foreground queue → Round Robin
Background queue → FCFS



Example Multilevel Structure
A system can have:
Queue 1 — System Processes
Contains critical operating-system processes.

Queue 2 — Foreground / Interactive
Contains programs the user is actively interacting with
Possible Policy: Round Robin
- Due to the importance of responsiveness.

Queue 3 — Background / Batch
Contains jobs such as: file indexing, backups, and other large background tasks
Possible policy: FCFS
- Due to immediate responsiveness being less important.

---------------------------------------------------------------------------------------------------------------------------------

25. The Problem: Which Queue Gets the CPU?
The are multiple Queues: System, Foreground, Background
- But only ONE CPU.

THUS, the system needs a policy for deciding which queue receives the CPU next.
Two Strategies:
1. Fixed Priority
2. Time slicing between queues.

---------------------------------------------------------------------------------------------------------------------------------

26. Fixed Prioiry Between Queues
With fixed priority: One queue always has priority over another.

For example:
Foreground
    ↓
Background

The system might always process foreground jobs first.
Background jobs receive CPU time only when: Foreground queue = empty



THE Problem With Fixed Priority:
- Fixed priority creates a starvation risk.

Imagine the foreground queue never becomes empty:
Foreground:
J1 → J2 → J3 → J4 → J5 → ...

Then:
Background:
J6 → J7 → J8 → ...
- may never receive CPU time.

Therefore: Background jobs could be delayed indefinitely.
This is the same general starvation problem encountered with priority-based scheduling.

---------------------------------------------------------------------------------------------------------------------------------

27. Time Slicing Between Queues
- Instead of giving one queue absolute priority, the system assigns each queue a percentage of CPU time.

EX:
80% → Foreground
20% → Background

Within those queues, different scheduling algorithms can still be used.
For example:
Foreground → RR
Background → FCFS



Why Time Slicing Helps:
Suppose:
Foreground = 80%
Background = 20%

Even if foreground jobs are constantly available: Background jobs are still guaranteed some CPU time.
Therefore:
Foreground → gets more CPU
Background → still gets CPU
- This avoids total starvation of the background queue.

---------------------------------------------------------------------------------------------------------------------------------

28. Most Important Things to Memorize

Round Robin = interactive systems.
RR is preemptive.
RR uses a quantum/time slice.
Quantum too short → switching overhead.
Quantum too long → poor responsiveness.
Priority scheduling = highest-priority job runs first.
Priority scheduling can be preemptive or non-preemptive.
External priority is typically static.
Internal priority is typically dynamic.
Aging increases priority as a job waits and helps prevent starvation.
Recent CPU usage can be used as an internal priority factor.
Multilevel scheduling divides jobs into separate queues.
Each queue can use its own scheduling algorithm.
Fixed queue priority can cause starvation.
Time slicing between queues guarantees each queue some CPU time.
RR emphasizes fairness/responsiveness.
Priority scheduling emphasizes importance.
Multilevel scheduling handles mixed workloads by allowing different policies for different job types.

---------------------------------------------------------------------------------------------------------------------------------

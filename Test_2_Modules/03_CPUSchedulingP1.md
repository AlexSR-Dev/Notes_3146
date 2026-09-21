03 - CPU Scheduling

- Part 1: Foundations of Scheduling:


1. What is CPU Scheduling?
- In a multiprogrammed system, multiple processes can be in the ready state at the same time.

A ready process:
• Is loaded in memory.
• Is capable of executing.
• Is waiting for an opportunity to use the CPU.

- Since there various amounts of ready processes competing for a limited number of CPUs, the OS needs to deteremine,
Which process should run next?
- The decision-making process is called CPU scheduling.


Scheduler - The OS component responsible for selecting which process runs next.
Scheduling Algorithm - The rule or logic the scheduler uses to make that decision.

THUS:
Scheduler = Performs the decision.
Scheduler Algorithm = Determines how the decision is made.

----------------------------------------------------------------------------------------------------------------------------

2. Scheduling Policy vs. Scheduling Mechanism

Scheduling Policy:
The rules/strategy used to decide: When should a process run, and which process should run?

Scheduling Mechanism:
The low-level implementation that carries out the switch from one process to another.

EX:
Policy     = "What should happen?"
Mechanism  = "How is it actually carried out?"

NOTE:
The CPU scheduling focuses on scheduling policy, rather than the mechanism.

----------------------------------------------------------------------------------------------------------------------------

3. Is There a Single Best Scheduling Policy?
- Nope
Its entirely dependent on the type of system being used.

There are three major environments each with different priorities and different scheduling goals:
1. Batch Systems
2. Interative Systems
3. Real-Time Systems

----------------------------------------------------------------------------------------------------------------------------

4. Batch Systems:
- Receives a set of jobs that are processed without a user actively waiting for an individual result.
EX:
Submit many jobs
      ↓
System processes jobs
      ↓
Results become available

- The scheduler focuses on overall system performance, rather than waiting for a person.

CONCERN: Process the overall batch efficiently.
- Thus, less importance on which individual job finishes first and more on the system processes the workload efficiently.
Batch → overall system performance

----------------------------------------------------------------------------------------------------------------------------

5. Interactive Systems:
- A user is directly interacting with the computer and waiting for results.
Includes:
• Issuing commands.
• Running programs.
• Typing into applications.

ISSUE: How fast does the system feel to the user?
- Technically productive system can feel slow if the user has to wait too long for responses.
Interactive → user-perceived performance

----------------------------------------------------------------------------------------------------------------------------

6. Real-Time Systems:
- Jobs have specific deadlines by which they need to finish.

CONCERN: Predictability
- The system needs to behave consistently enough that deadlines can be guaranteed or nearly guaranteed.
Thus, unexpected delays become more serious than in batch/interative systems.
Real-time → predictability + deadlines

----------------------------------------------------------------------------------------------------------------------------

7. Comparing the Three System Types

Memory Association:
Batch       → Efficiency
Interactive → Responsiveness
Real-time   → Predictability

----------------------------------------------------------------------------------------------------------------------------

8. General Objectives of Scheduling Algorithms
1. Fairness
2. Efficiency and balance
3. Policy enforcement

----------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------

8.1 Fariness
- A scheduling algorithm should give processes a fair opportunity to use the CPU.

GOAL: To prevent a process from being starved indefinitely while other processes consume CPU time.
NOTE: Every job should receive its fair share of CPU time.

----------------------------------------------------------------------------------------------------------------------------

8.2 Efficiency and Balance
- The scheduler should try to minimize CPU idle time.

THUS: Keep the CPU as busy as possible when there is ready work available.
- Efficiently using the available computing resources.

----------------------------------------------------------------------------------------------------------------------------

8.3 Policy Enforcement
- Once the scheduling policy has been selected, the system needs to enforce it correctly and consistenly.
A policy can't be useful if the system doesn't reliably follow it.

----------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------

9. Batch-System Scheduling Objectives
There are three major objectives that must be accomplished:

1) Maximize throughput
Throughput = Number of jobs completed during a given amount of time.

EX:
100 jobs/hour, has a greater throughput than: 50 jobs/hour.
GOAL: Maximize the number of completed jobs in a given interval.


2) Minimize turnaround time
Turnaround time = Is the total time between:
Job submitted
      ↓
Job completed

- The scheduler wants this interval to be the shortest as possible.


3. Maximize CPU Utilization
GOAL: Keep the CPU utilized rather than sitting idle while work is available.

----------------------------------------------------------------------------------------------------------------------------

10. Interactive-System Scheduling Objectives
There are two major objectives:

1) Minimize Response Time
Response time = Is the interval between when an interactive job/command is issued and when it is completed.
EX:
User issues command
        ↓
    response
        ↓
Command completes

- The shorter this interval, the faster the system feels to the user.


2) Maintain Proportionality
Proportionality = Matching user expections to the size of the job.

General Understanding: Large/complex job → may take longer
Expectation: Small/simple job → should finish quickly

THUS: The scheduler should avoid making a short job wait an excessively long time behind a large job.

----------------------------------------------------------------------------------------------------------------------------

11. Real-Time Scheduling Objectives
There are three MAJOR concerns:
1) Predictability
2) Meeting deadlines
3) Considering priorities


1) Predictability
- The systen should behave consistently enough that its timing can be relied upon.
IMPORTANT, as jobs have deadlines.


2) Meeting Deadlines
- The scheduler needs to make decisions that helps jobs complete before their deadlines.

TWO Categories:
A) Hard real-time
Missing a deadline can have catastrophic consequences.
EX:
- Industrial machinery with late response can potentially cause serious harm.

B) Soft real-time
Missing a deadline is undesirable but not necessarily catastrophic.
EX:
- Reduced quality of service or data loss.


3) Considering Priorities
- Not every real-time job necessarily has the same importance.
A scheduler may need to consider:
• Deadlines
• Urgency
• Other assigned measures of importance

----------------------------------------------------------------------------------------------------------------------------

12. When Does a Scheduling Decision Occur?
- They are triggered by particular events.

Four major situations:
1) A new process is created.
2) A process terminates.
3) A process becomes blocked.
4) An interrupt occurs.

----------------------------------------------------------------------------------------------------------------------------

13. Process Creation
- When a new process enters the ready state, the scheduler may need to decide whether:
New process OR Another ready process, should run next.

----------------------------------------------------------------------------------------------------------------------------

14. Process Termination
When a running process finishes:
Running process
      ↓
   terminates
      ↓
CPU becomes available
      ↓
scheduler chooses next process

- Thus the scheduler needs to select another ready process.

----------------------------------------------------------------------------------------------------------------------------

15. Process Blocking
- A running process can become blocked when it needs to wait for something.

EX:
- Waiting for I/O
- Waiting to acquire a lock on a shared resource.

Blocked process cannot continue making process, thus the scheduler can use the CPU for another ready process.

----------------------------------------------------------------------------------------------------------------------------

16. Interrupts
- Can alsot trigger a scheduling decision.

EX:
- I/O completion interrupt.
- An I/O operation finishes.

A process that had been blocked waiting for that I/O may becomes ready again.

Timer expiration: A hardware timer indicates that a time slice has ended. And that another process should be scheduled.

----------------------------------------------------------------------------------------------------------------------------

17. Preemptive Scheduling
- The currently running process can be interrupted before it naturally finishes.
The OS can take the CPU away from the current process can schedule another ready process.

Preemptive = OS forcibly takes the CPU.
Process A running
       ↓
OS interrupts A
       ↓
Process B runs

----------------------------------------------------------------------------------------------------------------------------

18. Non-Preemptive Scheduling
- Once a process is selected, it continues running until it:
1) Completes
2) Blocks itself
3. Voluntarily yields the CPU

The scheduler cannot forcibly interrupt it.
Non-preemptive = Process must give up the CPU voluntarily or naturally stop running.

----------------------------------------------------------------------------------------------------------------------------

19. Preemptive vs. Non-Preemptive

Feature	                                    Preemptive	                        Non-preemptive
Can OS interrupt running process?	            Yes	                              No
Process can be removed before completion?	      Yes	                              Not forcibly
Process may stop because it blocks?	            Yes	                              Yes
Process may voluntarily yield?	            Yes	                              Yes
Responsiveness	                              Generally gives more flexibility	Can be reduced by long-running jobs
Complexity	                                    More flexible	                  Simpler

Preemptive scheduling provides the OS with greater flexibilityy and responsiveness, while non-preemptive scheduling is simpler
but can leave the system less responsive when a long job is running.

----------------------------------------------------------------------------------------------------------------------------

20. Compute-Bound vs. I/O-Bound Jobs
- Processes don't all use the CPU in the same manner.

They are characterized by how they alternate between:
CPU execution
      ↕
I/O waiting

Two Important Classifications:
1) Compute-bound
2) I/O-bound

----------------------------------------------------------------------------------------------------------------------------

21. Compute-Bound Jobs
- Spends relatively long periods performing CPU work before wainting for I/O.

Characteristics:
• Long CPU bursts
• Infrequent I/O waits
• CPU is used heavily
• Relatively few interruptions from I/O

EX: A scientific simulation performing heavy numerical calculations and only occasionally reading/writing data.
Compute-bound = long CPU bursts.

----------------------------------------------------------------------------------------------------------------------------

22. I/O-Bound Jobs

- Has short periods of CPU activity separated by frequent I/O waits.

Characteristics:
• Short CPU bursts
• Frequent I/O waits
• Spends relatively more time waiting for I/O
• Uses CPU in short bursts

EX: A text editor that waits for user keystrokes and briefly processes each input.
I/O-bound = Short CPU bursts + frequent I/O waits.

----------------------------------------------------------------------------------------------------------------------------

23. Why Compute-Bound vs I/O-Bound Matters:
- Scheduling algorithms may need to treat theses types of jobs differently.

I/O Bound
- Since CPU bursts are short, giving them quick CPU access can help them finish their CPU work and return to I/O waiting.
Thus, improving system responsiveness.

Compute-Bound
- Since, it uses the CPU for long periods, the scheduler may need to prevent them from monopolizing the CPU for too long.

----------------------------------------------------------------------------------------------------------------------------

24. Key Vocabulary
| Term                     | Definition                                                               |
| ------------------------ | ------------------------------------------------------------------------ |
| CPU scheduling           | OS process of deciding which ready process runs                          |
| Scheduler                | OS component that makes scheduling decisions                             |
| Scheduling algorithm     | Rule used by the scheduler                                               |
| Scheduling policy        | Strategy/rules determining scheduling decisions                          |
| Batch system             | Processes jobs as a group, emphasizing overall performance               |
| Interactive system       | User directly waits for results                                          |
| Real-time system         | Jobs have deadlines                                                      |
| Fairness                 | Giving processes a fair share of CPU time                                |
| Throughput               | Number of jobs completed in a given time                                 |
| Turnaround time          | Submission → completion                                                  |
| Response time            | Interactive request → completion                                         |
| Predictability           | Consistency/reliability of execution timing                              |
| Preemptive               | OS can interrupt a running process                                       |
| Non-preemptive           | Running process keeps CPU until completion, blocking, or voluntary yield |
| Compute-bound            | Long CPU bursts, infrequent I/O                                          |
| I/O-bound                | Short CPU bursts, frequent I/O                                           |

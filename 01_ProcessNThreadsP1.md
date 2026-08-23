01 - Process And Threads Part 1:

Understanding Processes:

Process - Fundamental abstraction OS uses to represent and manage running programs.
Process Control Block (PCB): How the OS keeps track of every process using a data structure.


1. Why Do We Need the Concept of "Process"?
A computer system's main objective is to execute programs.
- Runs several different programs at the same time. (Text editor, browser, etc.)
- Run mutliple instances of the same program. (Text editor twice).

Thus, what/how does the OS's internal represent each running program individually, distinguish between them,
and manage them? The internal representation is called PROCESS.


Visual Practice:
On a computer, you can open a system utility (task manager for Windows).
- Every entry in the list represents one running instance of a program, thus every entry is a PROCESS.
- Moreover, if two separate windows of the same text editor app were opened, the OS would display these as
TWO DISTINCT PROCESSES. Suppoting the individuality for separate and organized apps.




2. What Exactly Is a Process?
Process - An abstraction that the OS uses to represent a single running program instance.

Process vs. Program: A Critical Distinction:
- A process is the equivalent to a program.
• Program: A static set of instructions, code stored on disk that describes what should be done.
• Process: A dynamic, running instance of that program, the actual activity of the program being executed.

EX: TextEdit is an application file which is a program itself, but if multiple instances were running, each
would accounted as multiple processes. While remaining as one program.



The Recipe and Cook Analogy:
• Recipe: Is like a program. Fixed instructions that must be followed. Doesn't do anything by itself.
• Person Following the Recipe: Like the processor (CPU), carrying out the instructions.
• Activity of the Person baking the Cookies: Following and producing the cookies, is the process. The recipe in action.
Book -> Person -> Action.
Program -> Processor -> Process.

Process, requires more than just the program's code, it also includes:
- Data program utilizes.
- State Information describing the state of execution its at.
- Resources it has whiling running, memory and open files.

In order to execute a program, a corresponding PROCESS must be CREATED.
- As it bundles the program's instructions,
- The data being processed,
- Tracking info about its current status,
- The system resources it needs to actually run.




3. When are Processes Created?
- As a corresponding process is needed, the OS needs to strategize on WHEN to create these processes. Two general apporaches:

1. Create All Possible Processes At System Startup: The system would create every process ever needed at startup and none
afterward. Works reasonably for an EMBEDDED SYSTEM - a device built for one specific limited purpose, but not pratical
for general purpose computers with multiple and complex programs running at once.

2. Create Processes As Needed: Most general-purpose computers uses a MIX of essentinal processes, some at system start up
for basic functioning of the OS, while others are on demand by the user via opening apps, or the system needs to start a new task.





4. The Life Cycle of A Process:
- Once created, it goes through a series of well-defined STATES during its lifetime.
The states, the transitions between them, are required to understand how OS manage running programs.

Step 1: New -> Ready:
- Once a process is created and admitted into the system, it enters a READY state.
Ready process is not running yet, but fully prepared to run as soon as the processor becomes available.


Step 2: Ready -> Running:
- Once the processor is free, th OS can select a ready process and starts executing it.
Once decided, the process moves from READY to RUNNING STATE, where the process's instrunctions are actively being carried out by the CPU.


Step 3: Running -> Waiting:
- In the event a process is running, it may need to pause and wait such as user input or loading files.
The process is BLOCKED and moves from RUNNING into WAITING STATE, until the event it's waiting happens.


Step 4: Waiting -> Ready (The Cycle Repeats):
- Once the event a process was waiting occurs, it moves from WAITING to READY.
However, it may be scheduled to run and wait again later, thus this cycle can repeat many times for a single process.


Step 5: Reaching the Terminated State:
- When a process finishes executing all of the instructions of the program, it may be TERMINATED, ending the life cycle.
Termination can occur at different reasons:
• User-Initiated Termination: The user clicks the "close" or exit button on an app, eliminating the process.
• Error-Based Termination: The process becomes forced to stop because of a runtime error (a issue odered while executing the program.)

NOTE:
A process does not have to go through any state to be terminated, thus it can move to the TERMINATED state directly
from any of the three active states: READY, RUNNING, or WAITING, depending on the situation.
EX: User exists while waiting for input.


Full Life Cycle Summary:
- New: The process is created.
- Ready: The process is ADMITTED and waiting for the CPU.
- Running: The process's instructions are actively being executed by the CPU.
- Waiting: The process is paused, waiting for an event to happen.
- Terminated: The process is finished executing or has been stopped/eliminated.

New -> Ready then possibly on loop: Ready <-> Running <-> Waiting, finally at Terminated.
- Preemption should be advised as it can move a process from RUNNING directly to READY state.





5. Multiple Processes and Multiprogramming:
- A computer system can have MULTIPLE PROCESSES ACTIvE SIMULATANEOUSLY, many at READY state, awaiting their turn to the CPU.

Important Limitation: A single processor system (1 CPU), only one process can be in the running state at any moment,
the CPU only executes one process's instructions at a time.

Resulting in the OS constantly switching BETWEEN THE DIFFERENT ACTIVE PROCESSES, providing each a turn for a period of time.
This capability of the OS managing and switching between multiple processes to make progress is called MULTIPROGRAMMING.





6. Scheduling and Preemption:
- Supporting multiprogramming introduces two additional concepts:

Scheduling Policy:
- As the OS constantly decides which ready process gets to run next and when, a set of rules to make decisions fairly and
efficiently is needed. Thus, a scheduling policy is used, various use different criteria for deciding on which process to run next.

Preemption:
- Depending on the scheduling policy used, preemption may be considered.
- Occurs when a process that's currently running is interrupted, allowing a different process a turn to run.
Once a running process is reempted, it doesn't go to the WAITING STATE (as it isn't waiting for an event), instead it moves 
back to the READY STATE, as it fully capable of running and is just waiting for its next turn on the CPU.





7. Tracking Process: The Process Control Block (PCB):
- With the existence of many processes in a system at once, and the OS constantly switching between them, a reliable
manner to track the detailed info of each individual process is needed.

Process Control Block (PCB): A data strcuture used by the OS.

A PCB is created for every process currently in the system, and stores all the essential info the OS needs to manage that
process. The key pieces of infor stored in a PCB:

- Process ID: Every process is assigned a unique identifying number when created. To distinguish processes a part even with the same program for the OS.

- Process State: Tracks which state the process is currently in. (New, Waiting, etc.)

- Program Counter (PC): Every process is associated with a program, once the process is executing some specific
instruction within that program. The program counter keeps track of the address of the next instruction to be executed
for the process, allowing the OS to know exactly where the process left off.

- Registers: Also with the program counter, a process can have other CPU-related registers in association to it, such as
stack pointer and general-purpose registers, that store working values needed while the process runs.

- Scheduling Information: Info related to how/when the process should be scheduled to run. Exact details are stored here,
unless the scheduling mechanisms and policy the system uses says otherwise.

- Memory Management Information: A process owns memory as its resources, the OS stores info related to managing that
process's memory.

- Accounting Information: Other bookkepping details about the process, time limits or usage statistics.

- Other Information: Dependein on the specific OS, additional details may also be stored as needed.


PCB Table: Can be visualized as a single structured record. Each process running in the system has its own PCB filled
with related specific process's current info.

--------------------
|    Process ID    |
--------------------
|   Process State  |
--------------------
|  Program Counter |
--------------------
|     Registers    |
|                  |
|                  |
--------------------
|   Memory limits  |
--------------------
|       ...        |
--------------------
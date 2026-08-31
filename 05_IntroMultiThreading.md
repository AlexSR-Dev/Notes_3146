05 - Introduction to Multi-Threading:


1. The Two Characteristics of a Process:

1) Resource Ownership - A process owns resources such as its memory image, which is subject to it the program code, input/output data, and other state info.

2) Execution of a sequence of instructions - A process also represents the actual ruunning of a sequence of program instructions. Line by line.

- Two characterisitcs that are conceptually different, and separated to become more useful.
Notably, the second characteristic regarding execution of sequence of instructions, is modeled using a separate abstraction called a 'THREAD'.


process - Provides the environment and resources needed for something to execute.
- passive characteristics of owning and holding resources.

thead - Carries out the actual sequence of instructions using the resources the process provides.
- active characteristics of running the instructions.



The House and Occupant Analogy:
- Think of this relationship from a standpoint of a 'process as a house', containing resources: rooms, appliances, etc.
Meanwhile, a 'thread is the occupant of the house', that does: live and uses the house's resources to do chores.

- A thread is the basic unit of execution and scheduling in an OS.
Thus, when a CPU scheduler decides what to run next, it chooses which THREAD to run, not just which process.




---------------------------------------------------------------------------------------------------------------------------------------------

2. Why Do We Need Threads at All?
- A process is not limited to a single thread of execution.
As a program can contain several different sequences of steps that may be independent of each other can may run at the same time concurrently.



The Roasted Vegetable Pasta Example:
- If the recipe is one strict single sequence of steps:
1) Cut all the vegetables
2) Heat the oil
3) Fry the onion
4) Add the garlic
5) Preheat the oven
6) Once heated, put the zucchini and asparagus in to roast
7) Wait about 20 minutes for roasting to finish
8) Boil a pot of water
9) Cook the pasta

- The sequence would be enough to reach the conclusion, but is NOT EFFICIENT. As your forced to do everything at a time, without reason why you should wait.
- In reality, most of these steps are independent of each other and can happen at the same time.

● While you are partway through cutting vegetables, you could already start heating the oil
— cutting and heating don't depend on each other.
● You could start preheating the oven at the same time as prepping the vegetables.
● Boiling water and cooking pasta is a completely separate task from roasting vegetables,
so it could happen concurrently with the roasting.

- Now this order contains multiple smaller sequences of steps, each utilizing resources with some dependencies, but are independent enough to be carried out partly at the same time.



Applying This to Programs:
- Each independent sequences can be modeled as a separate thread within the same process.
Thus, 'threads allow concurrenty within a single process'. Interleaving mutiple operations at once.

- Therefore, in the same house-occupant analogy, there can be mulitple occupants each with their own activities, while still sharing the house's resources.

- Also, there is no reason to split theses different instruction sequences into completely separate programs and run them as separate processes.
As threads within the same process are fundamentally different from separate processes:

● Threads of the same process are closely related to each other.
● Threads share the resources owned by their parent process.
● Threads work cooperatively towards the shared goal of a single program.




---------------------------------------------------------------------------------------------------------------------------------------------

3. Multi-Threading Defined:
- A single process containing multiple threads of execution.
● A process with more than one thread is called a multi-threaded process.
● A process with only one thread is called a single-threaded process.


Real-World Examples of Multi-Threading:
1) A Word Processor:
● A thread continouosly handles ineration with the user.
● A separate thread reformats the document in the background. Allowing the user to continue typing.
● Another thread to automatically save the document periodically, without interrupting the user.


2) A Web Server:
● A dispatcher thread - Listen for new incoming requests.
● Worker threads - When the dispatcher receives the new request, its sent to this thread, that processes and reponds to it.

- This practice is usually to maintain and distribute incoming requests concurrently from users.



---------------------------------------------------------------------------------------------------------------------------------------------

4. Thread States:
- Threads also passes through different states in its lifetime like processes.

● New - The thread is created.
- A new thread transitions into the READY state once its prepared to run.

● Ready - The threads waits for the CPU to be assigned to it.
- When the scheduler picks it, it moves to RUNNING (scheduled).

● Running - Thread is actively executing in the CPU.
- From Running, a thread can:
    - Be preempted and sent back to READY.
    - Move to WAITING if a pause or an event is requried.
    - Completes its work and move to TERMINATED.
    - Killed and moves to TERMINATED.

● Wating - Thread is pauses, until a specific event occurs.
- Once the event occurs, the thread moves back to READY, to be scheduled to run again.
- Can also be killed, and moved directly to Terminated.

● Terminated - The thread has finished exxecuting/killed and no longer runs.


NOTE: This represents a thread, no a process as a whole.

---------------------------------------------------------------------------------------------------------------------------------------------


5. What Infromation belongs to Each Thread?
- As threads in the same process share most of its resources, each thread needs 'some information that is exclusive to it', data to keep track of the thread's process.
This includes:
● Program counter - Track which instructions should be executed next for the particular thread.
● Stack and Stack pointer - Stores local function data, specific to that thread, and a stack pointer to keep track of current position in that stack.
● Registers - Holds temporary data specific to that thread's exeuction.


Visualizing a Multi-Threaded Process:

------------
| Code     | - Program instructions.
------------
| Globals  | - Global variables avaliable to any thread.
------------
| Heap     | - Dynamically allocated memory, shared.
------------
    ↓
    ↑
------------
| Stack    |
------------
    ↑
------------
| Stack    |
------------
    ↑
------------
| Stack    |
------------

- Each individual thread has its 'own separate stack'. In this example it would be three threads.
- Most of the process's memory (code, globals, heap) is shared across all its thread.
- BUT, each thread keeps its own private stack for its local function calls and data.



Thread Control Blocks (TCBs):
- Used to store information about each individual thread.
Contains:
● Thread State - Current state of the tread.
● Thread ID - Unique identifier to the thread.
● Program Counter - Next instructions to execute for this thread.
● Stack pointer - Points to the current position in this thread's stack.
● Registers - The thread's saved register values.
● Scheduling information - Data the scheduler uses to decide whtn to run this thread.

The OS uses this like the PCB (Process Control Block) to keep track of everything needed to run in order.


---------------------------------------------------------------------------------------------------------------------------------------------

6. Important Rules and Concepts to Remember:

1) A process is an instance of a single program, owned by a single user.
- Threads of the same process are expected to cooperate with each other rather than compete one another.

2) All thread-specific data (TCBs and stacks) is part of the process's overall memory image.
- The programmer's responsibility to carefully design and implement the program to prevent corruption or unauthorized access.

3) Every process starts with at least one thread of execution.
- A process never starts with zero threads executions.

---------------------------------------------------------------------------------------------------------------------------------------------

7. Advantages of Multi-Threading:

1) More Efficient CPU Utilization Within a Process
- Multi-threading allows a process to exploit concurrency internally, resulting in the CPU being kept busy and used efficiently.

2) Cheap Thread Creation and Termination
- Creating and destorying a process is already expensive with the full set of resources involved.
However, a thread, uses the resources from the process and only carries small amounts of info exclusive to its own. Resulting ina cheaper and faster creation/termination for a thread.

3) Minimal Context-Switching Cost Between Thread
- As threads of the same process have far less exlcusive information, only that is needed to be saved and swapped when switching threads in the same process (cheaper).
Everything else belongs to the process, thus stays the same. A switch of processes is still expensive.

4) Cheap and Easy Communcation Between Threads
- Communcation among threads of the same process is cheap due to the same memory. As a thread can write into the same memory and another can read it directly.
In comparison to processes, requires explicit sharing mechanisms, as the OS deliberately provides protection between different processes.
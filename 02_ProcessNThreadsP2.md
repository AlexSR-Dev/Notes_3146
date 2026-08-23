02 - Processes and Threads Part 2:

Process, Creation, Termination, and Switching:


1. Process Creation
- Traditionally, process creation was handle entirely by the OS, with no explicit tirgger from the user.
- Modern OS, provides a SYSTEM CALL allowing a running process to explicitly request the creation of a new process.

OS starting point, for Unix-based systems, the first process created at boot time is called INIT.
INIT - Creates the very first process.
However, then after as other processes exit, it cascades allowing any process to spawn children and from there their own children.


Steps Involved in Creating A New Process:
- When creating a new process, the OS performs many steps:

1. Assign a Unique Identifier to the process.
This Process ID, distinguish itself to the OS and other processes.


2. Allocate and set up memory for the process.
Called the process's MEMORY IMAGE, is composed of two main parts:
• Space for the Process Control Block (PCB):
Holds the bookkeeping information about the process, all PCBs kept together in a common location by the OS.

• Space for the program and its data, thats further explained into distinct regions:
- Code (Text) Space: Stores the actual program instructions.
- Global Data Space: Stores global variables, which are accessed from anywhere in the program.
- Heap Space: Stores data that is dynamically allocated while the program runs. (Memory requested at runtime rather than declared in advanced).
Without specification to the OS, the heap is allowed to grow as needed, up to some upper limit.
- Stack Space: Stores local data belonging to individual functions. For every function called, space is reserved on the Stack
for its local variables and when nested/recursive, the stack grows as well.


3. Understanding the Memory Layout Diagram:
The Process's Memory Image can be visualized as a vertical block divided into four regions:
------------------
|      Code      |
------------------
|    Globals     |
------------------
|      Heap      |
------------------
        ↓
        ↑
------------------
|      Stack     |
------------------

- The Heap and Stack grows towards each other.
• The Heap grows downward toward the stack as more dynamic memory is allocated.
- Like how an arraylist must resize and more dynamic memory is allocated.
• The Stack grows upward toward the heap as more function calls are made.
- Like how a function with recursion increases the stack call and its depth, thus allocating memory.

The combined space available to the heap and stack is fixed.
- Thus, romm availbles becomes limited, if one or the other grows a lot.
- In which excessive recusion (grows stack) or allocation of dynamic memory (grows heap) can run out of space and crash eventually.


4. Set Up the Memory Management Structures for the process.
- Additional data structures the OS uses to manage how the process's memory maps to physical memory.


5. Create any other Bookkeeping Structures the OS many want, like performance monitoring.



Two Mechanisms For Creating a Process:
Option 1: Cloning A process creation mechanism where an existing process spawns a new process that an exact copy of itself.
Utilized by Unix-based OS, implemented through the fork() system call.

Option 2: Creation from Scratch, A process creation mechanism that invokes a system call,
by passing an appropriate parameters, to build a brand new process.
Utilized by Windows, implemented through the CreateProcess() system call.









2. Unix Process Creation: fork()
- Used by Unix OS to create a new process. Terminology:
• The original process that calls fork() is the PARENT process.
• The newly created process is the CHILD process.

When fork() succeeeds, everything from the parent is copied into the new child process's memory:
- The Memory Image,
- Environment Settings,
- The I/O handles (open files).

HOWEVER, the child gains its own unique properties:
- Unique process ID,
- Own scheduling information,
Treated by the OS as a separate independent process.



Doesn't Cloning Mean Every Process Runs Identical Code?
No, the reason is how fork() reports back to the caller.

The Return Value of fork()
- It doesn't just create a process, but also RETURNS A VALUE, of which varies depending on the process asked.
• If the process creation SUCCEEDS, the PARENT process receives the child's process ID (positive number) as the return value.
• If the process creation SUCCEEDS, the CHILD process receives a return value of 0.
• If the process creation fails, fork() returns -1 to the parent, and no child process is created successfully.

This allows the conditional execution of the fork() return value.
EX:
pid_tod = fork();
if (id == -1) {
  std::cout << "Error Creating process\n";
} else if (id == 0) {
  std::cout << "I'm a child process!\n";
} else {
  std::cout << "I just became a parent!\n";
}

Walkthrough:
- fork() is called once, and returns twice - once in the context of the parent, and another in the context of child if succededed.
- If id == - 1, process creation fails; only the parent exists, and prints an error message.
- If id == 0, this branch is running in the CHILD process, so it prints "I'm a child process".
- Otherwise, with any positive number, this branch is running in the PARENT process, and print the relative statement.

- Cloning does not force every process to run identical code constantly.
The return value of fork() provides a manner for the parent and child to diverge right after the fork.






3. Changing the Program a Child Executes: exec
- Branching inside a single shared program is fine for simple cases.
But isn't practical for every process in a system to receive a truly different behavior.

- Instead of running a branch of its parent's program, a child process can completely REPLACE its own memory image
with a brand new program.

By using the EXEC family of system calls, when a child process calls one of these, it stops executing the parent's
program entirely and starts executing a different program from scratch, the child's identify stays the same,
but the code is running something else altogether.


EX: fork() combined with execvp()
pid_t id = fork();
if (id == -1) {
  std::cout << "Error creating process\n";
} else if (id == 0) {
  // child process functionality
  char* args[] = {"echo", "hello", NULL};
  execvp(args[0], args);
} else {
  std::cout << "I just became a parent!\n";
}

Walkthrough:
- fork() splits execution in a parent branch and a child branch.
- In the child branch (id == 0), instead of printing, the child calls execvp(args[0], args).
- execvp takes two parameters:
  1. The name of the program (executable) to run, in the EX its "echo".
  2. An array of command line parameters to pass to the new program.

- Two crucial rules about the arguments array; it must include the program's name as its FIRST element,
and must end with NULL as its last element, for the system to know where the arg list ends.

- The array {"echo", "hello", NULL} tells the system: Run the program ECHO, and pass its arg "hello".
It purpose is to display whatever is passed to it on the console.

NOTE:
This combination of fork() creating a new process, followed by exec to load a different program into it is a standard
mechanism used throughout the Unix-like systems.





4. Reducing the Cost of Cloning: Copy-on-Write
Because fork() clones a process, it requires the copy of the parent's entire memory image (code data, heap, stack, etc.)
into the new child process. Resulting in an expensive operation.
The OVERHEAD becomes significant, for processes with large memory footprints.

OS's reduce this overhead with a technique called COPY-ON-WRITE (COW):
• Instead the of immediate duplication of data when fork() is called, the parent and child 'start out sharing' the same physical memory.

• Therefore, only a copy of a piece of memory is only made 'at the momement either process tries to modify it'
('write' to it), resulting in 'copy on write'.
- Ohh, so the copies are only specific and limited to when either process attempt to modify an exisiting memory.

• Signifying that if a child process's next action is to call exec, which replaces its entire memory image
with a new program, a wasteful full copy of the parent's memory may 'never need to be made at all', as the child
removes the memory image provided, almost immediately.


NOTE:
Optimization is like lazy resource management: don't do the expensive work (copy memory) until you know it's needed.





5. Process Termination:
Terminatation can result for different reasons:
- Voluntary exit upon task completetion.
- Voluntary exit due to a fatal error.
- Involuntary exit due to an error or bug.
- Involuntary exit due to a KILL command issused by the OS or user.


Process Hierarchy, Oprhans, and Zombies:
On Unix-based systems, the OS keeps track of a PROCESS HIERARCHY.
- Which process created which other. 
Process Group - A process that keeps toegther its children and all its descendants.

Important rule to this hierarchy: A parent process must be allowed to read its child EXIT STATUS.
- Info about how/why the child process eneded. Creating two special situations depending on the order which the parent and child terminate.


• If the parent terminates before the child:
The Child becomes and orphan processs. As its original parent no longer exists to read its exit stats. The orphan
is automatically ADOPTED by the init process, becoming the new parent for any orphaned processes in the system.

• If the child terminates before the parent:
The system cannot discard the child process, as the parent needs to be able to read its exit status later on.
So the child's PCB is kept, and the child enters a state called a ZOMBIE PROCESS.
A zombie process is 'dead', as it finished executing, but its exit status hasn't been collected by its parent.


To clean up a zombie, the parent process must explicitly WAIT for the child to terminate and collect its exit status.
Once the parent calls WAIT system call and reads the child's status, the child's PCB can be released.





6. Managing Running Processes: Switching between Them
- With multiple active processes running and concurrent processes are limited to the CPU cores, a large part of
a system's management is repeatedly switching which process is currently running on the CPU at a given moment.

- To achieve this, the OS requires quick access to the PCBs of all currently active processes.
Maintaining the collection in a structure called the PROCESS TABLE, holding the PCB of every process in the system.


What arises when thinking about process switching:
1. WHEN should the OS switch to a differen process? (Policy Question).
2. WHICH process should it switch to? (Policy Question).
3. HOW does the OS actually perform the switch? (Mechanism Question).


The first two are answered by scheduling policies, however the third is answered by the process switching mechanism.


What Triggers a Process Switch?
- Several scenarios can enact the OS to decide the switch of processes:

• The currently running process terminates.
- Thus, the OS must switch to another already-active process.

• A new process is created.
- May immediately switched into.

• The currently executing process becomes blocked.
- Events like a system call that requires waiting, thus a switch would allow the CPU to compute even more.

• An event completes that some waiting process needed.
- The process moves from a 'waiting' into 'ready' state, that the OS may decide to switch into.

• A time slice expires.
- In scheduling policies, where each process is only allowed to run for a fixed amount of time before being interrupted,
in which once reached, it triggers a switch to give another process a turn.



The mechanism: How a process switch actually happens:
- Peforming a process switch requires support from both the HARDWARE and the OS.
As it requires the transition into privileged mode of operations (kernel). Triggered by an interrupt.

- The overhead of this switch, on how long it takes, depends heavily on how much support the hardware provides from a range of 1 to 1000 microseconds.



Understanding the Process Switch Execution Flow Diagram:
- The flow of execution during a switch between two processes, P0 and P1:

• There are three "columns", process P0 on the left, the OS in the middle, and process P1 on the right.

• Initially, P0 is executing (Indicated with an active downward arrow), while P1 is idle (with an idle marker in P1's column)

• An interrupt/system call occurs, handing over control to the OS.
• The OS then performs two key actions:
- "save state into PCB": The current state of P0 (registers, program counter, etc.) is saved into P0's PCB, to return later.
- "reload state from PCB": Previous saved state of P1 is loaded back from P1's PCB.

• Control then passes to P1, which starts/resumes executing, while P0 becomes idle.

• Later, another interrup/system call occurs during P1's execution. Same process in reverse:
The OS saves P1's state into PCB, then reloads P0's state from PCB.

• Control passes back to P0, which resumes executing, while P1 goes idle.

NOTE:
- The idea for this diagram is a "save and restore" operation.
The process when stopped saves its complete state to its PCB, then the next process's state is loaded from its PCB to start
running. Allowing continous execution for processes.



Typical Detailed Steps of a Process Switch:
The mechanism can be seen within ordered steps:
hawrdware (hw), assembly-level code (asm), or C-level code (C):

1. [hw]: Save the program counter and registers onto the stack. Done automatically by the hardware for interrupts.

2. [hw]: Load the program counter in the INTERRUPT VECTOR.
- A table/location that stores the address of the appropriate INTERRUPT SERVICE ROUTE.
- The code responsible for handling particular type of interrupt (for instance a process switch).

3. [asm]: Save the remaining context info for the current running process into its PCB.
Implemented using assembly-level instructions for precision and speed.

4. [asm]: Create a new stack, belonging to the process that's about to be switched.

5. [C]: Perform any remaining work needed to handle this specific type of interrupt. Implemeneted usually with high-level languages like C.

6. [C - scheduling]: Chooose which new processs should be scheduled next, bason on system's scheduling policy.

6. [asm]: Restore the context information for the newly chosen process from its PCB and start/resume its exection.

When this sequence complextes, the new process begins running exactly where the previous left off.
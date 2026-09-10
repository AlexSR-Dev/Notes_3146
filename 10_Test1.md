10 - Test 1

Introduction - Theory:
• Introduction to OS
◘ Reflection and Critical Thinking Questions

1) To begin with there would a Hardware Issue, in which every physical device is its own controller, with
different protocols each in the interaction to the software. Thus, a mismanagement with the protocols of multiple
devices can result in corrupt data or system crash.
1.1) Solution would be "Abstraction", hiding internal complexity, while also displaying simple external interface.
HOWEVER, another issue arise "Sharing Problem", in which multiple users/apps need to share the same hardare resources.
1.2) The solution, is the "Resource Manager", exists in the OS, maintains the organization and fairness of exisitng apps to use the hardware


2) "OS" is a software between hardware and application programs the bridges the connection of utilizing the hardware.
"user interface layer", the shell on top of the OS.

2.1) Handles two Fundamental jobs:
Provides a virtual interface to the hardware.
- On top of the hardware's physical interface, building multiple layers of abstraction, providing simplicity and safety from the apps to hardware.
Acts as the Resource Manager.
- Manages multiple apps/users to the hardware, for faire, efficient, and safety.


3) Services Provided by the OS:
- Many concrete services (Look in 00_IntroToOS.md):
program execution, memory management, file management, I/O management, information maintenance, communication services,
user management, error management, accouting services.

4) Separating Mechanism from Policy:
- An OS would need a manner separate mechanism (how something is done) from policy (what/when/which is chosen), maintaining
the system's flexible and modification over time.

5) User and Kernel Mode:
- Protection is enforced through the two modes of operations:
user mode (restriced) and kernel mode (privileged, full access). Switching occurs via exceptions(signal)/interrupts(automatic).

6) System Calls:
- Contolled and well-defined mechanism by which user programs request privileged OS services, using a "trap"(synchronous interrupt) to switch into kernel mode
and a "library wrapper"(Simple function/system call) to simplify the process for programmers. Switches into kernel mode.
- entry call

7) Internal Structure of Operating Systems:
- OS can be internally organized in different manners:
    - Monolithic - Single large program, all in kernel, efficient but hard to maintain, single error can crash everything.
    EX: The typical structure for a monolithic architecture is to have one main procedure and multiple service procedures.

    - Layered - Independent layers, each depending only on lower layers, clean and structured.

    - Microkernel - Minimal kernel plus separate user-mode service modules communicating via message passing. Easier build, isolate errors, requires more performance overhead.

    - Hybrid/Modern design - Combines mircokernel-style modularity with monolithic-style performance by loading modules directly into the kernel.
--------------------------------------------------------------

Processes - Theory & Application:
• Tests your understanding of basic process-related terminology
    ◘ Multiple Choice
    ◘ Short anser
    ◘ Reflection and Critical Thinking Questions


- Process is the OS abstraction for representing a single runing instance of a program. Allowing, multiple instances of programs often with the same copy at once.

- Process is not a program: A program is a static set of instructions (recipe), while the process is the actual running program (actively
bakes). Process includes the program, its data, its curent state, and resources (memory) it owns.

- Processes are created automatically at system boot or on demand by the user's/system request.

- Process moves though life cycle of states: New -> Ready -> Running.
 - Cycling between: Running -> Waiting -> Ready as needed, and eventually reaching Terminated - either by completing normally, killed by
 the user, or stopping due to an error.

 - Multiprogramming: The OS constantly switches between multiple ready processes often seen in single-processor systems.

 - Scheduling policy: OS decides which process runs next.
  - Preemption: When a running process is interrupted so another process can run, and the interrupted process returns to the ready state.

  Process Control Block (PCB) - A data structure containing the process ID, current state, program counter (picks up where the process left off), and registers, scheduling information, memory management information, and accounting information.



Process Creation - Assiging a unique ID, setting up a memory image (PCB + code/text + global data, heap, and stack regions), configuring memory management structures, and OS bookkeeping.

- Heap and Statck grow towards each other within a processe's memory image, thus a fixed combined maximum size.

Two mechanisms for creating processes:
- Cloning: fork()
- Creation from scratch: CreateProcess() or execvp

fork() - creates a child process, a copy of its parent. Return values, the child's PID to parent, 0 to the child, and -1 to the parent if creation failed. Allowing branching to different code paths.

exec - A child process completely replaces it program, thus removing its parent's code and running a new program, usually paird with fork().

Copy-On-Write: Avoids expensive full memory copying: Thus parent and child share memory, until a modification to the shared data is made to acutalize a copy.

Processes can terminate voluntarily (normal completion or error) or involuntarily (bug, or killed).

- When a parent terminates before its child, the child becomes an orphan and adopted by init.
- When a child terminates before it parents, the child becomes a zombie until the parent calls wait to read it exist status and reaps it.


- The OS keeps all active processes PCBs in a process table, contantly deciding when to switch processes and which process to switch to (scheduding policy) and how to perform the swithc (mechanism).

- Process switch can occur in process termination, new process creation, a process becoming blocked, an awaited event completing, time slice expiring.

- Process switch occurs by saving the current process's state into its PCB and restoring a new process's state from its PCB. A Save/restore cycle.



• Tests your understanding of Unix process-related code in C++
(creating, waiting for processes, and switching to a different program)
    ◘ Fill-in-the-blank
    ◘ Trace code and determine behavior/output
    ◘ Fill in missing parts/ Complete code with multiple choices.

const patient *p1 = static_cast<const patient *>(a);
- Casting the generic pointers into pointers to the patient struct.

#include <unistd.h>
pid_t id = fork();

if (id == -1) {// Failed
    // error
}
else if (id == 0) { // In child process
    execvp(args[0], args); // executes the arr which contains a command in order, with NULL at the end to signal terminatation.
}
else { // In parent process with child PID
    int status;
    wait(&status);
    exit(status);
}




--------------------------------------------------------------

C++ Programming:
• Tests your ability to understand and trace C++ code.

--------------------------------------------------------------

Pthread Basics:
• Understand how to create and use PThreads.

A process has two main characteristics: Its resources (memory) and its execution of instructions, seen as a thread.
- Thread is a basic unit of execution and scheduling.

- A thread utilizes the resources of its process in action.
- A single process can have multiple threads for multiple tasks to run concurrently.

Multi-threaded - A process with more than one thread. EX: Word processors (separate threads for interaction, background reformatting, and auto-save) and web servers.
Single-threaded - A process with one thread only.

- Threads pass through a similar states to processes: New -> Ready -> Running, with the possibility of Waiting (until an event happens, then back to Ready) and eventually to Terminated.

- Threads has its own Program Counter, Stack, Stack pointer, and registers.
Meanwhile code, global variables, and heap memory are shared across all threads in the process.


- OS tracks thread infor with a Thread Control Block (TCB).
The OS does not protect threads within the same process with the assumption that cooperation occurs. Thus design is essential to avoid threads corrupting one another's data.

- Every process starts with one default thread (main), creating more afterwards.
- Multi-threading allows CPU efficiency, cheaper creation/termination of threads, faster switching between threads and cheap communication.




- Thread of execution is a single sequence of instructions. Process has at least one (main), creating additional threads running independently and asynchronously.

- pthread library(pthread.h, compiled with -lpthread) allowing the creation of threads.

- Any function meant to run as a thread must follow the exact signature: void* function(void *),
as void* represnt a pointer to any data type, allowing flexible paramter passing.

- pthread_create(pthread_t *id, const pthread_attr_t *attr, void *(*start_func)(void *), void *arg).
Create a new thread, and returns 0 on success and a positive value on error.

- OS controls thread scheduling by default no guarantee on order.

- When main finishes, it implicitly calls exit(), terminating the entire process and all its threads immediately, even unfinished ones.

- Calling pthread_exit(void *retval) terminates only the calling thread, allowing the rest of the procedess and threads to continue running until all threads complete.

- Thread function automatically calls thread_exit when it finishes, an explicit calls only improves readability.

- To pass data into a thread conver the data's address to void * before passing it, then converting it back to the correct pointer type inside the thread function.

To pass multiple parameters, bundle them into a custom struct, pass the structure's address as void* and unpack it inside the thread function by casting back to the structure type.



EX: Arg param:
int arg:
void * to_pass = (void*)&arg;

Return values converted to void*
char retval;
void* to_return = (void*) retval;



pthread creation:
pthread_t id[sizeof(my_messages)/sizeof(my_messages[0])];
- Creates a pthread id array for all the threads created.
rc[i] = pthread_create(&id[i], NULL, printMessage, (void *) &index[i]);
- passes the address of the arr id and index.
The function itself:
void *printMessage(void *index)
- Returns a generic pointer
- Accepts one generic pointer
- "int i = *(int *)index;"
- Cast the parameter to a pointer integer, then dereference it.

--------------------------------------------------------------

Data Sharing:
• Tests your understanding of data sharing mechanisms.

- Threads within the same process share data implicitly; separate processes can share data with an explicit mechanism.
- Operation in a load, modify, store state are not a whole single step and can be interrupted partway.

Race condition - When the final result of a program depends on the unpredictable timing/order in which threads execute. Thus an overlap of executions, when multiple threads access a shared resource.

Critical region - The area of code where shared data is accessed.

Mutual exclusion - A rule in which no two threads may be inside cretical sections accessing the same shared data at the same time, thus threads must wait until one leaves.
Requirements:
1. No simultaneous access of shared data in critical sections.
2. No reliance on specific hardware.
3. Threads outside their critical section must not block others.
4. No thread should wait forever.

- HGuarantees that only one thread can be inside its critical region on a given time.

Strict Alternation - Simple software based mutual exclusion solution uses a shared turn variable so that two threads take turns entering their critical regions, one at a time, in strict order.

1. Busy waiting: Threads waste CPU cycles by continuously checking the turn variable instead of yielding or pausing while they wait.
2. OVerly rigid turn-taking: A thread can be blocked from re-entering its critical region even when the other thread isn't ready to use its own turn, violating the principle that non-critical activity shouldn't block another thread's critical region access.


- Thus, strict alternation is not practicial for real-world use, getting worse with more threads.
-These limiations allows to explore other mutual exclusion strategies.



EX
pthread_t the_thread;
rc = pthread_create(&the_thread, NULL, myFunction, (void*) &arg);
pthread_join(the_thread, NULL);
- Waits for a particular thread to finish before continuing on the next statement that can be:
std::cout << "Thread #" << arg << " done!" << std::endl;

Stric Alternation:
Shared variables
int count; // Counter threads can modify
int turn = 0; // Determines which thread is currently allowed to enter the critical region. Thread #0 is first


int actual_arg = *((int*) arg); // Passes the thread


while(turn != actual_arg); // Checks whether actual_arg is its turn.

For thread #0:
while(turn != 0);

For thread #1:
while(turn != 1);
- Checks whether it is this thread's turn.
- If turn != actual_arg, the thread waits.
- When turn == actual_arg, the thread leaves the loop
  and can enter the critical region.
- As long as turn ISN'T the current thread, the current thread must WAIT.
count++;
std::cout << "Thread #" << actual_arg
          << " count = " << count << std::endl;

After critical region:
turn = !actual_arg;

- gives the other thread a turn
!0 → 1
!1 → 0

- Repeating until the for loop or condition finishes.
--------------------------------------------------------------




Class Notes:

Pointer Usage:
int a;
int* p;
a = 2;
p = &a; // Stores the address of a.
p = a + 1;
cout << *p; // Derefernces the pointer and returns the value.
// Thus, this pointer returns the current address of variable a, which
is value 3.


int a, int b;
int* p, int* q;
a = 3;
p = *a; // Stores the memory address of a
q = p;  // Stores the memory address of p which points to a.
*q = *q + 5;    // Deference the memory address of p and add.
cout << *p;     // Print the deference value of q which is 8.
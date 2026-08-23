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
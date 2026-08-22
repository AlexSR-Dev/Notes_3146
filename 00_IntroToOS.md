00 - Introduction to Operating Systems:

1. The reason, is due to the Hardware Problem:
- Each piece of physical device has is own contoller and often have their protocol for how software interacts with it.
Therefore, a single miss up with the protocols of multiple devices can corrupt or crash the system or damages data.

The Solution: Abstraction
- Hides the internal complexity while displaying simple external interface. Fundamental concept in CS.


The Sharing Problem:
- A computer doesn't run a single program for a user, but instead many applications for many other users under the
same underlying hardware resource.

The Solution: Resource Manager
- That exists in operating systems, maintains the organization of existing application to the hardware (CPU, memory).




2. What is an Operating System?
- A software between the computer's hardware and applucation programs/(users) that want to use that hardware.

Its Jobs:
1) Provide a Virtual Interface to the Hardware.
- Hides messy details in exchange for simple consistent interface.
For the sake of simplicity(no need to learn hardware controller) and safety (no touching hardware directly).

- Multiple layers and levels of abstactions, provides simple interface.
EX: Hard disk, low level and complex. The OS provides software like "DISK DRIVER", a level of abstraction.
Still remains too complicated to for users to work directly, THUS the OS builds another layer, the FILE.
A simple abstraction of stored data on a disk. Allows programs/users to open, read, and write rather than interact with disk driver or disk controller.


NOTE:
Abstraction are often built on layers on top of itself.


2) Act as a Resource Manager.
- Allows a fair, efficient, and safe arrangement of application/users to share with the computer's hardware resources.

The operating systems becomes a gateway of the interaction of information, such as layers:
Top Layer - Applications: Music player, email, etc. These interact with the OS in a VIRTUAL INTERFACE.
Middle Layer - Operating Systems: Between applications and hardware.
Bottom Layer - Hardware: Physical components. The OS interacts with hardware through a PHYSICAL INTERFACE.



3. Services Provided by the Operating System
- With the responsibilities of (virtual interface + Resource management), OS can provide various services.

- Program Execution: Loading a program and its data into memory, then scheduling and executing it.

- Memory Management: Manages the computer's main memory (RAM), preventing interference of different program's memory space.

- File Management: An OS's abstraction over secondary storage (hard drive). Allows the modification and creation.

- I/O Management: Provides safe and controlled access of devices, apps wold never interact with them directly.

- Information Maintenance: Services that allow the modification of system's time and date by apps/users.

- Communication Services: Allows different programs to interact with on another.

- User Management: The management of user's log in and authentication, alongside the privileges of users to the system.

- Error Management: Detects and handles errors, to prevent system crash or corrupt data.

- Accounting Services: Collects statistics and monitoring system performance over time.

OS must be able to EVOLVE gradually over time to new hardware and requirements. With its long lifespans.




4. Managing Complexity: Separating Mechanism from Policy
- With OS's size and lifespan, designers rely on one organizing principle: SEPARATING MECHANISM FROM POLICY.
Separates the 'how' something is done from 'what', 'when', or 'which' things are done.

- Policy: The procedures used to dictate which action to commit among several possibilities.
EX: Serveral programs waiting to run, the policys decides the 'which', the 'next', and 'when'.

- Mechanism: Data structures and operations are used to implement a service. The 'how'.
EX: A specific mechanism for loading program into memory and starting its executions, determines the 'how' for programs to load and run.

NOTE:
This separation keeps the system flexible: to swap decision-making rules independently from low-level implementation.




5. Protection: User Mode and Kernel Mode
- Integral part of OS's job is 'PROTECTION'.
Protects the hardware from misuse, and multiple apps/users from interfering with each other.


Two Modes of Operations:
- OS's achievement to protection, is split into two modes.

1) User Mode:
- When code is executed on behalf an app/user. Called PROTECTED MODE.
- Progams in this mode have NO DIRECT ACCESS TO HARDWARE.
- Can execute only a RESTRICTED SUBSET OF INSTRUCTIONS.
- Can access only RESTRICTED AREAS OF MEMORY.


2) Kernal Mode (Synonyms: Monitor Mode, Supervisor Mode, or System Mode):
- When code is executing on behalf of the OS itself. Called PIVIEGED MODE.
- Code running in this mode has COMPLETE ACCESS TO HARDWARE.
- Can execute ANY INSTRUCTIONS.
- Can access ANY MEMORY AREA.


These operate like two stacked layers: apps like web browser or music player run in user mode, above the OS.
Which in turn runs the kernel mode, above the hardware, having full control.


How Hardware Enforces These Modes:
- Mode bit: How the system tracks the current mode used.
In the event a program in USER mode performs a PIVILEGED operation, two requirements are needed:
1. The operation must be prevented from taking place.
2. The system must be notified that this attempt occurred.

- Achieved through an EXCEPTION, specifically a SYNCHRONOUS INTERRUPT.
Thus, an interrupt is caused by the instruction being executed, whether synchronous or not, causes the system to switch into kernel mode.
When exceptions occur, the system automatically swithces to KERNEL MODE.




6. System Calls: How Applicatios Access OS Services:
- For applications running in user mode that can't get anything useful done, it uses the SYSTEM CALL.

- System Call: Formal interface between a running app and the OS. Provides a CONTROLLED ENTRY POINT into the kernel
for peforming privileged operations, and ensures access is done in a specific, defined, and safe manner.


How System Call Works:
- When the user program needs an OS service, it invokes a system call. Switching from user into kernel mode.
The mechanism that achieves this switch is called a TRAP.
- TRAP: A specific kind of synchronous interrupt. (As previously mentioned. Switches into kernel).

System calls are invoked using low-level assembly language instructions.
For programmers, the OS provides LIBRARY or API that apps can call using function calls.
- The library function acts as a WRAPPER around the true system call to hide the messy low level details of triggering the TRAP.


Overall Flow:
1. User Space: App is executed normally. (executing user program).
2. The app invokes the system call (through library wrapper).
3. System switches to kernel mode.
4. KERNEL SPACE: System call is executed with full privileges.
5. The system returns to user mode.
6. USER SPACE: Control returns from system call back to the app, which continues executing.


Worked EX: The Read System Call.
- Reading data from a file using the read system call.
1. Before the call, the calling program 'pushes the necessary parameters into the system stack', in this scenario number of bytes to read,
the address of the buffer to read into, and file descriptor identifying which file to read from.

2. The program calls the read library function (the WRAPPER).

3. Library function pushes any additional information needed onto the stack, then places a special code identifying this system call into register.

4. Executes a trap instruction, that switches the system into kernel mode.

5. In kernel space, the operating system's dispatcher looks up the system call code in a table to determine the exact system call handler needed to run.

6. The necessary system call handler executes, where the pivileged work of reading data occurs.

7. Once the system call handler finishes, control return back through the dispatcher and library function, the system switches back to user mode,
and the stack pointer is adjusted (incremented) to clean up.

8. Lastly, control returns to the original caller, the user program, that now has its data and continues executing.


NOTE:
User code never directly performs the privileged operation. Always through a controlled, well-defined path into kernel and back.




7. Internal Structure of Operating Systems:
- The 'how' an OS can be internally organized.
- Three Classic Architectural styles.
- One Modern Hybrid Approach.


7.1 Monolithic Architecture:
- The entire operating system is written as a single program.
Thus a large collection of procedures linked together as one executable, the whole program run fully in kernel mode.

- This approach nickname "SPAGHETTI NEST" appraoch.
- Due to the fact that the surface appears everything is tangled together.
- In reality, it has some internal structure, organized into three rough categories:

• Main procedure: The ENTRY point into the OS. When a user program wants to access an OS service, the controls are passed into here first.

• Service procedures: Handles and carry out the various system calls the OS supports (EX: read, write, and etc.)

• Utility Procedures: Provides common helper code used by multiple service procedures. EX: Several different file-related system calls (read/write)
might need to transfer data to or from the disk. Rather than duplicating the code, it pulled outs into shared utility procedures that service procedures can call on as needed.

- Typical structure for monolithic architecture is to have one main procedure and multiple service procedures. EX: Linux and Windows.

Pros & Cons:
• Pro: Everything is part of one program, procedures can call each other directly, making these calls efficient.

• Con: Designing, implementing, and debugging of this OS is difficult,
as isolating bugs is hard to specify.

• Con: The OS becomes UNWIELDY and HARD to understand in proportion to its growth.

• Cong: As everything runs together as a single program, an error anywhere in the OS results in bringing down the entire OS.




7.2 Layered Architecture
- Divides the OS into multiple distinct layers, each responsible for specific set of operations/services, and each layer depends only on the layers below it.
Independent layers are the layers above them.


EX: A simple batch system called THE. Its layers from bottom to top:
Layer               Function
0               Processor allocation and multiprogramming
1               Memory and drum management
2               Operator-process communication
3               Input/output management
4               User programs
5               The operator

From the lowest layer, it handles the most fundamental hardware-level concerns, and each higher layer adds more user-facing capability, until user programs and human operators at top.


A Variant: Concentric Circles
- Same layered idea, thought as set of concentric circles (rings), rather than a stack.

• The INNERMOST CIRCLE represents the hardware, layer 0.
• Each ring moving outward represents a higher layer.
• The OUTERMOST RING represents the user interface, layer N.
• INNER LAYERS hasve HIGHER PRIVILEGE than OUTER LAYERS, thus the privilege decreases moving from the center outward.

OS EX: Multics




7.3: Microkernel Architecture
- Splits OS functionality into many small, independent modules.

• A small CORE MODULE, called the MICROKERNEL, only part of the OS that runs in kernel mode.
• All other modules, like file server, window server, memory server, etc runs in USER MODE.
• These modules communicate with each each other using MESSAGE PASSING, rather than direct procedure calls.

Visualization:
- Rows of boxes sitting on top of a foundation
- The top has the "User Program", (file, window, memory server, etc).
- Underneath all of them is the MIRCOKERNEL, that handles the most essential core mechanisms. (scheduling, timers, interrupts).

How requests flow in this design:
- EX: user program want access to file management services. Cannot talk to the file server directly.
- Issues a system call to the mircrokernel, that redirects the request to the appropriate service, here it would be the file server.

The flow: user mode -> kernel mode (mircrokernel) -> back to user mode (file server).
- This guarantees safety, as program can access service modules directly, the kernel always mediate and control access.

Micorkernel-based OS EX: QNX and MINIX 3.
- Used in EMBEDDED AND REAL-TIME SYSTEMS, has limited resources and very specialized functionality requirements.

Pros & Cons:
• Pro: EASIER TO DESIGN, IMPLEMENT, and DEBUG than monolithic OS as functionality is split into separate modules.

• Pro: MORE FLEXIBLE AND EASIER TO EXTEND, new services can be added as new modules.

• Pro: BETTER FAULT ISOLATION, if one module crashes it does not bring down the whole OS.

• Pro: Microkernel systems tend to be MORE RELIABLE AND MORE SECURE.

• Con: SIGNIFICANT PERFORMANCE OVERHEAD, since accessing system services requires repeatedly switching back and forth between user mode and kernel mode (also passing messages between modules), is acutally slower than direct procedure calls from a single monolithic program.




7.4 Modern Operating System Design: A Hybrid Approach
- Most modern OS's don't use a 'pure' version of any architecture mentioned.
- Instead, utilizes a HYBRID, OBJECT-ORIENTED APPROACH that borrows ideas from both monolithic and mircokernel designs:

• From microkernel systems, functionality are in separate modules, each responsible for a specific area. (device & bus driver, file systems, etc.)

• Unlike microkernel systems, these modules are LOADED DIRECTLY INTO THE KERNEL ITSELF, rather than operating as separate user-mode processes. Avoiding performance overhead from the back and forth user and kernel mode.

• Different modules communicate to each other through WELL-DEFINED INTERFACES.

IRL Design: Core Solaris Kernel.
- Operates at the center with multiple specialized modules attached to it (device & bus drivers, file systems, etc). All loaded into and communicating with the core kernel as needed.
- Provides modern OS with organizational cleanliness and extensibility of mircorkernel design, while keeping performance benefits of a monolithic design.
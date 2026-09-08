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
--------------------------------------------------------------

Processes - Theory & Application:
• Tests your understanding of basic process-related terminology
    ◘ Multiple Choice
    ◘ Short anser
    ◘ Reflection and Critical Thinking Questions


• Tests your understanding of Unix process-related code in C++
(creating, waiting for processes, and switching to a different program)
    ◘ Fill-in-the-blank
    ◘ Trace code and determine behavior/output
    ◘ Fill in missing parts/ Complete code with multiple choices.

--------------------------------------------------------------

C++ Programming:
• Tests your ability to understand and trace C++ code.

--------------------------------------------------------------

Pthread Basics:
• Understand how to create and use PThreads.

--------------------------------------------------------------

Data Sharing:
• Tests your understanding of data sharing mechanisms.

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
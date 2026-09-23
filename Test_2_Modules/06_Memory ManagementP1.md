06 - Memory Management

Part 1: Partitioning and Static, Equal-Sized Partitions

1. What Does a Process Need to Execute?
- A process requires two broad categories of resources:

1.1 Active Resources
- Are resources the process actively uses while executing:
• CPU cycles - To execute instructions.
• I/O devices - to read and write information.
These are primarly handled by the process scheduling.


1.2 Memory Resources
- A process needs memory to store everything that makes up the process.
Three major components:
1) Program code ("text")
• The machine instructions that make up the program.
2) Data
• Variables, Heap, Stack, and other information the program allocates/manipulates while running.
3) OS structures
• Information the OS maintains about the procss.
• Process Control Block (PCB).

NOTE:
Process data = program code + program data + OS structures for the process

---------------------------------------------------------------------------------------------------------------------------------

2. Why Must Process Data Be in Main Memory?
- A process cannot execute while its required data exists only on disk.

The CPU need to access: Instructions, Data, Operands.
From main memory (RAM) while the process is executing.

Basic sequence: Disk -> Main Memory -> CPU executes process

The reason the process can be stored on disk permanently is:
- Main memory is volatile, does data is lost when power is out.
- Disk/SSD storage is non-volatile, information can remain stored.

THUS:
1) Process data is stored on non-volatile storage.
2) The OS loads the process data into main memory.
3) The process can execute.
4) The process's memory contents can eventually be stored back on disk.

---------------------------------------------------------------------------------------------------------------------------------

3. Why Not Keep Only One Process in Memory?

Simplest possible memory-management strategy would be:
1) Load one process from disk.
2) Give it all of main memory.
3) Let it run until it finishes.
4) Store its contents back on disk.
5) Load the next process.

- Simple, but creates two major issues:
Poor CPU utilization
Poor memory utilization


Problem #1 - Poor CPU Utilization
- A process does not continuously use the CPU.
As execution alterates between CPU burst and I/O wait.

EX of a process:
1) Execute instructions.
2) Request a file from disk.
3) Becomes blocked while waiting for the I/O operation.
4) Resume when the I/O finishes.
- THe I/O operations are slower than CPU operations.
- THUS, with one process the CPU may remain idle at times.

The solution would be OS using multiprogramming.
- Allowing multiple processes to remain active for the scheduler to switch to another ready process when the current process blcoks.



Problem #2 - Poor Memory Utilization
- A process usually does not need all of the computer's main memory.
A process requires small portions of memory, the remaining memory would be unused.
THUS a one-process-at-a-time approach wasts:
- CPU capacity when the process waits for I/O.
- Memory capacity when the process does not need all available memory.

SOLUTION:
The solution to both problems is to allow multiple processes to reside in memory at the same time.

---------------------------------------------------------------------------------------------------------------------------------

4. Memory Must be Multiplexed
- CPU is multiplexed among processes in time:
Time →
P1 | P2 | P3 | P1 | P2 ...

- Memory needs to be multiplexed among processes in space:
Memory
+---------+
|   P1    |
+---------+
|   P2    |
+---------+
|   P3    |
+---------+

- With memory, multiple processes can physically occupy memory simultaneously, which creates additional problems.

---------------------------------------------------------------------------------------------------------------------------------

5. Requirements of a Multi-Process Memory Scheme
- Any system that allows multiple processes to reside in memory must satisfy serveral requirements.

1 - Multiple processes should reside in memory
- The reason for multiple processes available is for the CPU to switch between them.
EX:
If A blocks for I/O, the scheduler can run B or C.


2 - Processes should not collide in memory
- Each process needs its own region.
THUS, you cannot accidentally allocate overlapping regions.

- If memory regions overlap accidentally, one process could overwrite another process's information.
Resulting in:
- Corrupted data
- Incorrect execution
- System instability


3 - A process should not access another process's memory
- It's memory protection.
Even if the OS gives every process its own separate region, a process might attempt to access an address outside its region.
- The OS must prevent this from:
• Accidental access caused by programming errors.
• Intentional access to another process's information.


4 - Processes should be able to share memory when desired
- Protection cannot mean that all overlap is forbidden.

Some processes need to share memory.
EX:
• Cooperating processes comunicating through shared memory.
• Multiple copies of the same program sharing one copy of its code.

THUS, the OS needs: "Controlled overlap"
- An explicitly permitted overlap.

Memory protection - Prevents unauthorized memory access.
Controlled overlap - Allow sharing when the system deliberately permits it.

---------------------------------------------------------------------------------------------------------------------------------

6. Memory Partitioning
- A basic manner to allow multiple processes to occupy memory is "memory partitioning."

Basic idea:
1) Divide main memory into multiple regions.
2) These regions are called partitions.
3) Allocate processes to partitions.
4) Protect the boundaries between partitions.
- Essentially: Divide memory, Allocate processes, and Ensure protection.

---------------------------------------------------------------------------------------------------------------------------------

7. Design Questions for Memory Partitioning

Q1. How should memory be divided?
Should partitions: All have the same/differet sizes? Many/few?

Q2. Which process gets which partition?
Should a process be assigned: Arbitrarily? Based on its size? Or another rule?

Q3. Should partitions be static or dynamic?

- All these choices lead to different memory-management schem,es.
Files begin with the simplest version: Static, equal-sized partitions.

---------------------------------------------------------------------------------------------------------------------------------

8. Static, Equal-Sized Partitions
- Two fundamental rules.

Rule 1 - Divide memory into equal-sized partitions.
- Partitions are created statically, and its boundaries are established at system startup and do not change afterward.
Every partition has the same size.


Rule 2 - Map each process to one partition
- Each process is assigned to a partition.
Main Memory

+-------------+
| Partition 1 |
|   1000 KB   |
+-------------+
| Partition 2 |
|   1000 KB   |
+-------------+
| Partition 3 |
|   1000 KB   |
+-------------+

---------------------------------------------------------------------------------------------------------------------------------

9. How Allocation Works
- Important Issue: Processes are not necessarily the same size.

- Suppose every partition is: 1000 KB
But processes require:
P1 = 800 KB
P2 = 100 KB
P3 = 600 KB

- Allocation can become:
Partition 1
+----------------+
| P1 = 800 KB    |
|                |
| 200 KB unused  |
+----------------+

Partition 2
+----------------+
| P2 = 100 KB    |
|                |
|                |
| 900 KB unused  |
+----------------+

Partition 3
+----------------+
| P3 = 600 KB    |
|                |
| 400 KB unused  |
+----------------+

- Leading to the first major weakness of the scheme.

---------------------------------------------------------------------------------------------------------------------------------

10. Internal Fragmentation
- Is unused memory inside an allocated partition because the process occupying that partition does not need all of it.
Key Word: Interal
- As wasted memory is inside a region that has already been allocated to a process.

EX:
Partition = 1000 KB
Process   = 100 KB
Unused = 900 KB

- The 900 KB cannot just be given to another process.
BECAUSE: The entire partition belongs to the process, including unused portions.



10.1 Why Internal Fragmentation Is Bad
- If there are many small processes, that under utilizes the allocated memory of the parition.
EX:
+----------------+
| P1 | huge gap  |
+----------------+
| P2 | huge gap  |
+----------------+
| P3 | huge gap  |
+----------------+
| P4 | huge gap  |
+----------------+
- The memory may look full to the "allocator" because every partition is assigned, while a lot of the memory is actually unused.

NOTE:
Internal fragmentation = wasted space inside an allocated memory region.

---------------------------------------------------------------------------------------------------------------------------------

11. What If There Are More Processes Than Partitions?
- Suppose the system has:
3 partitions
7 processes

The solution would be "swapping".
- The system can map multiple processes to the same partition, but:
Only one of those processes can occupy that partition at a time.
- Meanwhile the others remain on disk.

---------------------------------------------------------------------------------------------------------------------------------

12. Swapping
- Moving a process's data betweem main memory and disk to allow another process to use the same memory partition.

Suppose: Partition 1 -> P1
- Now P4 needs to use Partition 1.
The OS peforms an exchange.

Step 1 - Store the current process
- Move P1's data: RAM -> Disk

Step 2 - De-allocate the partition
- Partition 1 is no longer assigned to P1.

Step 3 - Allocate the partition
- Given Partition 1 to P4

Step 4 - Load the new process
- Move P4's data: Disk -> RAM

THIS is Swapping.

---------------------------------------------------------------------------------------------------------------------------------

13. Why Swapping Is Expensive
- Swapping allows the OS to manage more processes than there are partitions.

MAJOR COST: Disk access is much slower than main-memory access.

Every swap involves transferring the process's data:
1) Memory -> disk
2) Disk -> memory
THUS, frequent swapping can become expensive.

NOTE:
Swapping allows different process to "take turns using the same partition."

---------------------------------------------------------------------------------------------------------------------------------

14. Memory Protection with Based Address and Bounds Checking
- Next Issue is protecting each process's memory.

The OS needs to ensure: A process can only access addresses inside its assigned partition.
- The scheme uses a "base address (BA)."

Base Address - Is the starting memory address of the process's assigned partition.
EX:
Partition 1:
BA = 0
Size = 1000

THEN:
Valid addresses:
0 – 999

For another partition:
Partition 2:
BA = 1000
Size = 1000

Valid Addresses:
1000 – 1999

---------------------------------------------------------------------------------------------------------------------------------

15. The Bounds-Checking Formula
- An address is legal if:
BA <= Address < BA + PartitionSize

Lower Bound: 
- The Base address is included.

Upper Bound:
- The address: BA + PartitionSize, is excluded.

So if:
BA = 1000
Partition size = 1000

Then: 1000 <= Address < 2000
Valid Addresses: 1000 through 1999
Invalid: 2000

REMAINDER:
Base is included; base + size is excluded.

---------------------------------------------------------------------------------------------------------------------------------

16. Why the Check Happens on Every Memory Access
- The system cannot just check the process once as it is loaded.
The process could later attempt to access another memory address.

THUS, every memory access must be checked.
INCLUDES:
• Instruction fetches
• Loads
• Stores

- Checks must happen in hardware because checking every memory access through software would be too slow.

---------------------------------------------------------------------------------------------------------------------------------

17. Worked Protection Example
Main memory:
0 - 4999

Partition Size:
1000

THUS: 5000/1000 = 5

There are 5 partitions:
| Partition | Base | Address Range |
| --------- | ---: | ------------: |
| Ptn 1     |    0 |         0–999 |
| Ptn 2     | 1000 |     1000–1999 |
| Ptn 3     | 2000 |     2000–2999 |
| Ptn 4     | 3000 |     3000–3999 |
| Ptn 5     | 4000 |     4000–4999 |


17.1 Q1 - Can P1 Access 1004?
- P1 belongs to Partition 1.
THUS:
BA = 0
Partition size = 1000

Apply: 0 <= Address < 1000
TEST: 0 <= 1004 < 1000
Result in false.
THUS: No, P1 cannot access address 1004.
- Address 1004 belongs to Partition 2.



17.2 Q2 - Can P4 Access 3000?
- P4 belongs to Partition 4.
THUS:
BA = 3000
Partition size = 1000

Apply: 3000 <= Addres < 4000
Test: 3000 <= 3000 < 4000
Result in true.
THUS: Yes, P4 can access address 3000.

---------------------------------------------------------------------------------------------------------------------------------

18. General Procedure for Bounds-Checking Questions
Use the following Steps.

Step 1 - Identifying the process's partition
- Determine which partition belongs to the process.

Step 2 - Find its base address
- The base address is the beginning of that partition.

Step 3 - Identify the partition size
- For this scheme, all partition have the same size.

Step 4 - Apply:
BA <= Address < BA + PartitionSize

Step 5 - Decide
- If true: Access Allowed
- If false: Access illegal

EX:
BA = 2000
Partition size = 1000
Requested address = 2999

Check: 2000 <= 2999 < 3000
True -> allowed

Now: Requested address = 3000
Check: 2000 <= 3000 < 3000
False -> Not allowed.

---------------------------------------------------------------------------------------------------------------------------------

19. Advantages of Static Equal-Sized Partitioning

Simplicity
- Because every partition is the same size:
• The OS does not need complicated allocation logic.
• It does not need to search for a partition of a particular size.
• The partition size is one system-wide value.
• Protection can be performed using the base address and partition size.

NOTE:
Static equal-sized partitioning is easy to implement and manage.

---------------------------------------------------------------------------------------------------------------------------------

20. Disadvantages of Static Equal-Sized Partitioning

Several Major Weaknesses:

20.1 Memory Under-utilization
- Due to internal fragmentation, a large amount of memory would be allocated but not fully utilized.

20.2 Large processes cannot run
Suppose:
Partition size = 1 MB
Process requires = 1.5 MB

- The process cannot run, even if the system has plenty of total free memory, there is no single partition large enough.
And the scheme cannot combine partitions to accommodate the process.

---------------------------------------------------------------------------------------------------------------------------------

21. The Fundamental Problem: One Size Fits All
- Every process must fit inside one fixed-size partition.
But not every processes is the same size.

Small Partitions
Advantages:
• Less wasted space for small processes.
• More partitions can fit in memory.

Disadvantages:
• Large processes cannot run.


Large Partitions
Advantages:
• Larger processes can run.

Disadvantages:
• Small processes waste much more memory.

---------------------------------------------------------------------------------------------------------------------------------

22. Complete Process of Static Equal-Sized Partitioning
          MAIN MEMORY
              ↓
     Divide into partitions
              ↓
      Equal-sized partitions
              ↓
     Assign processes
              ↓
      +-------------+
      | Process 1   |
      +-------------+
      | Process 2   |
      +-------------+
      | Process 3   |
      +-------------+
              ↓
       Protect boundaries
              ↓
   Base + bounds checking
              ↓
   If more processes exist
              ↓
          SWAPPING
       RAM ↔ Disk

---------------------------------------------------------------------------------------------------------------------------------

23. Key Terms:
| Term                       | Meaning                                                                     |
| -------------------------- | --------------------------------------------------------------------------- |
| **Process data**           | Program code, program data, and OS structures associated with a process     |
| **Main memory**            | RAM where process data must reside for execution                            |
| **Memory multiplexing**    | Sharing memory among multiple processes in space                            |
| **Memory partitioning**    | Dividing main memory into regions called partitions                         |
| **Partition**              | A distinct region of main memory assigned to a process                      |
| **Static partitioning**    | Partition boundaries are fixed at startup                                   |
| **Equal-sized partitions** | Every partition has the same size                                           |
| **Internal fragmentation** | Unused memory inside an allocated partition                                 |
| **Swapping**               | Moving process data between memory and disk to reuse a partition            |
| **Base address (BA)**      | Starting address of a process's assigned partition                          |
| **Bounds checking**        | Checking whether a requested address falls within the process's legal range |
| **Memory protection**      | Preventing a process from accessing unauthorized memory                     |
| **Controlled overlap**     | Allowing memory sharing when explicitly permitted                           |

---------------------------------------------------------------------------------------------------------------------------------


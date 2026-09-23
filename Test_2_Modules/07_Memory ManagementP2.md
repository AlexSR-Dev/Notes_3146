07 - Memory Management

Part 2: Static, Unequal-Sized Partitions
Overview: The main issue from Part 1 was that forcing every partition to have the same size creates a 
one-size-fits-all system: Thus small processes waste large amounts of memory, meanwhile processes larger than
one partition cannot run.
Part 2 changes one major thing: Partitions are still fixed at startup, but they are now different sizes.

---------------------------------------------------------------------------------------------------------------------------------

1. Basic Idea
- The scheme of Static unequal-sized partitioning follows two rules:
1) Create partitions of different sizes at system startup.
2) Assign each process to a partition large enough to hold it.

- The partition boundaries remain fixed after startup. The difference is that partitions are no longer identical.
EX:
Main Memory

+------------------+
| Partition 1      | ← Small
+------------------+
| Partition 2      | ← Medium
+------------------+
|                  |
| Partition 3      | ← Large
|                  |
+------------------+
- The OS creates partitions with different capacities.

---------------------------------------------------------------------------------------------------------------------------------

2. Why Use Unequal-Sized Partitions?
- To match the fact that processes have different memory requirements.

In Part 1 with static equal-sized partitions:
Partition = 1000 KB
P1 = 800 KB
P2 = 100 KB
P3 = 600 KB
- P2 would waste 900 KB of memory.

With unequal-sized partitions, the OS creates:
Partition 1 = 200 KB
Partition 2 = 700 KB
Partition 3 = 1000 KB
- Thus, the process fit their partitions more closely.

GOAL: Reduce internal fragmentation by matching partition sizes more closely to process sizes.

---------------------------------------------------------------------------------------------------------------------------------

3. Example of Allocation
Suppose there are three partitions:
Partition 1 = Small
Partition 2 = Medium
Partition 3 = Large

And three processes:
P1 = Large
P2 = Small
P3 = Medium

The allocation becomes:
+--------------------+
| P2                 | ← Small process
| small unused space |
+--------------------+
| P3                 | ← Medium process
| small unused space |
+--------------------+
| P1                 | ← Large process
| small unused space |
+--------------------+

- Instead of arbitrary equal-sized regions, the processes are matched to partitions.

---------------------------------------------------------------------------------------------------------------------------------

4. Matching Processes to Partitions
P1 -> Partition 3
- As P1 is the largest process it would go into the largest partition available. Nearly filling it and leaving a few unused space.

P2 -> Partition 1
- P2 is small and can fit into Partition 1/2, but 1 is the smaller choice.
Since P2 would waste more memory and occupy a partition for other processes.

P3 -> Partition 2
- As a result each process is placed into a partition close to its required size.

---------------------------------------------------------------------------------------------------------------------------------

5. Internal Fragmentation Still Exists
- Unequal-sized partitions REDUCE internal fragmentation; they DO NOT eliminate it.
BECAUSE: The partitions are still fixed.

Suppose:
Partition = 600 KB
Process = 500 KB
- 600 - 500 = 100 KB
- Of unused spaced inside the parition, thus internal fragmentation.

Comparison:
Equal-Size Partitions:
Process size ≪ Partition size
       ↓
Large amount of wasted space

Unequal-Sized Partitions:
Process size ≈ Partition size
       ↓
Smaller amount of wasted space
- Thus improves memory utilization, but does not completely solve fragmentation.


| Equal-Sized                                      | Unequal-Sized                                          |
| ------------------------------------------------ | ------------------------------------------------------ |
| All partitions have the same size                | Partitions have different sizes                        |
| Created statically                               | Created statically                                     |
| Each process gets one partition                  | Each process gets one sufficiently large partition     |
| Any partition is effectively interchangeable     | Choice of partition matters                            |
| More internal fragmentation                      | Less internal fragmentation                            |
| No placement policy is needed                    | Placement policy is required                           |
| Protection only needs BA + common partition size | Protection needs BA + individual limit                 |
| Still cannot run process larger than partition   | Still cannot run process larger than largest partition |


---------------------------------------------------------------------------------------------------------------------------------

6. Swapping Still Works
- Unequal-sized partitioning can still use swapping.

When there are more processes than available partitions:
1) Store the current process's data on disk.
2) De-allocate its partition.
3) Allocate the partition to another process.
4) Load the new process's data into memory.

---------------------------------------------------------------------------------------------------------------------------------

7. Important New Constraint on Swapping
- One additional requirement.
A process can only be swapped into a partition that is large enough to hold it.

EX:
Partition 1 = 300 KB
Partition 2 = 700 KB
Partition 3 = 1200 KB

Suppose:
P = 800 KB

P cannot be swapped into:
Partition 1 → 300 KB ❌
Partition 2 → 700 KB ❌
Partition 3 → 1200 KB ✅
- Thus MATCHING requirement applies NOT ONLY during original allocation, but also during SWAPPING.

---------------------------------------------------------------------------------------------------------------------------------

9. Protection: Why Base Address Alone Is No Longer Enough
Part 1 - Equal-sized partitions
- The OS only needed to store: Base Address (BA)
Because every partition had the same size.

IF:
BA = 1000
Partition size = 1000
- Then the OS already knew: 1000 <= Address < 2000
The partition size was a single system-wide constant.



Part 2 - Unequal-sized partitions
Now Consider:
Partition 1 = 500
Partition 2 = 1000
Partition 3 = 1500

Knowing only: BA = 1000
- This does not tell the OS where that particular partition ends.
THUS, the OS needs another value.

Value: Limit

---------------------------------------------------------------------------------------------------------------------------------

10. Base Address + Limit
- For every process the system stores:
Base Address(BA)
- Starting address of the process's partition.

Limit
- The size of that particular partition.

THUS:
Protection information:
BA + Limit

EXAMPLE:
BA = 2000
Limit = 1000

The process can access: 2000 - 2999

---------------------------------------------------------------------------------------------------------------------------------

11. Base-and-Limit Bounds Checking
The formula is: BA <= Address < BA + Limit

Part 1 was: BA <= Address < BA
- When partition size was the same for everyone.

Part 2 is: BA <= Address < BA + Limit
- Where Limit is specific to the process's partition.

---------------------------------------------------------------------------------------------------------------------------------

12. Example of Base-and-Limit Checking
Suppose:
BA = 3000
Limit = 500

Then: 3000 <= Address < 3500
Address = 3200
- 3000 <= 3200 < 3500
TRUE, Access allowed.


Address = 3499
- 3000 <= 3499 < 3500
TRUE, Acess Allowed


Address = 3500
- 3000 <= 3500 < 3500
FALSE, Acess denied

- Lower bound is inclusive; upper bound is exclusive.

---------------------------------------------------------------------------------------------------------------------------------

13. Why is the Limit Necessary?
Think of two partitions:
Partition A:
BA = 1000
Limit = 500

Partition B:
BA = 1500
Limit = 1500

- Both have different sizes.
If the OS only stored: BA = 1000
- It would know where the partition begins but not where it ends.


The limit provies the missing information:
BA → where it starts
Limit → how large it is

- THUS: Base + Limit completely describes the process's legal memory range.

---------------------------------------------------------------------------------------------------------------------------------

14. Partition Placement Policy
- Unequal-sized partitions introduce another issue that wasn't in equal-sized partitioning.
Which partition should a process receive?

- As equal-sized partitions, had equal sizes.
- With unequal-sized partitions, the choice matters with the various of sizes available.

---------------------------------------------------------------------------------------------------------------------------------

15. What Is a Partition Placement Policy?
- A rule used to determine which partition a process should be assigned to.

EX:
Process = Small
Available:
Small partition
Large partition

- A resaonable policy would preferr: Small process -> Small partition.
Since the other option wastes more space.

---------------------------------------------------------------------------------------------------------------------------------

16. Why Placement Matters
- Placing small processes into huge partition, would prevent future large process from using the huge partition and even function.
Partition placement affects both current memory usage and future allocation possibilities.

---------------------------------------------------------------------------------------------------------------------------------

17. Advantages of Static, Unequal-Sized Partitioning

Two Major Advantages:
17.1 Better Support for Different Process Sizes
- This scheme does not assume all processes need approximately the same amount of memory.
Making the system more flexible.


17.2 Less internal fragmentation
- Since processes are matched more closely to partition sizes, rather than wasting unused space from large partitions with small processes.
There is considerable less unused memory.

---------------------------------------------------------------------------------------------------------------------------------

18. Disadvantages

#1 - Increased Complexity
- Unequal-size partitions require more decision-making.

The OS now needs a: Partition Placement Polcy
- To determine which partition should receive each process.

Protection is more complicated since the OS now stores:
- BA + limit, instead of a system-wide partition size.



#2 - Partitions Are Still Static
- The partitions are still created at system startup.
Thus the OS has to decides: HOW MANY of the various partition sizes to CREATE.

- Partition arrangement may be poorly suited to the workload.



#3 - The Largest Partition Still Sets a Limit
- Unequal-sized partitions solve one issue, but does not allow an arbitrarily large process to run.

EX:
Largest Partition = 2 MB
Process 1 = 2.5 MB
- The process cannot be split across multiple partitions.

---------------------------------------------------------------------------------------------------------------------------------

19. The Fundamental Limitation
- Partition sizes are still a guess made before the actual workload is known.

Progession:
Equal-sized partitions
        ↓
Too much wasted memory
        ↓
Unequal-sized partitions
        ↓
Better matching
        ↓
Less internal fragmentation
        ↓
BUT...
        ↓
Partition sizes are still fixed
        ↓
May not match future workload
        ↓
Large processes may still not fit

---------------------------------------------------------------------------------------------------------------------------------

20. Key Terms:
| Term                           | Definition                                                               |
| ------------------------------ | ------------------------------------------------------------------------ |
| **Static partitioning**        | Partitions are created at startup and their boundaries remain fixed      |
| **Unequal-sized partitions**   | Partitions are deliberately created with different sizes                 |
| **Partition placement policy** | Rule for deciding which partition receives a process                     |
| **Internal fragmentation**     | Unused space inside an allocated partition                               |
| **Swapping**                   | Moving process data between RAM and disk to reuse a partition            |
| **Base address (BA)**          | Starting address of a process's partition                                |
| **Limit**                      | Size of the process's assigned partition                                 |
| **Base-and-limit checking**    | Checking whether a memory address falls within the process's legal range |

---------------------------------------------------------------------------------------------------------------------------------

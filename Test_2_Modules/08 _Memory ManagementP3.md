08 - Memory Management

Part 3 - Dynamic, Variable-Size Partitions and Address Translation

---------------------------------------------------------------------------------------------------------------------------------

1. Dynamic, Variable-Sized Partitioning
Basic Idea:
- Unlike static partitioning, in which partition sizes are decided before the system knows the processes that will run.
Dynamic, variable-sized partitioning begins with all of main memory as one large block of unused memory called FREE SPACE.

When a process needs to enter memory:
1) The OS finds enough free space.
2) A partition is created specifically for that process.
3) The partition is sized according to the process's memory requirement.
4) The remaining memory continues to be free space.

THUS:
- The number of partition changes as processes enter and leave memory.
- Partitions can have different sizes.
- Partition sizes are determined when the process is loaded, rather than beforehand.

Dynamic Partitioning = Partitions are created on demand and sized to the process.
- Flexible, but new memory-management issues.

---------------------------------------------------------------------------------------------------------------------------------

2. How Memory Initially Fills Up:
Initially:
┌─────────────────────┐
│                     │
│     FREE SPACE      │
│                     │
│                     │
│                     │
└─────────────────────┘
- Porcess can initially be placed contiguously, one next to the another.

EX:
┌─────────────────────┐
│         P1          │
├─────────────────────┤
│         P2          │
├─────────────────────┤
│         P3          │
├─────────────────────┤
│                     │
│     FREE SPACE      │
│                     │
└─────────────────────┘
- Each process receives a partition sizes specifically for its memory requirements.

- An issues arises when processes are swapped out and replaced by processes of different sizes.
- If a smaller process replaces P1, part of P1's old region may remain unused.
Creating multiple separate regions of free memory, called HOLES.

EX:
┌─────────────────────┐
│       FREE          │
├─────────────────────┤
│         P2          │
├─────────────────────┤
│         P3          │
├─────────────────────┤
│       FREE          │
└─────────────────────┘

---------------------------------------------------------------------------------------------------------------------------------

3. External Fragmentation
- Occurs when free memory is divided into multiple separate holes between allocated partitions instead of existing as one
contiguous block.

EX:
┌──────────────┐
│      P1      │
├──────────────┤
│ FREE 300     │
├──────────────┤
│      P2      │
├──────────────┤
│ FREE 300     │
├──────────────┤
│      P3      │
├──────────────┤
│ FREE 300     │
└──────────────┘
- There are 900 units of free memory, but if a new process requires 800 units of contiguous memory, it cannot be loaded.

NOTE:
Total free memory can be large enough for a process, but the process still cannot be loaded because the free memory is fragmented into separate holes.

---------------------------------------------------------------------------------------------------------------------------------

4. Internal vs. External Fragmentation

| Type                       | Where is the wasted space?    | Cause                                           |
| -------------------------- | ----------------------------- | ----------------------------------------------- |
| **Internal fragmentation** | Inside an allocated partition | Partition is larger than the process needs      |
| **External fragmentation** | Outside allocated partitions  | Free memory becomes divided into separate holes |

Internal Fragmentation:
┌──────────────────┐
│     PROCESS      │
│                  │
│   UNUSED SPACE   │ ← waste inside partition
└──────────────────┘
- The process under utilizes the space provided.


External Fragmentation:
┌──────────────────┐
│     PROCESS      │
├──────────────────┤
│      HOLE        │ ← free space
├──────────────────┤
│     PROCESS      │
├──────────────────┤
│      HOLE        │ ← free space
└──────────────────┘
- Free space exists outside allocated partitions.

With dynamic partitioning, partitions are sized exactly according to the process.
- Thus internal fragmentation is eliminated.
- External fragmentation becomes a major issue.

---------------------------------------------------------------------------------------------------------------------------------

5. Tracking Free Space
Dynamic partitioning creates a new responsibility for the OS:
- The OS must keep track of where the free memory is.
Additionally a new manner to determine which free space should be used when a new process arrives.



One Method is a "linked list" of free spaces.
- Each element in the list represents one hole.

Each element stores:
1) Base address
- Where the free space begins.

2) Size
- How large the free space is.

3) Pointer
- Points to the next free-space entry in the linked list.

EX: Of a free list appearance
(100, 600)
      ↓
(850, 1300)
      ↓
(3000, 800)
      ↓
(4200, 600)

Meaining:
| Base Address | Size |
| -----------: | ---: |
|          100 |  600 |
|          850 | 1300 |
|         3000 |  800 |
|         4200 |  600 |

NOTE:
- Free list describes holes, not allocated paritions.

---------------------------------------------------------------------------------------------------------------------------------

6. Updating the Free List After Allocation

Suppose a process requires 500 units and is placed into:
Base = 100
Size = 600

The Remaining free space:
600 - 500 - 100

New free-space entry:
Base = 100 + 500 = 600
Size = 100

Thus:
Before:
(100, 600)

After:
(600, 100)


General Rule:
IF Process size < hole size
THEN:
new base = old base + process size
new size = old size - process size

IF Process size = hole size
- Then the hole is completely consumed and the entry is removed from the free list.

---------------------------------------------------------------------------------------------------------------------------------

7. Partition Placement Policies
- The OS needs a policy for deciding: "Which free hole should receive the next process?"

The four policies:
1) First Fit (FF)
2) Next Fit (NF)
3) Best Fit (BF)
4) Worst Fit (WF)
- These consider holes that are large enough to hold the process.

---------------------------------------------------------------------------------------------------------------------------------

8. First Fit (FF)
RULE - Start at the beginning of the free list and choose the first hole large enough.

EX:
Free List:
(100, 600)
(850, 1300)
(3000, 800)
(4200, 600)

Process requires: 800
Check:
- 600 -> too small
- 1300 -> large enough
THUS: Place process in the 1300-unit hole.



Advantages:
• Simple
• Fast
• Does not need to scan the entire list once a suitable hole is found.

Disadvantages:
- Beacuse the search always starts from the front, early portions of memory can becomes crowded with small fragments.

First Fit = First hole that works.

---------------------------------------------------------------------------------------------------------------------------------

9. Next Fit (NF)
Rule - Works like First Fit, except the search does not restart from the beginning.
INSTEAD: Start searching from where the previous search ended.


Purpose: This distributes allocations more evenly throughout memory.

Advantage
• More distributed allocation
• Helps address First Fit's tendency to crow the beginning of memory.

Next Fit → start where previous search stopped

---------------------------------------------------------------------------------------------------------------------------------

10. Best Fit (BF)
RULE - Search the entire free list and choose the smallest hole that is still large enough.

EX:
Suppose the available holes are:
600
1300
800
600

Process requires: 800
Best Fit Chooses: 800
- Since it is the smallest hole that can hold the process.


Disadvantages:
• Slower because the entire list must be examined.
• Can leave many small, potentially unusable holes.
- These economical choices can contribute to external fragementation.

Best Fit = Smallest hole that works.

---------------------------------------------------------------------------------------------------------------------------------

11. Worst Fit (WF)
RULE - Search the entire free list and choose the largest hole that can hold the process.

EX:
Holes:
600
1300
800
600

Process requires: 800
The Largest suitable hole is: 1300
Worst Fit Chooses: 1300


Advantages:
- The remaining space is relatively large, potentially making it useful for another process.

Disadvantages:
- Like Best Fit, it is slower because the entire list must be searched.

Worst Fit = Largest hole that works.

---------------------------------------------------------------------------------------------------------------------------------

12. Placement Policies - Quick Comparison

| Policy        | Chooses                                           | Search                       | Main characteristic         |
| ------------- | ------------------------------------------------- | ---------------------------- | --------------------------- |
| **First Fit** | First adequate hole                               | From beginning               | Simple and fast             |
| **Next Fit**  | First adequate hole after previous stopping point | From previous stopping point | More distributed            |
| **Best Fit**  | Smallest adequate hole                            | Entire list                  | Can create many small holes |
| **Worst Fit** | Largest adequate hole                             | Entire list                  | Leaves larger remainders    |

FIRST → first one that works
NEXT  → next one from where you stopped
BEST  → smallest one that works
WORST → largest one that works

---------------------------------------------------------------------------------------------------------------------------------

13. Worked Example - First Fit
Given memory address range:
0 – 4999

Processes:
P1 = 500
P2 = 800
P3 = 750
P4 = 1200
P5 = 900

Initial Free List:
(100, 600)
(850, 1300)
(3000, 800)
(4200, 600)

- These starting conditions are used for both First Fit and Best Fit

13.1 Place P2 Using First Fit
P2 requires: 800

Scan from the beginning: 600 -> too small, 1300 -> Large enough
THUS: P2 -> Base address 850
The remaining portion of the 1300-unit hole is: 1300 - 800 = 500
New Base: 850 + 800 = 1650

Updated Free List:
(100, 600)
(1650, 500)
(3000, 800)
(4200, 600)



13.2 Place P1 Using First Fit
P1 requires: 500

Start at the beginning: 600 -> Large enough
THUS: P1 -> bae address 100
Remaining Spcae: 600 - 500 = 100
New Base: 100 + 500 = 600

Updated Free List:
(600, 100)
(1650, 500)
(3000, 800)
(4200, 600)
- The 100-unit hole is an example of fragments difficult to use.

---------------------------------------------------------------------------------------------------------------------------------

14. Worked Example - Best Fit
- Starting with the original free list:
(100, 600)
(850, 1300)
(3000, 800)
(4200, 600)


14.1 Place P2 Using Best Fit
P2 requires: 800

Check the entire list:
600   → too small
1300  → works
800   → works
600   → too small
- Best Fit chooses the smallest suitable hold: 800
THUS: P2 -> Base address 3000

- Since the hole is exactly 800 units, the entire hole is consumed.
Disappearing from the free list:
(100, 600)
(850, 1300)
(4200, 600)



14.2 Place P1 Using Best Fit
P1 requires: 500

Remaining holes:
600
1300
600

The smallest suitable hole is 600.
- Since there are two 600-unit holes the first begins at 100.
THUS: P1 -> Base address 100
Remaining space: 600 - 500 = 100
Updated list:
(600, 100)
(850, 1300)
(4200, 600)

---------------------------------------------------------------------------------------------------------------------------------

15. First Fit vs. Best Fir Example

First Fit for P2:
P2 = 800

600 → no
1300 → YES

P2 → address 850




Best Fit for P2:
P2 = 800

600 → no
1300 → yes
800 → yes ← smallest suitable
600 → no

P2 → address 3000

- First Fit can make its decision after examining only part of the list.
- Best Firt must examine the entire list to determine which suitable hole is smallest.

---------------------------------------------------------------------------------------------------------------------------------

16. Advantages and Disadvantages of Dynamic Partitioning

Advantages:
• Flexibility
Partitions are sized according to the processes that actually arrive.
- There are no fixed maximum partition size selected in advance.



Disadvantages:
1) More complicated management
- The OS must maintain the free-space structure during:
• Allocation
• Deallocation
• Swapping

2) External Fragmentation
- Free memory can become scattered into holes.
- A system can have enough total free memory, yet unable to satisfy a request since no single hole is large enough.
- The severity of fragmentation can also vary depending on the placement policy.

---------------------------------------------------------------------------------------------------------------------------------

17. The Addressing Problem
- Dynamic partitioning creates another important question:
"How does a program specify the memory address it wants to access?"

A possibility would be to use an "Absolute Address."
EX: "Give me the value at address 400."
- Consequently creating another issue.

---------------------------------------------------------------------------------------------------------------------------------

18. Why Absolute Addresses Do Not Work
- An absolute address refers to an actual physical location in main memory.
The program CANNOT know in advance where its partition will be placed.

EX, Today:
P1 → physical address 1000

Later, after processes have entered and left memory:
P1 → physical address 3000

- If P1's instructions directly referred to physical address 1000, moving P1 would cause those instructions to access the wrong location.

THUS: Absolute addressing is not partical for dynamicaly partitioned memory.
- Reducing portability under static partitioning, since the program would depend on a particular physical memory layout.

---------------------------------------------------------------------------------------------------------------------------------

19. Logical Addresses
- Instead of providing the program an absolute physical address, the program uses an offset relative to the beginning of
its own partition.
Called a "logical address"

EX:
Logical address = 16
- Signifies: Access the memory location 16 units from the beginning of my partition.
- The program does NOT need to know where its partition physically begins.

---------------------------------------------------------------------------------------------------------------------------------

20. Logical Address Space vs. Physical Address Space

Logical Address Space
- The range of logical addresses that a process can generate.

Begins at: 0
- And extends to the size of the process's data.



Physical Address Space
- The range of actual absolute addresses in main memory that the process can access.

Relationship:
PROGRAM
   │
   │ logical address
   ▼
SYSTEM TRANSLATION
   │
   │ physical address
   ▼
MAIN MEMORY

- The program generates the logical address.
- The system converts it into the corresponding physical address.

---------------------------------------------------------------------------------------------------------------------------------

21. Base and Limit
- The OS maintains two important values for the process's partition:

Base - The physical address where the process's partition begins.
Limit- The boundary that determines whether the process's requested address is legal.

These serve TWO purposes:
1) Address Translation
2) Memory Protection

---------------------------------------------------------------------------------------------------------------------------------

22. Address Translation
- Basic translation is: Physical Address = Base Addres + Logical Address
Then the system checks whether the resulting address is within the process's permitted limit.

Supose:
Base = 100
Logical Address = 16

Calculate:
Physicall address = 100 + 16 = 116

THUS, the process accessses: Physical address 116

MEMORIZE:
Physical Address = Base + Logical Address

---------------------------------------------------------------------------------------------------------------------------------

23. Translation + Protection
- Base and Limit information helps with "memory protection."

The system:
1) Takes the logical address.
2) Adds the base.
3) Produces the physicall address.
4) Checks whether the requested location is within the process's allowed partition.


THUS:
Logical address
       ↓
   + Base
       ↓
Physical address
       ↓
Check against limit
       ↓
Legal? → Access memory
Illegal? → Reject access

- Translation and protection work together usin the same stored base and limit values.

---------------------------------------------------------------------------------------------------------------------------------

24. Relocation
- Since programs use relative/logical addresses, they DON'T depend on a particular physical locatio.
Allowing for "relocation".

Suppose P1 originally has:
Base = 1000

P1 generates:
Logical address = 50

Physical address:
1000 + 50 = 1050

Later, P1 is moved to:
Base = 3000

The program still generates:
Logical address = 50

The system now calculates:
3000 + 50 = 3050

- The program itself does not need to change.

---------------------------------------------------------------------------------------------------------------------------------

25. Why Relocation Matters
- Useful when processes are swapped out and latter brought back into memory.

Suppose P1 originally occupied one physical location.
After being swapped out:
• Another process may occupy that location.
• The free-space layout may have changed.
• A different hole may now be available.

With Logical addressing, P1 can simply be placed somewhere else.
- The OS records the new base address, and address translation continues to work.

NOTE: Logical addressing separates the program from its physical memory location.
Separation allows for:
• Dynamic partitioning
• Swapping
• Relocation

---------------------------------------------------------------------------------------------------------------------------------

26. Full Concept Connection:
Dynamic Partitioning
        ↓
Partitions have variable sizes
        ↓
Processes enter/leave memory
        ↓
Free memory becomes scattered
        ↓
External Fragmentation
        ↓
OS must track free spaces
        ↓
Free-space linked list
        ↓
OS needs placement policy
        ↓
FF / NF / BF / WF
        ↓
Process location can change
        ↓
Absolute addresses won't work
        ↓
Logical addresses
        ↓
Base + Logical Address
        ↓
Physical Address
        ↓
Relocation becomes possible

---------------------------------------------------------------------------------------------------------------------------------

27. Key Terms:
| Term                         | Meaning                                                       |
| ---------------------------- | ------------------------------------------------------------- |
| **Dynamic partitioning**     | Creates partitions on demand according to process size        |
| **Variable-sized partition** | Partition whose size is based on the process being loaded     |
| **Free space**               | Unallocated memory                                            |
| **Hole**                     | A separate region of free memory between allocated partitions |
| **External fragmentation**   | Free memory divided into separate holes                       |
| **Internal fragmentation**   | Unused space inside an allocated partition                    |
| **Free list**                | Linked list used to track free memory                         |
| **Base address**             | Starting address of a free space or process partition         |
| **First Fit**                | First adequate hole                                           |
| **Next Fit**                 | First adequate hole starting from previous stopping point     |
| **Best Fit**                 | Smallest adequate hole                                        |
| **Worst Fit**                | Largest adequate hole                                         |
| **Logical address**          | Relative address generated by a process                       |
| **Physical address**         | Actual address in main memory                                 |
| **Logical address space**    | Range of logical addresses a process can generate             |
| **Physical address space**   | Range of actual memory addresses available to the process     |
| **Relocation**               | Moving a process's partition to another physical location     |
| **Limit**                    | Boundary used to determine whether an address is valid        |

---------------------------------------------------------------------------------------------------------------------------------

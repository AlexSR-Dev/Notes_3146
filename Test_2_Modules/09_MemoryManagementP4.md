09 - Memory Management

Part 4: Pages


1. The Problem: Externla Fragmentation
- With the introduction to dynamic, variable-sized partitioning.
An occuring issue was: External Fragmentation.
- As processes are allocated and removed from memory, free memory becomes divided into separate holes.

┌───────────────┐
│      P1       │
├───────────────┤
│   FREE SPACE  │
├───────────────┤
│      P2       │
├───────────────┤
│   FREE SPACE  │
├───────────────┤
│      P3       │
├───────────────┤
│   FREE SPACE  │
└───────────────┘

- External Fragmentation reduces memory utilization because some free memory cannot be used when a process requires
one contiguous region.

---------------------------------------------------------------------------------------------------------------------------------

2. Two Possible Solutions

Approach 1 - Compaction
- The method of moving existing partitions so that the free spaces are combined into one larger contiguous space.

Apporach 2 - Non-Contiguous Allocation
- Allows a process's memory footprint to be divided into multiple pieces and place those pieces into different free spaces.

---------------------------------------------------------------------------------------------------------------------------------

3. Compaction
- Moving allocated partitions so that they become adjacent to one another.
GOAL: To combine the separate holes into one larger contiguous free space.

Before Compaction:
┌──────────────┐
│      P1      │
├──────────────┤
│     FREE     │
├──────────────┤
│      P2      │
├──────────────┤
│     FREE     │
├──────────────┤
│      P3      │
└──────────────┘

After Compaction:
┌──────────────┐
│      P1      │
├──────────────┤
│      P2      │
├──────────────┤
│      P3      │
├──────────────┤
│              │
│  FREE SPACE  │
│              │
└──────────────┘

NOTE: Compaction does NOT CREATE additional memory. Just changes the arrangement of memory to become contiguous.

---------------------------------------------------------------------------------------------------------------------------------

4. Compaction Requires Relocation
- Partitions have to be move from its physical location.

THUS, the system needs partition relocation.
REASON: Due to relative/logical addressing.
- As the OS can update the process's physical location while hte process continues using the same logical addresses.


Disadvantages:
- Compaction can be expensive.

As the OS must repeatedly:
1) Move partitions.
2) Change their physical locations.
3) Maintain the appropriate address information.
4) Continue doing this as fragementation develops.

---------------------------------------------------------------------------------------------------------------------------------

5. The Alternative: Non-Contiguous Allocation
- Changing the requirement that a process occupy one contiguous partition.

IDEA: Split a process's memory footprint into multiple pieces.
Then place those pieces into different free regions of memory.

Suppose:
P1 = 90 units

Available Holes:
50 units
20 units
40 units

- No single hole is large enough.
But: 50 + 40 = 90

- The pricess is potentially divided into:
Part 1 = 50
Part 2 = 40

- And placed into two separate locations. Allowing for non-contiguous memory.

---------------------------------------------------------------------------------------------------------------------------------

6. The New Issue
- Previous in address translation in dynamic partitioning.
The OS maintained: Base and Limit, for each process partitions.

There is no longer a single base address or limit instead (multiple):
Process
   │
   ├── Part 1 → Base + Limit
   │
   ├── Part 2 → Base + Limit
   │
   └── Part 3 → Base + Limit

- THUS a more sophisticated memory-management mechanism is needed: (Paging).

---------------------------------------------------------------------------------------------------------------------------------

7. Paging
- Allows a process to be divided into smaller pieces and allow those pieces to be mapped to different locations
in physical memory.

Structure:
Logical Memory
      ↓
   Pages
      ↓
Mapped to
      ↓
Physical Memory
      ↓
Page Frames


- Page -> Block of process's logical memory.
- Page Frame -> Equal-sized block of physical memory.




8. Page Frames
- Dividing physical memory into equal-sized blocks.
- The new blocks itself are called: Page Frames.

EX:
Physical Memory

┌─────────────┐
│ Page Frame 0│
├─────────────┤
│ Page Frame 1│
├─────────────┤
│ Page Frame 2│
├─────────────┤
│ Page Frame 3│
├─────────────┤
│ Page Frame 4│
├─────────────┤
│     ...     │
└─────────────┘

- Every page frame has the same size.




9. Pages
- The process's logical memory

Physical memory → Page Frames
Logical process memory → Pages

NOTE: Pages and page frames are the same size.




10. Mapping Pages to Page Frames
- When a process enter main memory:
1) The process is divided into pages.
2) Physical memory is already divided into page frames.
3) Each page is mapped to a free page frame.

EX:
Process P1
Page 0 ─────────→ Frame 3
Page 1 ─────────→ Frame 7
Page 2 ─────────→ Frame 1

- Pages do not have to be placed next to one another.




12. Contiguous vs. Non-Contiguous Paging
NOTE:
- Pages of a process could end up being contiguous, but they do not have to be.


Contiguous:
P1 Page 0 → Frame 0
P1 Page 1 → Frame 1
P1 Page 2 → Frame 2
- Pages happens to be next to each other.


Non-Contiguous
P1 Page 0 → Frame 2
P1 Page 1 → Frame 8
P1 Page 2 → Frame 5
- Pages are scattered across memory.

Which one ocuuers depends on:
- The "state" of main memory when the process arrives.
- The "policy" used to allocate the page frames.

---------------------------------------------------------------------------------------------------------------------------------

13. EX of Paging Multiple Processes

Physical memory has several equal-sized frames.
- Processes are divided into pages.

Process 1
P1 Page 0
P1 Page 1
P1 Page 2

Process 2
P2 Page 0
P2 Page 1
P2 Page 2
P2 Page 3

The OS can map them to available frames:
Frame 0 → P1 Page 0
Frame 1 → P1 Page 1
Frame 2 → P2 Page 0
Frame 3 → P1 Page 2
Frame 4 → P2 Page 1
Frame 5 → P2 Page 2
Frame 6 → P2 Page 3

---------------------------------------------------------------------------------------------------------------------------------

14. Page Numbers and Page-Frame Numbers

- Both Numbering systems begin at "ZERO".
No requirement that the page number and page-frame number have to match.

---------------------------------------------------------------------------------------------------------------------------------

15. Page Tables
- Pages can be mapped to different physical frames, THUS, the OS needs to keep track of those mappings using: PAGE TABLES.

Page Table - Records the relationship between: Logical Page -> Physical Page Frame
EX:
Logical Page 0 → Frame 1
Logical Page 1 → Frame 4
Logical Page 2 → Frame 10
Logical Page 3 → Frame 8

The page table conceptually can appear as:
| Logical Page | Physical Frame |
| -----------: | -------------: |
|            0 |              1 |
|            1 |              4 |
|            2 |             10 |
|            3 |              8 |




16. One Page Table Per Process
- Each process has its own page table.
Since different processes can use the same logical page numbers.

EX:
Process A:
Page 0 → Frame 1

Process B:
Page 0 → Frame 9

- Both processes have a Page 0, but are not the same memory.
- The page table associated with each process distinguishes the mappings




17. Example Page Table
- Suppose a process has four logical pages:
Page 0
Page 1
Page 2
Page 3

- Its page table thus has four entries.
- Suppose the mappings are:
Page 0 → Frame 1
Page 1 → Frame 4
Page 2 → Frame 10
Page 3 → Frame 8

NOTE: Number of page-table entries = number of pages belonging to the process.

---------------------------------------------------------------------------------------------------------------------------------

18. Tracking Free Page Frames
- The OS maintains the physical page frames available through a: Linked list of free page frames




19. Allocating a Process with Paging
- Suppose a process requires: n pages

The OS must:
Step 1:
Find: n free page frames

Step 2:
Map the process's pages to those frames.

Step 3:
Update the process's page table
EX:
Process requires 4 pages
           ↓
Find 4 free frames
           ↓
Map:
Page 0 → Frame 2
Page 1 → Frame 7
Page 2 → Frame 4
Page 3 → Frame 9
           ↓
Update page table




20. Paging and Internal Fragmentation
- Paging does not completely eliminate internal fragmentation.
Because page frames have a fixed size.
- As a process's memory footprint might be an exact multiple of the page size.


EX:
Page size: 4 KB
Process size: 15 KB

How many pages are required? (The process needs 4 page frames)
Page 1 → 4 KB
Page 2 → 4 KB
Page 3 → 4 KB
Page 4 → remaining 3 KB

But the fourth frame is:
4 KB available
3 KB used
1 KB unused

THUS: 1 KB = Internal Fragmentation




21. Where Internal Fragmentation Occurs in Paging
- Internal fragmentation can occur in the last page frame of a process.
Because the process's memory footprint may not be an exact multiple of the page size. (In 20.)

THUS: Paging can still have internal fragmentation, but it is generally limited to unused space in the final page/frame of a process.

---------------------------------------------------------------------------------------------------------------------------------

22. Paging Difference From Static Partitioning
- Both divides physical memory into equal-sized regions.

Static partitioning
A partition needs to be large enough to contain the entire process.

Paging
The process itself is divided into multiple pages.

Static partitioning:

┌──────────────────────────┐
│        PROCESS           │
└──────────────────────────┘
       One partition


Paging:

┌──────┐
│Page 0│
├──────┤
│Page 1│
├──────┤
│Page 2│
└──────┘
     ↓
Can be placed into
different frames




23. Memory Management Unit (MMU)
- In charge of:
: Mapping pages to page frames.
: Address translation.
: Related memory-management functions.

---------------------------------------------------------------------------------------------------------------------------------

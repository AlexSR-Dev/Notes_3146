14 - Virtual Memory

1. Goal of Virtual Memory

The goal is to keep as many processes in memory as possible at the same time to support multiprogramming.

Why?
: More processes in memory → more opportunities to schedule processes.
: However, processes can have very large address spaces.
: Multiprogramming means multiple address spaces must coexist.
: Physical memory may not be large enough to contain all pages from all processes.

Problem: A process may require more memory than is physically available.
Solution → Virtual Memory.

---------------------------------------------------------------------------------------------------------------------------------

2. Virtual Memory

Virtual memory is a technique that allows a process to execute even when the process is not completely loaded into physical memory.

Main advantage
- A program's logical address space can be larger than physical memory.

Example from the presentation
Physical/main memory:
: 32 KB
: Physical address = 15 bits

Logical address space:
: 64 KB
: Logical address = 16 bits

Page size:
: 4 KB

Therefore:
Physical page frames:
 32KB / 4KB = 8 page frames

Logical pages:
 64KB / 4KB = 16 pages}

So the process has:
: 16 logical pages
: But only 8 physical page frames

This means the entire process cannot be in physical memory at once.


Address breakdown

Because each page is 4 KB:

 4KB = 2^{12}

Therefore:

12-bit offset
Remaining bits identify the page.

For the 16-bit logical address:

 16-12=4 

→ 4-bit logical page number

For the 15-bit physical address:

 15-12=3

→ 3-bit physical page-frame number.

---------------------------------------------------------------------------------------------------------------------------------

3. What Virtual Memory Provides

Virtual memory creates an abstraction of main memory.

It separates:
Logical memory
→ what the program/user sees

from

Physical memory
→ the actual RAM available.

The user/program therefore gets the illusion of having more memory than physically exists.
This also frees programmers from having to manually manage the physical-memory limitation.

---------------------------------------------------------------------------------------------------------------------------------

4. Why Doesn't the Entire Program Need to Be in Memory?

Instructions must be in physical memory before they can execute.

At first, this appears to mean:
- Entire program → must fit in physical memory.

However, real programs often do not need every part of the program at the same time.

Examples:
: Error-handling code may rarely execute.
: Arrays, lists, and tables may allocate more memory than they actually use.
: Some program features/options may rarely be used.
: Even if the entire program is eventually needed, it may not all be needed simultaneously.

Therefore, keeping the entire program in physical memory can waste valuable memory.

---------------------------------------------------------------------------------------------------------------------------------

5. Virtual Memory — Big Picture

Virtual memory allows the program's pages to be divided between:
Physical Memory (RAM)
and
Disk / Swap Space


The page table keeps track of where the logical pages are located.
Conceptually:
Logical / Virtual Memory
        ↓
    Page Table
        ↓
 ┌───────────────┐
 │ Physical RAM  │
 └───────────────┘
        ↕
 ┌───────────────┐
 │ Disk / Swap   │
 └───────────────┘

- Pages do not have to remain permanently in RAM. Pages can be moved between RAM and disk as needed.

---------------------------------------------------------------------------------------------------------------------------------

6. Swap Space
Swap space is an area of the disk reserved for moving pages in and out of physical memory.

If a page cannot currently fit in RAM, it can reside in swap space until it is needed.
This is what allows virtual memory to support a logical address space larger than physical memory.

---------------------------------------------------------------------------------------------------------------------------------

7. Demand Paging

Demand paging means:
- Load a page into physical memory only when it is needed.

The page is loaded on its first access.

Therefore:
: Pages that are never used → never need to be loaded.
: Pages are brought into RAM only when the process actually requests them.
This reduces unnecessary use of physical memory.

---------------------------------------------------------------------------------------------------------------------------------

8. Valid/Invalid Bit

Each page-table entry contains a valid/invalid bit.

Valid = 1
The page is:
: Legal for the process, and
: Currently in physical memory.


Valid = 0
The page is either:
: Not a valid/legal address, or
: A valid page that is currently not in physical memory.

If the requested page is not in memory, accessing it causes a:

Page Fault
The operating system then handles the page fault.

---------------------------------------------------------------------------------------------------------------------------------

9. Page Fault — What Happens?
Suppose a process requests a page.

Case 1: Page is in memory
Page requested
      ↓
Valid = 1
      ↓
Page is in RAM
      ↓
Address translation continues

- No page fault occurs.



Case 2: Page is not in memory
Page requested
      ↓
Valid = 0
      ↓
Page Fault
      ↓
Operating System handles it

- The MMU generates the page fault, and the page-fault handler takes control.

---------------------------------------------------------------------------------------------------------------------------------

10. Page Fault Handler

When a page fault occurs, the operating system first determines whether the requested address is actually valid.

Step 1 — Check the address
The OS checks the process's memory-allocation information.

Step 2 — Check access permissions
The OS determines whether the requested type of access is valid.

Step 3 — Invalid access
If the address/access is invalid:
→ Send a SEGV signal to the process.

Step 4 — Valid page but not in memory
If the page is valid but simply isn't in RAM:
→ Retrieve the page from swap space/disk.

---------------------------------------------------------------------------------------------------------------------------------

11. Page Fault — Completing the Process

Once the page is retrieved from disk:
1) Disk I/O fetches the page.
2) The OS updates the page table to indicate that the page is present.
3) The OS updates the PFN (Page Frame Number) in the page-table entry.
4) The instruction is retried.
5) This may initially cause a TLB miss.
6) The TLB is updated with the page-table information.
7) The instruction is retried again.
8) Eventually, the translation results in a TLB hit.


Simplified sequence
Page Fault
    ↓
Find page on disk
    ↓
Read page into RAM
    ↓
Update Page Table
    ↓
Retry instruction
    ↓
TLB updated if necessary
    ↓
Retry instruction
    ↓
Continue execution

---------------------------------------------------------------------------------------------------------------------------------

12. Demand Paging When a Process Starts
The presentation shows that the OS does not immediately load the entire executable.

Instead:
1) Open the executable file.
2) Set up the memory map:
: Stack
: Text/code
: Data
3) Do not load everything immediately.
4) Load the first required page.
5) Allocate the initial stack page.
6) Begin running the process.

Additional pages are loaded when they are actually needed.

---------------------------------------------------------------------------------------------------------------------------------

13. Cost of a Page Fault

Page faults are expensive because accessing disk is much slower than accessing RAM.

The presentation gives approximate values:
: Page-fault handling: ~1–100 μs
: Disk seek/read: ~8 ms
: Memory access: ~200 ns

Therefore:
- Avoid page faults whenever possible.

The presentation states that with a page-fault rate of p = 0.001, performance can degrade by around a factor of 40.
To keep performance degradation below 10%, the presentation gives a required rate of approximately:
$$ p < 0.0000025

That corresponds to roughly one page fault per 399,990 memory accesses.

---------------------------------------------------------------------------------------------------------------------------------

14. Page Replacement Policies

Once demand paging is used, another problem appears:
- What happens when a page is needed but all physical page frames are already occupied?

The OS must choose a page to replace/evict.

The presentation identifies several policy questions:
1) Which page frame should receive a new page?
2) Which page should be removed if there are no free frames?
3) Which page-table entries should be placed in the TLB?

---------------------------------------------------------------------------------------------------------------------------------

15. Choosing a Physical Page Frame

If there is an empty physical frame, choosing one is simple.
All page frames have the same size.

Therefore:
- Any empty page frame can be used.

The difficult situation occurs when:
- No empty frame exists.

Then the OS must choose a victim page to evict.

---------------------------------------------------------------------------------------------------------------------------------

16. Evicting a Page

When a page is evicted:
1) Choose the victim page.
2) If the page has been modified, write it back to disk.
3) The physical frame becomes available.
4) Load the requested page into that frame.

The page only needs to be written back if it was modified since being loaded.

---------------------------------------------------------------------------------------------------------------------------------

17. Page Replacement Example

The presentation uses:
3 physical page frames

and the following page-reference sequence:
7, 0, 1, 2, 0, 3, 0, 4, 2, 3

This same sequence is used to demonstrate different replacement policies.

---------------------------------------------------------------------------------------------------------------------------------

18. Optimal Page Replacement

The ideal strategy would be:
- Evict the page that will not be needed for the longest time in the future.

This requires knowing the process's future memory-reference pattern.

Therefore:
Optimal Page Replacement = best theoretical choice, but impractical in real systems because the future is unknown.

---------------------------------------------------------------------------------------------------------------------------------

19. Optimal Replacement — Step-by-Step Example

Reference sequence:
7, 0, 1, 2, 0, 3, 0, 4, 2, 3

Three frames are available.

Request 7
[ 7 ] [ - ] [ - ]
- 7 is loaded.


Request 0
[ 7 ] [ 0 ] [ - ]
- 0 is loaded


Request 1
[ 7 ] [ 0 ] [ 1 ]
- 1 is loaded


Request 2
Memory is full:
[ 7 ] [ 0 ] [ 1 ]

- Look at the future:
2, 0, 3, 0, 4, 2, 3
: 0 → needed soon
: 1 → never used again
: 7 → never used again

Therefore, either 7 or 1 could be removed.
The example chooses 7:
[ 2 ] [ 0 ] [ 1 ]

---------------------------------------------------------------------------------------------------------------------------------

20. Optimal Replacement — Hits

Next request:
0

0 is already in memory.

Therefore:
HIT
[ 2 ] [ 0 ] [ 1 ]
- No replacement is required.

---------------------------------------------------------------------------------------------------------------------------------

21. Optimal Replacement — Request 3

Next:
3

Current memory:
[ 2 ] [ 0 ] [ 1 ]

Look at the remaining references:
3, 0, 4, 2, 3

: 0 → needed soon
: 2 → needed later
: 1 → never used again

Therefore:
1 is replaced by 3
[ 2 ] [ 0 ] [ 3 ]

---------------------------------------------------------------------------------------------------------------------------------

22. Optimal Replacement — Request 0

Next:
0

0 is already present:
[ 2 ] [ 0 ] [ 3 ]

Therefore:
HIT
No replacement occurs.

---------------------------------------------------------------------------------------------------------------------------------

23. Optimal Replacement — Request 4

Next:
4

Current:
[ 2 ] [ 0 ] [ 3 ]

Look ahead:
4, 2, 3
: 2 → needed soon
: 3 → needed after 2
: 0 → never used again

Therefore:
0 is replaced by 4
[ 2 ] [ 4 ] [ 3 ]

---------------------------------------------------------------------------------------------------------------------------------

24. Optimal Replacement — Final Requests

Next:
2

Already present:
[ 2 ] [ 4 ] [ 3 ]

→ HIT

Next:
3

Already present:
[ 2 ] [ 4 ] [ 3 ]

→ HIT

Therefore, the final physical memory contains:
[ 2 ] [ 4 ] [ 3 ]

The presentation labels optimal replacement as:
Optimal, but impractical!
because it depends on knowing future memory references.

---------------------------------------------------------------------------------------------------------------------------------

25. FIFO Page Replacement

FIFO provides a much simpler strategy:
- Evict the page that has been in main memory the longest.

FIFO means:
First In → First Out

It does not consider whether a page is frequently used.

---------------------------------------------------------------------------------------------------------------------------------

26. FIFO — Key Example

Start with:
[ 7 ] [ 0 ] [ 1 ]

Request:
2

- 7 entered memory first.
Therefore:
7 is evicted → 2 enters
[ 2 ] [ 0 ] [ 1 ]

Request:
0

0 is already present:
HIT

The presentation emphasizes that FIFO is:
: Simple
: Fair
: But does not really consider page usage.

---------------------------------------------------------------------------------------------------------------------------------

27. Second Chance Algorithm

Second Chance is a modification of FIFO.

It maintains:
: A FIFO list
: A reference bit for every page

When replacement is necessary:
Reference bit = 0
→ Evict the page.

Reference bit = 1
→ Give it a second chance:
1) Set reference bit to 0.
2) Move the page to the end of the FIFO list.
3) Check the next oldest page.

Easy way to remember:
Oldest page
     ↓
Reference = 0? ── YES → Replace
     │
     NO
     ↓
Reset to 0
     ↓
Move to back
     ↓
Check next oldest

---------------------------------------------------------------------------------------------------------------------------------

28. Second Chance — Example

At one point, suppose the list is:
Oldest                         Newest
   ↓                              ↓
 [7] → [0] → [1]


If 7 has:
R = 0
- then 7 is immediately replaced.


But if the oldest page has:
R = 1
- then it is not replaced.

Instead:
R: 1 → 0

and the page moves to the end:
Before:
[0] → [1] → [2]

After giving 0 a second chance:
[1] → [2] → [0]

Now the OS examines 1, which has become the oldest page.
This is how Second Chance prevents a recently referenced page from automatically being removed simply because it is old.

---------------------------------------------------------------------------------------------------------------------------------

29. Least Recently Used (LRU)
Another option is Least Recently Used (LRU).

LRU evicts:
- The page that has not been used recently.

Unlike FIFO, LRU does not care when the page originally entered memory.
Instead, it looks at when the page was last referenced.

---------------------------------------------------------------------------------------------------------------------------------

30. LRU — Example

Start:
[ 7 ] [ 0 ] [ 1 ]

Request:
2

Determine which page was used least recently.
→ 7

Therefore:
7 → replaced by 2

Memory:
[ 2 ] [ 0 ] [ 1 ]

Next request:
0

0 is already present, so it becomes the most recently used page.
Next request:
3

Compare recent usage:
: 0 → just used
: 2 → used before 0
: 1 → used least recently

Therefore:
1 → replaced by 3

Memory:
[ 2 ] [ 0 ] [ 3 ]

---------------------------------------------------------------------------------------------------------------------------------

31. LRU — Continued Example

Next request:
0

0 is already present → HIT

Now request:
4

Recent usage indicates:
: 0 → most recent
: 3 → used before 0
: 2 → least recently used


Therefore:
2 → replaced by 4


Memory:
[ 4 ] [ 0 ] [ 3 ]


Then:
2 is requested again.
2 is no longer in memory, so another replacement is necessary.


The least recently used page is 3:
3 → replaced by 2

Finally, 3 is requested.

Now 0 is the least recently used page, so:
0 → replaced by 3

---------------------------------------------------------------------------------------------------------------------------------

32. Locality of Reference
LRU works reasonably well when a program has locality of reference.

Locality of reference means:
- If a program accesses a particular memory location/page, it is likely to access the same page or nearby pages again in the near future.

Therefore, recently used pages are often good candidates to keep in memory.
This is why LRU can work well for programs with strong locality.

---------------------------------------------------------------------------------------------------------------------------------

33. Page Replacement Comparison
| Algorithm         | What does it look at?      | Replacement rule                             |
| ----------------- | -------------------------- | -------------------------------------------- |
| **Optimal**       | Future references          | Remove page needed farthest in the future    |
| **FIFO**          | Time in memory             | Remove oldest page                           |
| **Second Chance** | FIFO order + reference bit | Remove oldest page only if reference bit = 0 |
| **LRU**           | Recent usage               | Remove least recently used page              |


Exam recognition

Optimal
"Which page will be needed last/farthest in the future?"

FIFO
"Which page has been here the longest?"

Second Chance
"Which page is oldest, and has it been referenced?"

LRU
"Which page has gone the longest without being used?"

---------------------------------------------------------------------------------------------------------------------------------

34. Overall Process to Remember
The presentation can be reduced to this overall chain:

Process has a large logical address space
                ↓
Physical RAM is limited
                ↓
        VIRTUAL MEMORY
                ↓
Only needed pages are loaded
                ↓
        DEMAND PAGING
                ↓
Requested page isn't in RAM
                ↓
          PAGE FAULT
                ↓
OS retrieves page from disk
                ↓
If RAM is full:
        PAGE REPLACEMENT
                ↓
Choose victim page
                ↓
Optimal / FIFO / Second Chance / LRU

The central idea of the presentation: virtual memory allows programs to operate with address spaces larger than physical RAM by keeping only the pages currently needed in memory and moving pages between RAM and disk as necessary.
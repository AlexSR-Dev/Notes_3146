11 - Memory Management

Part 5b: TLB, Demand Paging & Virtual Memory

1. Page Table Entry Structure

A page table entry can contain more than just the page frame number.

Possible information includes:
- Page frame number — identifies the physical frame.
- Present/absent bit — indicates whether the mapping is valid.
- Protection bits — control read, write, and execute permissions.
- Modified bit — indicates whether the page has been changed.
- Referenced bit — indicates whether the page has been accessed.
- Caching bit — controls whether the page can be cached.

The exact structure depends on the operating system.

---------------------------------------------------------------------------------------------------------------------------------

2. Page Table Performance Problem

The page table is stored in main memory.

This creates an issue: every memory request may require:

1) Accessing the page table to find the frame.
2) Accessing the actual memory location.

Therefore, one memory request can require two memory accesses.

---------------------------------------------------------------------------------------------------------------------------------

3. Translation Lookaside Buffer (TLB)

The Translation Lookaside Buffer (TLB) is a small, fast cache containing frequently used page → frame mappings.

Purpose
The TLB reduces the need to repeatedly access the page table in main memory.

Think of it as:
Page table = complete list

TLB = small, fast list of commonly used mappings

---------------------------------------------------------------------------------------------------------------------------------

4. TLB Hit

A TLB hit occurs when the requested page's mapping is already in the TLB.

Process

Logical address
→ Check TLB
→ Page-to-frame mapping found
→ Use frame number
→ Create physical address

The system does not need to search the page table for that mapping.

---------------------------------------------------------------------------------------------------------------------------------

5. TLB Miss

A TLB miss occurs when the requested page is not in the TLB.

Process

Logical address
→ Check TLB
→ Mapping not found
→ Check page table
→ Find frame number
→ Create physical address

The offset remains unchanged during the translation.

Remember
TLB miss does NOT automatically mean page fault.
A TLB miss simply means the mapping wasn't found in the TLB.

---------------------------------------------------------------------------------------------------------------------------------

6. Why Demand Paging Is Needed

Previously, we assumed that all pages belonging to a process had to be in main memory before the process could execute.

This creates two limitations:

- Fewer processes can fit in memory.
- Process data size is limited by physical memory.

However, a process does not necessarily use all of its pages at the same time.

---------------------------------------------------------------------------------------------------------------------------------

7. Demand Paging

Demand paging allows only the pages currently needed by a process to be loaded into main memory.

Other pages can remain outside main memory until they are needed.

Basic idea

Instead of:
Load everything → Run process

Use:
Load what is needed → Run → Load additional pages when needed

---------------------------------------------------------------------------------------------------------------------------------

8. Page Fault

A page fault occurs when a process requests a page that is not currently in main memory.

Process
1) Process requests a page.
2) System checks whether the page is in memory.
3) Page is missing → page fault.
4) System loads the missing page into memory.
5) The mapping may be added to the TLB.
6) The instruction that caused the fault is restarted.
7) The memory access can now succeed.


Important distinction
| Situation      | Meaning                             |
| -------------- | ----------------------------------- |
| **TLB hit**    | Mapping found in TLB                |
| **TLB miss**   | Mapping not found in TLB            |
| **Page fault** | Requested page isn't in main memory |

---------------------------------------------------------------------------------------------------------------------------------

9. Virtual Memory

Demand paging allows a process to have a logical address space larger than physical main memory.

This is called virtual memory.
Example
Suppose:
- Physical memory = 32 KB
- Process logical memory = 64 KB

The process is larger than physical memory.
This is possible because only a subset of its pages needs to be in physical memory at one time.

---------------------------------------------------------------------------------------------------------------------------------

10. Logical Pages vs. Physical Frames

With virtual memory, there can be:
More logical pages than physical page frames.

Therefore:
- Logical page number can require more bits.
- Physical frame number can require fewer bits.

Example
64 KB logical memory:
2¹⁶ bytes

4 KB pages:
2¹² bytes

Number of logical pages:
2¹⁶ ÷ 2¹² = 2⁴ = 16 pages

Therefore:
4-bit logical page number

---------------------------------------------------------------------------------------------------------------------------------

11. Physical Memory in the Same Example

Physical memory:
32 KB = 2¹⁵

Page size:
4 KB = 2¹²

Number of frames:
2¹⁵ ÷ 2¹² = 2³ = 8 frames

Therefore:
3-bit physical frame number


Address structures
Logical/virtual address:
[4-bit Page Number | 12-bit Offset]

Physical address:
[3-bit Frame Number | 12-bit Offset]

---------------------------------------------------------------------------------------------------------------------------------

12. Example: 64 KB Process in 32 KB Memory

The process has:
- 16 logical pages
- Physical memory has only 8 frames

Therefore, at most 8 of the process's pages can be in main memory at one time.
The remaining pages can be brought into memory when needed.

Key idea
Logical memory does not have to fit entirely inside physical memory.
This is the main advantage provided by virtual memory and demand paging.

---------------------------------------------------------------------------------------------------------------------------------

13. TLB vs. Page Table vs. Demand Paging

| Concept            | Purpose                                                 |
| ------------------ | ------------------------------------------------------- |
| **Page Table**     | Maps logical pages to physical frames                   |
| **TLB**            | Speeds up access to frequently used page mappings       |
| **Demand Paging**  | Loads pages into memory only when needed                |
| **Page Fault**     | Occurs when a requested page isn't in memory            |
| **Virtual Memory** | Allows logical memory to be larger than physical memory |

---------------------------------------------------------------------------------------------------------------------------------

14. Key Concepts to Remember
TLB
Fast lookup for page → frame mappings.


TLB Hit
Mapping found in TLB.


TLB Miss
Mapping not found in TLB; check page table.


Demand Paging
Load pages only when needed.


Page Fault
Requested page is not currently in main memory.


Virtual Memory
Allows a process's logical address space to be larger than physical memory.

---------------------------------------------------------------------------------------------------------------------------------

15. Final Review

The overall progression is:

Paging
↓
Page Table
↓
Address Translation
↓
TLB improves translation speed
↓
Demand Paging loads pages as needed
↓
Page Fault handles missing pages
↓
Virtual Memory allows logical memory > physical memory


Mental Model:
Page table tells you where a page is. TLB helps find that information faster. Demand paging decides when pages need to be brought into memory. Virtual memory allows the process to appear larger than physical memory.

---------------------------------------------------------------------------------------------------------------------------------

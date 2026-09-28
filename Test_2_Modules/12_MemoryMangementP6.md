12 - Memory Management

Part 6: Intro, Optimal, FIFO

1. Page Replacement Policies

Once the paging mechanism is established, the operating system must make several policy decisions:
- Which physical page frame should receive a newly requested page?
- If there are no free page frames, which page should be removed from memory?
- Which page-table entries should be stored in the TLB?

For allocating a new page, the choice is straightforward because all page frames are the same size. Any available frame can be used.
The more important policy question is what to do when no empty frame exists.

---------------------------------------------------------------------------------------------------------------------------------

2. Evicting a Page
When memory is full and a new page must be loaded:
1) Select an existing page to evict from main memory.
2) Remove that logical page from its physical page frame.
3) Write the page back to disk if it has been modified since being loaded.
4) Place the newly requested page into the now-available frame.

The page table keeps track of whether a page has been modified, which determines whether it needs to be written back to disk.\
The operating system uses a page replacement policy to determine which page should be evicted.

---------------------------------------------------------------------------------------------------------------------------------

3. Example: Three Physical Page Frames

The example assumes:
- Main memory contains 3 physical page frames.
- A program generates the following logical-page reference pattern:
7 → 0 → 1 → 2 → 0 → 3 → 0 → 4 → 2 → 3

The purpose is to compare different page replacement policies using the same memory-reference pattern.

---------------------------------------------------------------------------------------------------------------------------------

4. Optimal Page Replacement

The optimal page replacement policy attempts to evict the page that will not be needed for the longest time in the future.

The idea is:
Look at the remaining memory references and remove the page whose next use is farthest away.

This produces the ideal result because it avoids replacing a page that will be needed again soon.

Important limitation:
The algorithm requires knowing the program's future memory-reference pattern ahead of time, which generally is not possible in practical programs. Therefore, although optimal replacement is ideal, it is generally not practical to use.

---------------------------------------------------------------------------------------------------------------------------------

5. Optimal Replacement — Example

With three available frames:
Reference 7
| Frame 1 | Frame 2 | Frame 3 |
| ------- | ------- | ------- |
| 7       | —       | —       |

Reference 0
| Frame 1 | Frame 2 | Frame 3 |
| ------- | ------- | ------- |
| 7       | 0       | —       |


Reference 1
| Frame 1 | Frame 2 | Frame 3 |
| ------- | ------- | ------- |
| 7       | 0       | 1       |


Now 2 is requested, but all three frames are occupied.

Look at the remaining references:
- 0 will be needed soon.
- 2 is the page being loaded.
- 7 and 1 are not needed again in the remaining sequence.
Therefore, either 7 or 1 could be replaced. The example arbitrarily replaces 7 with 2.

| Frame 1 | Frame 2 | Frame 3 |
| ------- | ------- | ------- |
| **2**   | 0       | 1       |


Next, 0 is requested.

Since 0 is already in memory, this is a hit; no replacement is necessary.
When 3 is requested, another replacement is required. The remaining references show that:
- 0 is needed soon.
- 2 is needed later.
- 1 is not needed again.

Therefore, 1 is replaced by 3.
This demonstrates the central idea of optimal replacement: use future references to decide which current page is least useful.

---------------------------------------------------------------------------------------------------------------------------------

6. Why Optimal Replacement Is Impractical

Optimal replacement provides the best possible decision if the future is known.

However, a real operating system generally does not know:
- Which memory addresses a process will access next.
- How far in the future a particular page will be needed.

Therefore:
Optimal = theoretically ideal, but generally impractical.
This motivates the need for replacement policies that can make decisions using information currently available.

---------------------------------------------------------------------------------------------------------------------------------

7. First-In, First-Out (FIFO) Page Replacement

FIFO replaces the page that has been in main memory for the longest amount of time.

It follows the same basic principle as a queue:
First page in → First page out

Unlike optimal replacement, FIFO does not examine when a page will be needed again.

---------------------------------------------------------------------------------------------------------------------------------

8. FIFO — Beginning of Example

Using the same three-frame example:
The first three page requests are:
7 → 0 → 1

So memory becomes:
| Frame 1 | Frame 2 | Frame 3 |
| ------- | ------- | ------- |
| 7       | 0       | 1       |

- The next request is 2.
There is no empty frame, so FIFO removes the page that entered memory first:

7 → replaced by 2
| Frame 1 | Frame 2 | Frame 3 |
| ------- | ------- | ------- |
| **2**   | 0       | 1       |

The next request is 0.
Since 0 is already present, it is a hit and no replacement occurs.

---------------------------------------------------------------------------------------------------------------------------------

9. FIFO — Continued Example

The next request is 3.
The pages currently in memory are:
2, 0, 1

Since 0 has now been in memory the longest, FIFO replaces 0:
0 → replaced by 3

The important point is that FIFO does not ask whether 0 is frequently used. It only considers how long the page has been in memory.

---------------------------------------------------------------------------------------------------------------------------------

10. FIFO Can Replace Frequently Used Pages

Later in the example, 0 is requested again.
However, FIFO had previously removed 0.
Therefore, 0 must be brought back into memory, replacing another page.

The sequence continues with additional replacements:
: 0 replaces 1
: 4 replaces 2
: 2 must then be brought back
: 2 replaces 3
: 3 must then be brought back
: 3 replaces 0

This demonstrates a major weakness of FIFO: a page can be frequently referenced and still be removed simply because it has been in memory longer than the other pages.

---------------------------------------------------------------------------------------------------------------------------------

11. FIFO — Main Idea

FIFO is:
- Simple
- Fair in the sense that the oldest page is removed first
- Based only on time in memory
- Not based on page usage
Therefore, FIFO may remove a page that the program is actively using and then require that page to be loaded again shortly afterward.

---------------------------------------------------------------------------------------------------------------------------------

12. Optimal vs. FIFO
| Policy      | Replacement Decision                             | Main Advantage                           | Main Problem                                   |
| ----------- | ------------------------------------------------ | ---------------------------------------- | ---------------------------------------------- |
| **Optimal** | Replace the page needed farthest in the future   | Produces the ideal replacement decisions | Requires knowledge of future memory references |
| **FIFO**    | Replace the page that has been in memory longest | Simple to implement                      | Ignores how frequently/recently pages are used |

Key distinction to remember:
- Optimal → “When will I need this page again?”
- FIFO → “How long has this page been here?”

---------------------------------------------------------------------------------------------------------------------------------

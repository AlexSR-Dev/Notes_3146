13 - Memory Management

Part 7: Second Change, LRU

1. Second Chance Page Replacement

The Second Chance algorithm is a modification of FIFO designed to overcome FIFO's main limitation: FIFO may remove a page that has been referenced recently.

It keeps:
: A list of pages in FIFO order
: A reference bit for each page


When a page needs to be evicted:
1) Look at the oldest page.
2) Check its reference bit.
3) If the bit is 0 → evict the page.
4) If the bit is 1 → give the page a second chance:
: Reset its reference bit to 0.
: Move it to the end of the list as the newest page.
: Examine the next oldest page.

The process continues until a page with a reference bit of 0 is found.

---------------------------------------------------------------------------------------------------------------------------------

2. Second Chance — Reference Bit

The reference bit indicates whether a page has been referenced.
: Reference bit = 0: Page has not been referenced since it was loaded or since its last second chance.
: Reference bit = 1: Page has been referenced recently.

When a page is first brought into memory, its reference bit starts at 0.
When the page is referenced, its reference bit becomes 1.
---------------------------------------------------------------------------------------------------------------------------------

3. Second Chance — Initial Example

Using the same example with three page frames:
Reference sequence:
7 → 0 → 1 → 2 → 0 → 3 → 0 → 4 → 2 → 3

Initially:
| FIFO List    | Reference Bit |
| ------------ | ------------: |
| **7** oldest |             0 |
| **0**        |             0 |
| **1** newest |             0 |

The pages 7, 0, and 1 are all brought into memory with reference bits of 0.
Now page 2 is requested.
The oldest page is 7.
Its reference bit is 0, so it does not receive a second chance.

Therefore:
7 is evicted → 2 is brought in.

Updated list:
| FIFO List    | Reference Bit |
| ------------ | ------------: |
| **0** oldest |             0 |
| **1**        |             0 |
| **2** newest |             0 |

---------------------------------------------------------------------------------------------------------------------------------

4. Second Chance — Referencing a Page
Next, 0 is requested.
Page 0 is already in memory, so it does not need to be brought in.

Instead, its reference bit changes:
0 → 1

The list remains:
| FIFO List | Reference Bit |
| --------- | ------------: |
| 0         |         **1** |
| 1         |             0 |
| 2         |             0 |
- The important point is that referencing a page sets its reference bit to 1.

---------------------------------------------------------------------------------------------------------------------------------

5. Second Chance — Giving a Page a Second Chance

Now page 3 is requested.
There is no empty frame, so we examine the oldest page:
0

But:
Reference bit of 0 = 1

Therefore, 0 is not immediately evicted.
Instead:
1) Reset 0's reference bit:
1 → 0
2) Move 0 to the end of the FIFO list.
3) Examine the new oldest page.

The list now becomes:
| FIFO List    | Reference Bit |
| ------------ | ------------: |
| **1** oldest |             0 |
| **2**        |             0 |
| **0** newest |             0 |

Now page 1 is the oldest and has a reference bit of 0.

Therefore:
1 is evicted → 3 replaces 1.

Updated list:
| FIFO List    | Reference Bit |
| ------------ | ------------: |
| **2** oldest |             0 |
| **0**        |             0 |
| **3** newest |             0 |

This is the central example of why it is called Second Chance: page 0 would have been removed under normal FIFO, but because it had recently been referenced, it was given another opportunity to remain in memory.

---------------------------------------------------------------------------------------------------------------------------------

6. Second Chance — Continued Example

The next reference is 0.

Since 0 is already in memory:
Reference bit of 0: 0 → 1

No replacement is necessary.
Next, 4 is requested.
The oldest page is 2, whose reference bit is 0.

Therefore:
2 is evicted → 4 replaces 2.

The list is reordered with 4 becoming the newest page.

---------------------------------------------------------------------------------------------------------------------------------

7. Second Chance — Another Second Chance

Next, 2 is requested.
Unfortunately, 2 was just replaced by 4, so 2 must be brought back into memory.
The oldest page is 0.

However:
Reference bit of 0 = 1

Therefore, 0 receives a second chance:
: Reset 1 → 0
: Move 0 to the end of the list
: Examine the next oldest page

The next page has a reference bit of 0, so that page can be evicted.
Thus, 2 replaces that page.

---------------------------------------------------------------------------------------------------------------------------------

8. Second Chance — Final Example
The next reference is 3.
The page at the beginning of the list can be replaced because its reference bit is 0.

Therefore:
3 replaces 4

The FIFO list is then adjusted one final time.

---------------------------------------------------------------------------------------------------------------------------------

9. Second Chance — Main Idea

Second Chance can be summarized as:
FIFO + Reference Bit

Instead of automatically removing the oldest page:
Oldest + reference bit 0 → Replace

Oldest + reference bit 1 → Reset bit, move to back, give second chance
This allows recently referenced pages to remain in memory longer than they would under ordinary FIFO.

---------------------------------------------------------------------------------------------------------------------------------

10. Least Recently Used (LRU)

The next replacement algorithm is Least Recently Used (LRU).
LRU replaces the page that was used least recently.

Rather than asking:
"Which page entered memory first?"

LRU asks:
"Which page has gone the longest without being used?"

This means LRU considers recent page usage, unlike FIFO.

---------------------------------------------------------------------------------------------------------------------------------

11. LRU — Initial Example

Using the same three-frame example:
Initially:
7 → 0 → 1

Memory contains:
| Frame | Page |
| ----- | ---: |
| 1     |    7 |
| 2     |    0 |
| 3     |    1 |

Now 2 is requested.

Determine which existing page was used least recently:
: 7 was used earlier.
: 0 was used after 7.
: 1 was used most recently among the three.

Therefore, 7 is the least recently used.
7 → replaced by 2

Memory now contains:
2, 0, 1

---------------------------------------------------------------------------------------------------------------------------------

12. LRU — Using Recent References

Next, 0 is referenced.
Because 0 is already in memory, no replacement occurs.
However, 0 now becomes the most recently used page.
Next, 3 is requested.

Looking at the previous references:
: 0 was just used.
: 2 was used before 0.
: 1 was used the furthest in the past.

Therefore:
1 is the least recently used → 1 is replaced by 3.

Memory becomes:
2, 0, 3

---------------------------------------------------------------------------------------------------------------------------------

13. LRU — Continued Example

Next, 0 is referenced again.
Because 0 is already in memory, it becomes the most recently used page.
Now 4 is requested.

Compare the recent usage:
: 0 → used most recently
: 3 → used before 0
: 2 → used least recently

Therefore:
2 is replaced by 4.

Memory becomes:
4, 0, 3

---------------------------------------------------------------------------------------------------------------------------------

14. LRU — Replacing a Page That Is Needed Again

Next, 2 is requested.
But 2 was just removed, so it must be brought back.

At this point:
: 4 was recently used.
: 0 was also recently used.
: 3 was the least recently used.

Therefore:
3 is replaced by 2.

Memory becomes:
4, 0, 2

---------------------------------------------------------------------------------------------------------------------------------

15. LRU — Final Replacement

Next, 3 is requested.
But 3 was just removed.

Looking at the remaining pages:
: 4 was recently used.
: 2 was used more recently than 0.
: 0 has gone the longest without being used.

Therefore:
0 is replaced by 3.

This demonstrates how LRU continuously tracks which page was used least recently, rather than simply tracking when pages entered memory.

---------------------------------------------------------------------------------------------------------------------------------

16. Locality of Reference

LRU tends to work well when a program demonstrates locality of reference.

Locality of reference means that when a program accesses a particular memory location/page, it is likely to:
: Access the same page again, or
: Access nearby pages

in the near future.

Therefore, if a program has good locality, pages that were recently used are more likely to be needed again soon.
LRU takes advantage of this behavior by keeping recently used pages in memory and replacing pages that have not been used for the longest time.

---------------------------------------------------------------------------------------------------------------------------------

17. FIFO vs. Second Chance vs. LRU
| Algorithm         | What determines replacement?                     | Key idea                                     |
| ----------------- | ------------------------------------------------ | -------------------------------------------- |
| **FIFO**          | Page that has been in memory the longest         | Oldest page leaves                           |
| **Second Chance** | Oldest page **unless** its reference bit is 1    | Recently referenced pages get another chance |
| **LRU**           | Page that has not been used for the longest time | Keep recently used pages                     |

Quick recognition:
- FIFO → Oldest
- Second Chance → Oldest, but check reference bit
- LRU → Least recently used

The major progression in these algorithms is that each one considers more information about page usage than the basic FIFO approach.

---------------------------------------------------------------------------------------------------------------------------------

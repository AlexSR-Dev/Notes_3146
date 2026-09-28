10 - Memory Management

Part 5a: Address Translation in Paged Systems

1. Address Translation
- In paging, a logical address has two parts:
: Page Number (pn) - identifies which logical page.
: Offset (off) - identifies the location within that page.

A physical address also has two parts:
: Page frame number (fn) - idnetifies the physical frame.
: Offset (off)
 - location within that frame.

 NOTE: Page number -> Page table -> Page frame number.
 - THe offset stays the same because pages and page frames are the same size.

 ---------------------------------------------------------------------------------------------------------------------------------

2. Basic Translation Process
When a process generates a logical address:
1) Separate the address into:
- Page number
- Offset
2) Use the page number to look up the page table.
3) Find the corresponding page frame number.
4) Keep the offset unchanged.
5) Combine:
- Page frame number + offset
6) This produces the physical address.

Mental model
Logical address:
[Page Number | Offset]

↓ Page table

Physical address:
[Frame Number | Offset]

---------------------------------------------------------------------------------------------------------------------------------

3. Example: Page 1

Suppose a process generates an address with:
- Page number = 1
- Offset = some value

The page table says:
Page 1 → Frame 4

Therefore:
- Frame number becomes 4
- Offset remains unchanged

So:
Logical: [1 | offset]
Physical: [4 | offset]

Common trap
Do not change the offset during translation.

---------------------------------------------------------------------------------------------------------------------------------

4. Binary Addresses

Computer memory addresses are represented in binary.

Because binary numbers can become long, hexadecimal is often used as a more compact representation.

Important rule
With n bits:
Number of possible values = 2ⁿ

Examples:
2 bits → 4 values
3 bits → 8 values
4 bits → 16 values

---------------------------------------------------------------------------------------------------------------------------------

5. Determining Address Size

The number of bits depends on how many possible memory locations must be represented.

Example
32 KB of physical memory:
32 KB = 2¹⁵ bytes

Therefore, you need:
15-bit physical addresses
because 15 bits can represent 2¹⁵ different addresses.

---------------------------------------------------------------------------------------------------------------------------------

6. Page Size Determines the Offset

Suppose the page size is:
4 KB = 2¹² bytes

Therefore, the offset requires:
12 bits

Important
The least significant 12 bits are the offset.

So:
[Page/Frame Number | 12-bit Offset]
The same offset size is used for both logical and physical addresses.

---------------------------------------------------------------------------------------------------------------------------------

7. Finding the Number of Page Frames

Formula:
Physical memory ÷ Page-frame size

Example:
Physical memory = 32 KB = 2¹⁵
Page size = 4 KB = 2¹²

Therefore:
2¹⁵ ÷ 2¹² = 2³ = 8 page frames

Since there are 8 frames:
Frame number requires 3 bits.

---------------------------------------------------------------------------------------------------------------------------------

8. Finding the Number of Logical Pages

Formula:
Logical address space ÷ Page size

Example:
Logical address space = 16 KB = 2¹⁴
Page size = 4 KB = 2¹²

Therefore:
2¹⁴ ÷ 2¹² = 2² = 4 pages

Since there are 4 pages:
Page number requires 2 bits.

---------------------------------------------------------------------------------------------------------------------------------

9. Example Memory System

Given:
| Component             |    Size |
| --------------------- | ------: |
| Physical memory       |   32 KB |
| Logical address space |   16 KB |
| Page/frame size       |    4 KB |
| Physical address      | 15 bits |
| Logical address       | 14 bits |
| Offset                | 12 bits |
| Page frames           |       8 |
| Logical pages         |       4 |
| Frame number          |  3 bits |
| Page number           |  2 bits |

Address structure

Logical address:
[2-bit Page Number | 12-bit Offset]

Physical address:
[3-bit Frame Number | 12-bit Offset]

---------------------------------------------------------------------------------------------------------------------------------

10. Concrete Address Translation

Suppose the logical address contains:
- Page number = 1
- Offset = 101001001010

The page table gives:
Page 1 → Frame 3

Convert frame 3 to the required 3-bit representation:
3 = 011

Therefore:
Logical:
01 | 101001001010

Physical:
011 | 101001001010

The offset is copied exactly; only the page number is replaced by the corresponding frame number.

---------------------------------------------------------------------------------------------------------------------------------

11. How to Solve Address-Translation Problems

When given a new problem, use this order:

Step 1 — Find offset bits
Look at the page size.
Example:
4 KB = 2¹² → 12 offset bits


Step 2 — Find number of pages
Logical memory ÷ page size


Step 3 — Find page-number bits
Use:
2ⁿ = number of pages


Step 4 — Find number of frames
Physical memory ÷ page size


Step 5 — Find frame-number bits
Use:
2ⁿ = number of frames


Step 6 — Split the logical address
[Page Number | Offset]


Step 7 — Look up the page number
Use the page table.


Step 8 — Replace page number with frame number
Keep the offset unchanged.

---------------------------------------------------------------------------------------------------------------------------------

12. Common Traps
❌ Changing the offset

The offset does not change.

❌ Using physical memory to determine page-number bits

Page-number bits come from the logical address space.

❌ Using logical memory to determine frame-number bits

Frame-number bits come from physical memory.

❌ Forgetting leading zeros

If the frame number requires 3 bits, frame 3 must be written as:

011, not just 11.

❌ Mixing up pages and frames
Pages → logical memory
Frames → physical memory

---------------------------------------------------------------------------------------------------------------------------------

13. Key Terms
| Term                 | Meaning                             |
| -------------------- | ----------------------------------- |
| **Logical address**  | Address generated by a process      |
| **Physical address** | Actual address in main memory       |
| **Page**             | Fixed-size block of logical memory  |
| **Page frame**       | Fixed-size block of physical memory |
| **Page number**      | Identifies a logical page           |
| **Frame number**     | Identifies a physical frame         |
| **Offset**           | Location within a page/frame        |
| **Page table**       | Maps pages to frames                |
| **MMU**              | Performs memory address translation |

---------------------------------------------------------------------------------------------------------------------------------
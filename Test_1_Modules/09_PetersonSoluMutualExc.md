09 - Peterson's Solutions for Mutual Exclusion

Strict Alternation successfully prevents simultaneous access, but it is too rigid. Peterson’s Solution keeps mutual exclusion while removing that unnecessary rigidity.

The most important flow to remember is:

Strict Alternation
        ↓
Problem: forced turn-taking
        ↓
Peterson's Solution
        ↓
Adds "interest" information
        ↓
A process waits only when the other process also wants in
        ↓
Mutual exclusion is preserved
        ↓
But busy waiting remains

The analysis below follows the document's concept order and bold-topic flow.

1. Peterson's Solution for Mutual Exclusion
Central Idea

Peterson's solution is a software-based solution to the critical-region problem for two processes.

Its major improvement over strict alternation is:

A process does not have to wait merely because it is "not its turn." It only waits when the other process is actually interested in entering its critical region.

It still guarantees:

Only one process
      ↓
inside a critical region
      ↓
at a time

So Peterson's solution keeps the important property of mutual exclusion, while making access more flexible.

Remember

Strict Alternation:

"Wait because it isn't your turn."

Peterson:

"Wait only if the other process actually wants in."

That distinction is the heart of the entire document.

2. The Problem With Strict Alternation

The previous solution required:

A → B → A → B → A → B

with no exceptions.

The problem is that one process may not actually need the critical region.

Imagine:

Process A → finishes critical work
Process B → finishes critical work
Process B → wants critical region again
Process A → currently doing unrelated work

Under strict alternation:

B → MUST WAIT

even though A isn't interested in the shared resource.

So strict alternation creates:

Unnecessary blocking.

Peterson's improvement

Peterson asks a better question:

"Does the other process actually want to enter?"

If the answer is no:

Continue.

If the answer is yes:

Potential conflict → wait according to the protocol.
3. Understanding the Idea Through an Analogy

The Mike and Melissa analogy illustrates the difference.

Strict Alternation
Mike
 ↓
Melissa
 ↓
Mike
 ↓
Melissa

Even if Melissa isn't ready, Mike cannot go again.

Peterson's Solution

The turn-taking idea becomes much more flexible.

Suppose:

Mike → takes turn
Melissa → takes turn
Mike → is busy doing something else

Melissa can take another turn because Mike has not indicated interest.

So:

Other process not interested
        ↓
No reason to wait

This is the conceptual breakthrough of Peterson's solution.

Core mental model

Interest matters more than rigid turn order.

4. Setting Up the Code: Key Variables

Peterson's solution uses two shared variables.

This is an extremely important difference from strict alternation.

Variable 1: turn

turn is an integer indicating whose turn it currently is.

turn = 0
    ↓
Process 0

turn = 1
    ↓
Process 1

This variable is familiar from strict alternation.

Variable 2: interested[]

This is the new piece.

interested[] is an array with one entry for each process.

For two processes:

interested[0]
interested[1]

Initially:

interested[0] = FALSE
interested[1] = FALSE

Each process uses its own slot to announce:

"I want to enter my critical region."

For example:

interested[0] = TRUE

means:

Process 0 is interested in entering.

5. Why Two Variables?

This is worth understanding conceptually.

Strict alternation has:

turn

which basically answers:

"Whose turn is it?"

Peterson adds:

interested[]

which answers:

"Who actually wants access?"

So together:

turn
  +
interested[]
  ↓
Who gets priority?
+
Does the other process actually want in?

This combination is what allows Peterson's solution to remain mutually exclusive without being rigidly alternating.

6. The enter_region Function

Every process must call:

enter_region(process);

before entering its critical region.

The key rule is:

A process cannot proceed into its critical region until enter_region() finishes.

If the function keeps waiting, the process keeps waiting.

Think:

enter_region()
       ↓
"Am I allowed in?"
       ↓
YES → critical region
NO  → wait
7. enter_region — Step 1: Identify the Processes

The function receives:

process

which identifies the current process:

0 or 1

Then it calculates:

other = 1 - process;
Why does this work?

If:

process = 0

then:

other = 1 - 0 = 1

If:

process = 1

then:

other = 1 - 1 = 0

Therefore:

process = 0 → other = 1
process = 1 → other = 0

The variable other simply identifies the other process.

8. enter_region — Step 2: Declare Interest

The process sets:

interested[process] = TRUE;

This means:

"I want to enter my critical region."

For Process 0:

interested[0] = TRUE;

For Process 1:

interested[1] = TRUE;

This is an important difference from strict alternation because now the system knows whether a process actually wants access.

9. enter_region — Step 3: Set turn

Next:

turn = process;

The process sets turn to its own ID.

For Process 0:

turn = 0;

For Process 1:

turn = 1;

At first this may look strange:

"Why would a process set the turn to itself and then potentially wait?"

Because turn works together with interested[].

You cannot understand the waiting rule by looking at turn alone.

10. enter_region — Step 4: The Waiting Condition

Now we reach the most important line in the solution:

while (turn == process && interested[other] == TRUE);

A process waits only while BOTH conditions are true.

Condition #1
turn == process

Meaning:

The turn variable still equals this process.

Condition #2
interested[other] == TRUE

Meaning:

The other process also wants to enter.

Therefore:

WAIT IF:

I have the turn
        AND
The other process is interested

11. Understanding the &&

The && is extremely important.

The process does not wait simply because:

turn == process

And it does not wait simply because:

interested[other] == TRUE

It waits only when:

Condition A = TRUE
AND
Condition B = TRUE

So:

turn == process	interested[other]	Wait?
TRUE	TRUE	YES
TRUE	FALSE	NO
FALSE	TRUE	NO
FALSE	FALSE	NO

This table is one of the best ways to understand Peterson's waiting rule.

Memorize:

Both true → wait.
Either one false → proceed.

The document explicitly states that the loop ends as soon as either condition becomes false.

12. The enter_region Logic as Plain English

Take:

while (turn == process && interested[other] == TRUE);

and translate it into English:

"Keep waiting while it is still my turn AND the other process also wants access."

Eventually one of two things happens:

The turn changes
       OR
The other process stops being interested

Then:

while condition = FALSE
       ↓
leave loop
       ↓
enter critical region

This translation is much more useful than simply memorizing the syntax.

13. The leave_region Function

Once a process finishes its critical region, it calls:

leave_region(process);

The function performs one critical action:

interested[process] = FALSE;

Meaning:

"I am no longer interested in entering my critical region."

This is the signal that can allow the other process to continue.

14. The Full Peterson Pattern

Now we can combine everything:

Process wants critical region
        ↓
interested[process] = TRUE
        ↓
turn = process
        ↓
Check:
turn == process
AND
interested[other] == TRUE
        ↓
Both TRUE?
   /        \
 YES         NO
 ↓           ↓
WAIT       ENTER
             ↓
      Critical Region
             ↓
   interested[process] = FALSE
             ↓
           LEAVE

That is the core algorithm.

15. Walking Through an Example

Now we follow the document's exact example.

Initially:

interested[0] = FALSE
interested[1] = FALSE

Suppose Process 0 begins.

Step 1 — Process 0 Calls enter_region

Process 0 determines:

process = 0
other = 1

Then:

interested[0] = TRUE;
turn = 0;

Now:

interested[0] = TRUE
interested[1] = FALSE
turn = 0
Check the while condition
turn == process
0 == 0
TRUE

and:

interested[other]
interested[1]
FALSE

So:

TRUE && FALSE
= FALSE

The loop does not run.

Therefore:

Process 0 enters its critical region.

16. Process 0 Gets Interrupted

Now suppose Process 0 is inside its critical region:

Process 0 → CRITICAL REGION

and gets interrupted.

Process 1 gets scheduled.

This creates the situation we need to test:

Can Process 1 enter while Process 0 is still inside?

Peterson must prevent that.

17. Step 2 — Process 1 Wants Access

Process 1 calls enter_region.

Now:

process = 1
other = 0

Process 1 sets:

interested[1] = TRUE;
turn = 1;

Now:

interested[0] = TRUE
interested[1] = TRUE
turn = 1

Process 0 is still interested because it is still inside its critical region.

18. Process 1 Checks the Waiting Condition

The condition is:

turn == process && interested[other] == TRUE

For Process 1:

turn == process
1 == 1
TRUE

and:

interested[other]
interested[0]
TRUE

Therefore:

TRUE && TRUE
= TRUE

So Process 1 waits.

It busy-waits.

19. Step 3 — Process 0 Leaves

Eventually Process 0 finishes its critical region.

It calls:

leave_region(0);

which performs:

interested[0] = FALSE;

Now:

interested[0] = FALSE
interested[1] = TRUE

Process 1 eventually checks its while condition again.

It sees:

turn == process
TRUE

but:

interested[other]
interested[0]
FALSE

Therefore:

TRUE && FALSE
= FALSE

The loop ends.

Process 1 enters the critical region.

20. Why Mutual Exclusion Is Preserved

This example demonstrates the core protection mechanism.

While Process 0 is inside:

interested[0] = TRUE

Therefore Process 1 cannot enter while Process 0 remains interested.

Only when Process 0 leaves and executes:

interested[0] = FALSE;

can Process 1 proceed.

So:

Process 0 inside
      ↓
interested[0] = TRUE
      ↓
Process 1 waits
      ↓
Process 0 leaves
      ↓
interested[0] = FALSE
      ↓
Process 1 proceeds

This is why the solution maintains mutual exclusion.

21. Understanding the Slide Code

The actual C-like implementation is:

#define FALSE 0
#define TRUE 1
#define N 2

int turn;
int interested[N];

void enter_region(int process)
{
    int other;
    other = 1 - process;
    interested[process] = TRUE;
    turn = process;

    while (turn == process && interested[other] == TRUE);
}

void leave_region(int process)
{
    interested[process] = FALSE;
}

22. Meaning of the #define Statements
#define FALSE 0
#define TRUE 1
#define N 2

These create readable names.

So:

FALSE → 0
TRUE  → 1
N     → 2

This makes the rest of the program easier to understand.

23. Meaning of the Shared Variables
int turn;

Means:

Which process currently has the turn?

And:

int interested[N];

means:

Each process has a slot indicating whether it currently wants to enter.

Both are shared between the processes.

24. Understanding the null statement

The line:

while (turn == process && interested[other] == TRUE);

ends with:

;

That means the loop body is empty.

The process does nothing except repeatedly evaluate the condition.

This is the same busy-waiting concept you learned in Part 6.

25. Peterson's Main Advantage
No Strict Alternation

This is the major improvement.

With strict alternation:

Other process not interested
        ↓
Still must wait ❌

With Peterson:

Other process not interested
        ↓
Can proceed ✅

This makes Peterson significantly more flexible.

The process does not have to wait just because the other process's "turn" hasn't been completed.

26. Peterson's Remaining Disadvantage
Busy Waiting

Peterson fixes:

❌ Rigid alternation

But it does not fix:

❌ Busy waiting

A process that cannot enter still repeatedly checks:

while (...)

So the CPU is still being used for waiting instead of productive work.

Important comparison
Strict Alternation
✅ Mutual exclusion
❌ Busy waiting
❌ Unnecessary blocking

Peterson's Solution
✅ Mutual exclusion
✅ Removes unnecessary turn-based blocking
❌ Busy waiting remains
27. Strict Alternation vs. Peterson's Solution

This comparison is probably the single most useful thing to memorize from the document.

Concept	Strict Alternation	Peterson's Solution
Mutual exclusion	✅	✅
Uses turn	✅	✅
Uses interested[]	❌	✅
Rigid turn-taking	✅	❌
Can proceed when other process isn't interested	❌	✅
Busy waiting	✅	✅
Practical improvement	Limited	Better

The document's central comparison is that Peterson adds flexibility by considering whether the other process is actually interested, while still retaining busy waiting as a weakness.

28. The Most Important Logical Condition

You should be able to look at:

while (turn == process && interested[other] == TRUE);

and immediately say:

"Wait only when it is still my turn AND the other process also wants to enter."

Then remember:

Both TRUE → WAIT
Either FALSE → ENTER

This is the central piece of reasoning behind Peterson's Solution.

29. Important Definitions and Terms
Term	Meaning
Peterson's solution	Software-based mutual exclusion method for two processes
Mutual exclusion	Only one process can occupy the critical region at a time
Critical region	Code that accesses the shared resource
turn	Shared variable indicating the current turn
interested[]	Shared array indicating whether each process wants to enter
enter_region()	Function a process calls before entering its critical region
leave_region()	Function called after leaving the critical region
other	ID of the other process
Busy waiting	Repeatedly checking a condition while waiting, consuming CPU cycles
Null statement	An empty statement represented by ;
30. Concept Flow to Memorize

This is the flow I would use for studying this document:

RACE CONDITION
       ↓
Need MUTUAL EXCLUSION
       ↓
STRICT ALTERNATION
       ↓
Problem: too rigid
       ↓
PETERSON'S SOLUTION
       ↓
Two shared variables:
    turn
    interested[]
       ↓
Process declares interest
       ↓
Process sets turn
       ↓
Check both conditions
       ↓
Both TRUE?
   ↙       ↘
 YES       NO
 ↓          ↓
WAIT      ENTER
            ↓
    Critical Region
            ↓
Leave region
            ↓
Set interested[process] = FALSE
            ↓
Other process can proceed
31. The "One Sentence" Version

Peterson's solution provides mutual exclusion for two processes by combining a turn variable with an interested[] array, allowing a process to wait only when both it and the other process are competing for the critical region.

That single sentence captures most of the document.

32. Final On-the-Go Review
Problem

Strict alternation forces rigid turn-taking.

Solution

Peterson's Solution introduces interested[].

turn

Tracks whose turn it is.

interested[]

Shows whether each process actually wants access.

enter_region()

Declares interest, sets the turn, and waits when necessary.

Waiting condition
turn == process && interested[other] == TRUE
Interpretation

Both conditions must be true for the process to wait.

leave_region()

Sets:

interested[process] = FALSE;
Advantage

No unnecessary waiting simply because the other process hasn't taken its turn.

Remaining disadvantage

Busy waiting still wastes CPU cycles.

Bottom Line

Peterson improves strict alternation by replacing rigid turn-taking with interest-aware waiting, while still guaranteeing mutual exclusion.
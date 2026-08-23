04 - Security Focus: Fork Bombs

As a process is a running instance of a program that an OS tracks and manage.
It also vital to understand the types of cyberattacks and how to defend aganist them.



What Is a Fork Bomb?
- A type of attack that targets a computer system by rapidly launching enourmous numbers of new processes,
that the system can't handle, until the system can no longer create any additional processes.

Additioanl alias:
• Rabbit Virus
• Wabbit
- Refers the same idea of the attack 'breeds' processes at a quick and uncontrollable rate.


Why "Fork"?
- Comes from a system operation used to create a new process.
In which it creates a copy of itself with a distingishment for the OS. However, a fork bomb abuses this mechanism
with repeated usage with no limit, until the system is overwhelmed.




How a Fork Bomb Attacks a System:
1. The attack starts by creating one new process.
2. The new process immediately creates another new process.
3. The process, in turns creates another one.
4. This cycle repeats endlessly, without stopping.


The Result: The Process Table Fills Up
- Every OS keeps a process table that tracks every process currently running on the system, with its limited size,
only a maximum number of processes can be tracked at once.

- Thus, the fork bomb immediately fills every slot, and when its full the OS can no longer create any new processes.
Not full of malicious or legitimate processes.
- Thus, the system typically crashes or becomes unresponsive as it can't lanuch anything new.





Why This is Called a "Denial of Service" Attacl:
A fork bomb is classified as Denial of Service (DoS) attack.
- Prevents legitimate users from operating the system when they need to.
- Attacks the availability, but the attcaks isn't necessarily trying to steal data (confidentiality) or
corrupt data (integrity); its objective to make the system unavailable to those that need it.



How to Prevent a Fork Bomb Attack:
- Just limiting the number of new processes created by a user.

If a user or malicious program is running under the user's account is capped at a small reasonable number of processes.
The system remains healthy and available as this prevent the consumption of the entire process table.


Setting a Process Limit in Linux
ulimit -u 40
- Limits the current user to maximum of 40 processes running at the same time, thus for a fork bomb it would
be stopped after creating 40 processes. The number of processes can be adjusted for a hard ceiling on process creation.


Why This Matters for IT Professionals:
- It provides insight to what to defend against attacks that my try to abuse building blocks of systems.
- Recognizing the connection, between how a system is supposed to work and how that same functionality
can be exploited is a core skill for IT security.
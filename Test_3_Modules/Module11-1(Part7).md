Module 11-1 Part 7 - Types of Network Services

- Within layered network architecture, each layer provides services to the layer above it.
These services determine how data is transferred between a source computer and a destination computer.

- This lesson will describe the type of network services:
1) Is a connection established before data is transferred?
- Providing connection-oriented or connection-less service.

2) Does the service check that the data has been received correctly?
- Providing reliable and unreliable service.


---------------------------------------------------------------------------------------------------------------------------------


1. Connection-Oriented Service
- Also known as connection-based service, establishes a logical connection between the source and destination computers
before transferring data.
- The logical connection represents the intention of communication to begin data exchanges.

- Three Phases:
1) Phase 1: Establish the Connection
- The source and destintation set up a logical connection. May include negotiating parameters for both endpoints to agree on
the conditions of communications.

2) Phase 2: Transfer Data
- The endpoints use the established connection to exchange data. Multiple units of data share the connection details and
are routed along the same established path.

3) Phase 3: Release the Connection
- When communication is complete, the connection is terminated and the resources associated with maintaining it can be released.


ENTIRE PROCESS: Establish -> Transfer Data -> Release


1.1) Telephone Analogy
- This analogy illustrates connection-oriented service well:
• Establish: You call someone, and the call connects.
• Transfer: Once connected, you talk.
• Release: When the converstaion ends, you hang up.


---------------------------------------------------------------------------------------------------------------------------------


2. Connection-Less Service
- Does not establish a connection between the source and destination before sending data.
- Instead the source sends each data unit when needed. Every unit is handled independently of the other units.

CONSEQUENCES:
1) Each data unit carries the full destination address.
2) Each data unit is routed through the network independently of the other data units.


2.1) Postal Analogy
- This illustrates connection-less service.
• When you send a letter, you write the recipient's full address on the envelope. Thus, no need to establish a special connection
with the recipient before mailing it.
• Each letter is handled independently, and two letters sent to the same recipient are not guaranteed to travel along exactly
the same route.


---------------------------------------------------------------------------------------------------------------------------------


3. Reliable and Unreliable Service
- Connection is separate from reliability. Each of the two services above (connection/not) can be of two kinds.


3.1) Reliable Service
- Aims to ensure the accuracy and integrity of communicated data.

- The mechanism ar based on "acknowledgements" and retransmission.
• Step 1: Sender Transmits Data
- The sender sneds a data unit to the receiver.

• Step 2: Receiver acknowledges receipt
- The receiver sends an acknowledgement to the sender.

• Step 3: Sender checks for the acknowledgement
- The sender excepts the acknowledgement within a specified time.

• Acknowledgement Received:
- The sender learns that the receiver acknowledged the data.

• No Acknowledgement:
- If the expected time passes, the sender resends the data.


- This acknowledgement mechansim helps the sender determine whether the receiver received tht data within the specified time, if
not then the sender retransmits the data.

THUS: Reliable Service has Overhead:
- The requirements need additional work beyond just transmitting data:
• Acknowledgement Traffic: The receiver must send acknowledgements back to the sender.
• Tracking: The sender must keep track of the data it has sent and how long it has been waiting.
• Timers: The sender needs to determine when the acknowledgement hasn't arrived within the expected time.
• Retransmission: When acknowledgement does not arrive in time, the sender sends data again.

- All this consumes resources and generates additional traffic.



3.2) Unreliable Service
- Sends data without checking whether it was correctly received.
THUS: Sender does not require the receiver to acknowledge each transmission.

RESULTING:
• Lost: A data unit never reaches its intended destination.
• Delayed: A data unit arrives later than expected.
• Corrupted: The data arrives with its contents altered or damaged.


ADVANTAGE:
- Overhead is reduced, since there is no acknowledgements, timers, and retransmissions.

TAKEAWAY: Reliability is a trade-off between delivery safeguards and overhead. Neither approach is universall best for every application.

OVERALL: The classifications are independent (connection/reliablility). A service can be connection-oriented or connection-less, and
it can independently be reliable or unreliable. Providing four possible combinations.


---------------------------------------------------------------------------------------------------------------------------------
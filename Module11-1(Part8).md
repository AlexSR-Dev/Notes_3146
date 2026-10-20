Module 11-1 Part 8 - Physical Layer, Link Layer - Framing

- Part 8 begins examining the layers of the hybrid network model, starting with the lowest layers: physical and link.
- The Link layer's job is to carry a unit of data across a single link.
- But proposes another issue, the physical layer cannot solve, it delivers a continuous stream of bits, but the receiver needs to
know where one unit ends and the enxt begins.
SOLUTION: Framing


---------------------------------------------------------------------------------------------------------------------------------


1. The Physical Layer
- Bottom layer of the hybrid model, defines the electrical, timing, and other inteface required to transfer data bits
between two adjacent nodes.

- Adjacent ndoes are network nodes directly connected by a communication channel. Conceptually behaving like a wire, sending
from one end to the other in the same order.

HOWEVER: This layer's ordering guarantee does not tell the receiver where packets begin and end or even that every bit arrives
correctly.


---------------------------------------------------------------------------------------------------------------------------------


2. What the Link Layer Does
- Provides well-defined services to the network layer.

Three Main Responsibilities
2.1) Transfer of a Data Unit Between Adjacent Nodes:
- Central task is to transfer a data unit across a single link, between adjacent nodes.

• The link layer works with packets rather than just raw bits. The logical unit of data provided from the network layer.

• The link layer uses a physical address. The receiving node is identified using its "MAC address" (Media Access Control Address),
during the transfer of data.

• The receiver must distinguish one packet from another. A sender may transmit multiple packets in sequence.



2.2) Error Control
- A physical connection channel can introduce errors due to electrical noise or imperfections.
- The link layer is responsible for error control: Detecting and possibly correcting certain transmission erros.
THUS: Ensures that the packet reaching the receiving side's network layer is error-free.



2.3) Flow Control
- A sender may transmit packets faster than a receiver can process them. If packets arrive faster than the receiver
can hanlde them, the receiver may become overwhelmed.
THUS: The Link layer regulates the flow of packets over the link, so that the sender does not inundate the receiver.

NOTE: The link layer transfers packets across a single link, addresses transmission errors, and regulates packet flow.


---------------------------------------------------------------------------------------------------------------------------------


3. Framing
Recall, a physical layer delivers a stream of bits (packet) in order. But the stream of bits does not tell the receiver how
those bits are grouped into packets.
THUS: Without a known boundary rule, the receiver cannot determine solely where one packet ends and the next begins.

SOLUTION:
- Encapsulation of each packet received from the network layer into a frame.
THUS: The link layer encapsulates each packet given to it by the network layer into a frame. The frame itself consists of:
• Header
• Packet
• Trailer

-  Succesive frames can now be transmitted sequentially through the physical layer.
THUS: A frame is a link layer's logical unit of data transfer.

TO FUNCTION:
- The two link layers must agree on the format of the header and trailer, to identify frame boundaries.
Link Layer Protocol must be understood by both computers.

NOTE:
- The following sections introduces 4 wats to identify frame boundaries:
• Byte Count
• Flag bytes with byte stuffing
• Flag bits with bit stuffing
• Physical layer coding violations


---------------------------------------------------------------------------------------------------------------------------------


4. Method 1: Byte Count
- A field in the frame header contains the number of bytes in that frame.
- The receiver reads the count to determine how many bytes belng to the current frame, to know where the next frame begins.
NOTE: The count includes the count byte itself.


4.1) Worked Example
- Consider the following byte stream, with vertical bars added to show intended frame boundaries:
5 1 2 3 4 | 5 6 7 8 9 | 8 0 1 2 3 4 5 6 | 8 7 8 9 0 1 2 3

- The receiver proceeds sequentially:
1) The first byte is 5, so the first frame contains five bytes: 5 1 2 3 4 (Including the count byte 5)
2) The next byte is 5, so the second frame contains five bytes: 5 6 7 8 9 
3) The next byte is 8, so the third frame contains eight bytes: 8 0 1 2 3 4 5 6
4) The next byte is 8, so the fourth frame contains eight bytes: 8 7 8 9 0 1 2 3

LIMITATION:
- If the count field is corrupted during transmission, the receiver may calculate the wrong frame boundary.


---------------------------------------------------------------------------------------------------------------------------------


5. Method 2: Flag Bytes with Byte Stuffing
- Uses a special byte to mark the beginning and end of a frame.
- By using the same special byte for both boundaries the: "flag byte".

The frame Conceputally:
• FLAG
• Payload
• FLAG

- The receiver interpets two consecutive flag bytes as the end of one frame and the beginning of the next.


5.1) The Problem: What if a Flag Appears in the Payload?
- The receiver would interpret it as a frame boundary, cand split the data in the wrond place.
SOLUTION: Byte Stuffing

5.2) Byte Stuffing
- If a flag byte occurs in the payload, the sender inserts a special "escape byte" written as "ESC" before any flag byte.
- If an escape byte itself occurs in the payload, the sender escapes that byte by inserting another ESC.

- The receiver performs the reverse, called "Destuffing". Recognizes an escape byte and treats the following byte as ordinary data.


5.3) Worked Example 1: A Flag Inside the Payload
- Suppose the original payload is:
A FLAG B

- A and B represents ordinary data bytes, and FLAG is a special byte.
- If the sender transmitted this sequence unchanged, the receiver may mistake the "FLAG" inside the payload for a boundary.

THUS: The sender inserts an escape byte:
A ESC FLAG B

- The receiver sees "ESC" and interpets the following FLAG as ordinary data.
- The original payload is recovered correctly.



5.4) Worked Example 2: An Escape Byte Inside the Payload
- Now suppose the original payload is:
A ESC B

- The escape byte itself could confuse the receiver if it appeared unescaped in the data.
THUS: The sender escapes it:
A ESC ESC B

- The receiver interprets the first "ESC" as an escape instruction and treats the second "ECS" as data.
- The original payload is recovered.



5.5) Worked Example 3: Both a Flag and an Escape Byte
Consider the more challenging payload:
A ESC FLAG B

- There are two special bytes in the original data, so both must be escaped:
A ESC ESC ESC FLAG B

1) The first "ESC" is inserted to escape the original "ESC".
2) The second "ESC" represnts the original escape byte as data.
3) The third "ESC" is inserted to escape the original "FLAG" and is treated as data than a frame boundary.


NOTE: Byte stuffin protects frame boundaries by escaping special bytes that appear in the payload.
Then the receiver removes the stuffing to recover the original data.


---------------------------------------------------------------------------------------------------------------------------------


6. Method 3: Flag Bits with Bit Stuffing
- Uses the same basic idea as flag bytes, BUT it operates on bit sequences rather than individual bytes.
- Each frame begins and ends with a special sequence of bits, it may be eight bits long, but does not have to be.

- Bit Stuffing: Prevents a flag bit seqence being read by the receiver as a boundary and instead data.


6.1) The HDLC Example
- The older HDLC protocol uses the following flag pattern:
01111110

- Written as 0x7E in hexadecimal.
Bit-Stuffing Rule: Whenever the sender encounters five consecutive "1" bits in the data, it inserts a "0" bit right afterwards.
THUS: Preventing six consecutive "1" bits from appearing, else the receiver recognizes it as a flag pattern.



6.2) Worked Example: Sixteen Consecutive Ones:
- Consider the original data:
0110 1111111111111111 0010

- The middle porition contains sixteen consecutive "1" bits.
- The sender scans the data and inserts a "0" after each group of five consecutive "1" bits. Resulting, transmitted bit stream:
0110 11111 0 11111 0 11111 0 1 0010

- There are three inserted zeros, Original data contains 24 bits, stuffed data contains 27 bits.

How the Receiver Removes Stuffed Bits?
- The receiver applies the reverse rule.
- Scans the received data, recognizes five consecutive "1" bits followed by a 0. Under the protocol's stuffing rule,
that zero is a stuffed bit, so the receiver removes it.
Destuffing: The receiver removes the three inserted zeros and recovers the original 24-bit data.



6.3) Why Byte and Bit Stuffing Produce Variable-Length Frames
- The final frame length depends on the content of the payload.

NOTE: Bit stuffin prevents a flag pattern from appearing accidentally in data.
Both byte and bit stuffing preserve the original payload but can increase the transmitted frame length.


---------------------------------------------------------------------------------------------------------------------------------


7. Method 4: Physical Layer Coding Violations
- Uses properties of the physical layer's signal encoding to identify frame boundaries.
- Physical layer encodes bits as signals, including redundancy, thus some possible signal cannot legitimately occur within the payload.

- The unavailable sugnal patterns are "coding violations".
THUS: The link layer uses those reserved signals as "fram deliminters".


Three Steps:
1) The physical layer uses an encoding scheme to represent bits as signals.
2) The encoding scheme leaves some signal patterns unavailable for normal payload data.
3) The link layer uses those reserved patterns to mark frame boundaries.

THUS: They do not need to be escaped or removed from ordinary data like stuffing.

NOTE: Physical layer coding violations use otherwise unavailable signal patterns as frame delimiters, avoiding the extra stuffing 
required by the previous methods.


---------------------------------------------------------------------------------------------------------------------------------


8. Using Framing Methods in Combination
- Each method solves the same fundamental problem: Helping the receiver idenfity where a frame begins and ends.
- A link-layer protocol may combine methods when their advantages complement one another.


8.1) Ethernet
- The Ethernet uses 72-bit pattern called "preamble" at the beginning of every frame, followed by a byte count.
The two mechansims perform complementary functions:
• Preamble: Marks the start of the frame.
• Byte Count: Indicates the frame's length, to determine its end.

NOTE: Framing is essential because a continuous bit stream does not inherently reveal packet boundaries. Different protocols can use 
different boundary-marking strategies, or combine them.


---------------------------------------------------------------------------------------------------------------------------------
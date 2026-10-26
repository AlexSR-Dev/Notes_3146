Module 11-1 Part 9 - Error Control at the Link Layer

- In Part 8, we examined the link layer identification of framing.
- Now this part focuses on another responsibility of the link layer: error control.


- As communication channels aren't perfect, during transmission, interference can cause bits to change.
As a result the receiver may lose synchronization and misinterpret the remaining data.

SOLUTION: Two apporaches:
1) Error Dection: Determines that an error has occured, then potentially request that the data be sent again.
2) Error Correction: Use additional information to detect and correct certain errors at the receiver, without retransmission.


---------------------------------------------------------------------------------------------------------------------------------


1. Why Error Control is Needed?
- A transmission error can occur when the data received differs from the data sent.
EX: Bit Flip
• A transmitted 1 arrives as 0.
• A transmitted 0 arrives as 1.

THUS: The link layer needs a mechanism to detect or correct errors during transmission. When the physical layer deliver bits without
guarantee every bit remains unchanged.



1.1) How One Flipped Bit Cascades
- The first byte of a frame specifies the total number of bytes in that frame, including the count byte itself:
5 1 2 3 4 | 5 6 7 8 9 | 8 0 1 2 3 4 5 6 | 8 7 8 9 0 1 2 3

The stram contains four frames:
• Frame 1 has 5 bytes.
• Frame 2 has 5 bytes.
• Frame 3 has 8 bytes.
• Frame 4 has 8 bytes.

- Suppose a transmission error changes the count type of the second frame from 5 to 7.
Thus with a single bit flip. Binary:
• 5 = 00000101
• 7 = 00000111

THUS: The receiver sees the corrupted stream:
5 1 2 3 4 7 6 7 8 9 8 0 1 2 3 4 5 6 8 7 8 9 0 1 2 3


1) Step 1 - The first fram is read correctly.
2) Step 2 - The second frame is misread.
- The corrupted count is not 7, so the receiver believes the second frame contains seven bytes, reading:
7 6 7 8 9 8 0
THUS: Consumes the beginning of the original third frame.

3) Step 3 - The receiver loses synchronization.
- From this point onwards, the receiver may interpret ordinary data bytes as counts and choose incorrect frame boundaries.
THUS: A "loss of frame synchronization".


---------------------------------------------------------------------------------------------------------------------------------


2. Error-Control Information in a Frame
- The link layer can include extra information in a frame's header and trailer for transmission errors.

The Two Functions:
1) Error Detection
2) Error Correction

NOTE: A protocol may provide error dectection alone, or provide both detection and correction.


---------------------------------------------------------------------------------------------------------------------------------


3. Error Detection
- A technique, which additional information is included with transmitted data so the receiver can determine whether certain
transmission errors have occured.

NOTE: The detection does not repair the corrupted data.
INSTEAD: The receiver can request the sender transmit the data again. After recognizing that an error is detected.


Three Common Error-Detecting Codes:

3.1) Parity
- That adds a parity bit to data according to a defined rule.
THUS Allows the receiver to check whether the received bit pattern satisfies the expected parity rule. If not then error.

3.2) Checksum
- Adds a calculated value derived from the data, for the receiver to performa a check on the received information.

3.3) Cyclic Redundancy Check (CRC)
- Provides additional information for the receiver to check.


3.4) When is Error Detetion the Right Choice?
- Useful when errors are rare.
For instance fibre-optic transmission where errors are uncommon.


---------------------------------------------------------------------------------------------------------------------------------


4. Error Correction
- Additional information is included with transmitted data so that the receiver can both detect and correct certain transmission errors.
THUS: No retransmission needed.

Two Common Error-Correcting Codes:

4.1) Hamming Code
- Adds redundant information.

4.2) Reed-Solomon Code
- Also adds redundant information.

NOTE: These codes are more complex than error-detecting codes, and with more information, more overhead.


4.3) When Is Error Correction the Right Choice?
- Useful in transmission media where errors occur often.
For instance: Wireless link
- Prone to transmission errors from noise.
- So instead of paying the cost of retransmission, you sending everything needed by itself.

THUS: Error correction requires more resources for each frame, but it can reduce the need for retransmission when errors
occur frequently.

OVERALL: The choice is a trade between overhead paid always and overhead paid on
failure. Detection is cheap per frame but expensive per error; correction is expensive per
frame but cheap per error. Which wins depends entirely on how error-prone the medium is.


---------------------------------------------------------------------------------------------------------------------------------
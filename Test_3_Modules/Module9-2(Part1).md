Module 9-2 Computer Networks - Part 1 (Intro)

GOALS:
• Define a network in general terms and give examples that are not computer networks
• Explain what a computer network is and describe its main uses
• Explain why a network requires a scheme for uniquely identifying its participants
• Describe what an IP address is
• Read and write IPv4 addresses in dotted decimal notation, and convert between binary and dotted decimal
• Describe IPv6 notation and explain why IPv6 was introduced


---------------------------------------------------------------------------------------------------------------------------------


1. What Is a Network?
- A group or system of interconnected people/things.
- Predates computers and exists in the application of multiple manners.

A few instances of a network can be:
• Postal Network
- To operate, the service requires the origin and the destination. As for the application its sending the item with the recipient's 
address.
• Telephone
- To operate, requires entering the recipient's number, the system identifies it as the intended destiniation and establishes connection,
then the participant can communicate through the network.


1.2) Common Pattern
- Participants must be uniquely identified so a network can direct information to the correct address.

- Such as a Postal Network needs a "postal address" to route items, Telephone Networks requires a "number" to connect a call.


---------------------------------------------------------------------------------------------------------------------------------



2. Computer Networks
- Provides communication between computers a mechanism, enabling the exchange of data.

2.1) What Computer Networking Gives Us (Few or Many examples):
A) Communication and Information Sharing
- Allowing the exchange of information electronically like email, messaging, or broadcasts.

B) Resource Sharing:
- Allowing multiple computers to use shared "hardware" and "software", such as several computers to a single print or an app software.

C) Remote Computing
- Allows Location Independence, thus Geography doesn't restrict the accessibility of resources from physical location.


---------------------------------------------------------------------------------------------------------------------------------



3. Identifying Computers on a Network
- Initially concluded, a computer network needs: "a manner to identify different computers".
- This identification is a: IP Address.


IP Address - A unique identifier for any device connected to the Internet.
- Its purpose (like postal and telephone) to help data reach its intended destination with the addressing information.

APPLICATION:
Suppose a computer requests a webpage from a server.
1. The computer sends a request through the network.
2. IP addressing provides information about the source and destination.
3. Network infrastructure forwards the data toward the appropriate destination.
4. The server processes the request and sends data back.

- Two versions of IP Addresses.


---------------------------------------------------------------------------------------------------------------------------------



4. IPv4 and Dotted Decimal Notation
- IPv4 (Internet Protocal Version 4) is the older version.

- Written in dotted decimal notation for human readability:
: Is four groups of 8-bit numbers separated by dots.
THUS: 32 bits total.


4.1) Converting Binary to Dotted Decimal:
: The following 32-bit binary address:
10101100  00010000  00000000  01100100

Step 1: Convert each group into decimal:
10101100 = 128 + 32 + 8 + 4 = 172

00010000 = 16

00000000 = 0

01100100 = 64 + 32 + 4 = 100


REMAINDER - Each bit is a power of base 2, starting at the right with 2^0:
Bit position	128	64	32	16	8	4	2	1
Binary digit	1	0	1	0	1	1	0	0


Step 2: Join the four decimal values in Dotted Form:
172.16.0.100
- Final converted IPv4 address.

NOTE: Since each group is 8 bits, every number in the address must fall between 0-255 to be valid.


---------------------------------------------------------------------------------------------------------------------------------



5. IPv6
- IPv6 (Internal Protocal Version 6)
- The issue from IPv4 were the limited number of addresses as it can provide approximately 4.3 billion possible total addresses.

- IPv6 addressed the limitation by using 128 bits instead of 32.

5.1) IPv6 Notation:
- IPv6 address is written as 8 groups of four hexadecimal digits separated by colons.

EX: 2001:0DB8:0000:0000:0000:ff00:0042:8329
- Each group contains 4 hexdecimal digits. A hexadecimal represents four binary bits:
• 4 hexadecimal digits represent 16 bits.
• 8 groups represent 128 bits.


---------------------------------------------------------------------------------------------------------------------------------


6. Finding Your Own IP Address
- The simplistic method is searchinng the web for you public IP address, that sees the request origination.
NOT RECOMMENDED: Crude.
REASON:
- The address like a site report is the address your traffic appears to originate from as seen from the outside,
which is frequently not the address configured on your own machine.


---------------------------------------------------------------------------------------------------------------------------------
Module 12-2 Part 15: Internetworking, IPv4, and IPv6

- How computers communicate across networks that use different technologies, addressing schemes, and services.
GOALS:
- Understand why internetworking is necessary.
- Recognize the differences that make connecting networks difficult.
- Explain how a common network-layer protocol allows communication across different networks.


---------------------------------------------------------------------------------------------------------------------------------


1. Internetworking

1.1) Homogeneous Network Versus Real Networks
- Some routing algorithms assume a homogeneous network in which all nodes are similar and use common protocols from all layers.
HOWEVER: In reality, real network environments are composed of different technologies, protocols, addressing schemes, and packetsize limits.

1.2) How do Networks Differ?
a) Services
- Networks may provide different communication services like Connectionless service (packets sent prior to connection) or 
Connection-oriented service (Connection is established prior to communication).

b) Addressing
- Different addressing schemes may be followed.
EX:
- Address Size: The number of bits available to represent an address.
- Flat addressing: Identifies nodes within a numbering scheme.
- Hierarchical addresssing: Levels of organization that represent groupings

c) Packet Size
- Network can impose different limits on the packet or frame size they can carrt. Often due to its technology capacity.

d) Quality of Service
- Networks have different sertice guarantees.
EX: One network distinguish priority packets from ordinary packets and another network doesn't.


---------------------------------------------------------------------------------------------------------------------------------


2. One Solution - Cerf and Kahn
- A common network-layer protocol to enable communication across different physical and link-layer technologies.

THUS: In the Internet it would be the Internet Protocol (IP).
- The IP packet provides a common network-layer format even when the packet travels over different underlying networks.

THUS: Network-layer approach provides a consistent packet format and addressing framework.

RESULT:
- Network layer constructs an IP packet
- The link layer type adds its own header and transmits the packet.

- Packet reaches a router, that link layer of a different type removes the heading and sent to the network layer to be processed.
- And the cycle repeats until the destination is acheived.


---------------------------------------------------------------------------------------------------------------------------------


3. Internet Protocol (IP)
- A common network-layer protocol that allows communication across different types of networks possible.
THUS: The foundation of Internet's internetworking architecture.
- Provides a shared basis for packet addressing and forwarding.

- Provides connectionless and best-effort service.
THUS: No reliability nor ordering guarantees.

Two Types of IPs:
- IPv4
- IPv6


---------------------------------------------------------------------------------------------------------------------------------


4. IP Version 4
- Widely used, includes an IPv4 header with each network-layer packet prior to being passed to the link layer. That header contains
information for processing and forwarding the packet.

NOTE: Address Size of the header is 32 bits or 4 bytes.

4.1) IPv4 Header: General Structure
- The IP version and header structure.
- The packet's size.
- Fragmentation.
- How long the packet can remain in the network.
- The transport-layer protocol that should receive the payload.
- Error detection.
- Source and destination addresses.
- Optional functionality.

b) Header Length (IHL)
- Specifies the length of the IPv4 header in units of 32-bit(4 bytes) words.
THUS: MIN; 5 words * 4 bytes = 20 bytes and MAX; 15 words * 4 bytes = 60 bytes.

c) Service Class and Explicit Congestion Notification
- Discussed in Part 14 with ECN.

d) Total Length
- Specifies the length of the entire IPv4 packet, including both the header and the data.
- At a maximum of 64 KB = 65,535 bytes

e) Packet Identification and IPv4 Fragmentation
- The packet-identification field helps identify fragments that belong to the same original IPv4 packet.
- This field becomes important because networks may have different maximum packet sizes.
- When a packet is too large to pass through a network and fragmentation is permitted, it can be divided into smaller pieces called 
fragments. The fragments must carry information that allows the destination to determine which original packet they belong to.

f.1) Don't Fragment (DF)
- When DF is set, a router must not fragment the packet. Else dropped.

f.2) More Fragments (MF)
- The MF flag indicates whether more fragments of the original packet follow.

f) Fragment Offset
- The fragment-offset field identifies the position of a fragment within the original packet's data.

g) Time to Live (TTL)
- Limits how long a packet can continue travelling through the network.
- When the counter decrements at each hop, at zero its dropped.

h) Protocol Field
- The protocol field identifies which transport-layer protocol should receive the packet's payload.

i) Header Checksum
- The IPv4 header checksum is used for error detection in the header.

j) Source and Destination Addresses
- The source and destination address fields identify the packet's originating and intended destination IP addresses.

k) Options
- The IPv4 options field supports additional functionality beyond the basic header fields.
EX: Security and routing.

┌──────────────────────────────────────────────────────────────┐
│                       IPv4 HEADER                            │
├──────────────────────────────────────────────────────────────┤
│ Version │ Header Length │ Service Class / ECN                │
├──────────────────────────────────────────────────────────────┤
│ Total Length                                                 │
├──────────────────────────────────────────────────────────────┤
│ Identification                                               │
├──────────────────────────────────────────────────────────────┤
│ Flags: DF, MF │ Fragment Offset                              │
├──────────────────────────────────────────────────────────────┤
│ Time to Live (TTL) │ Protocol │ Header Checksum              │
├──────────────────────────────────────────────────────────────┤
│ Source Address (32 bits)                                     │
├──────────────────────────────────────────────────────────────┤
│ Destination Address (32 bits)                                │
├──────────────────────────────────────────────────────────────┤
│ Options (Optional)                                           │
├──────────────────────────────────────────────────────────────┤
│ Data / Payload                                               │
└──────────────────────────────────────────────────────────────┘


---------------------------------------------------------------------------------------------------------------------------------


5. Keeping Up with Growth
- The limination of IPv4 is that it cannot keep up with the growing size of the Internet because its address space is limited.
THUS: With 32-bit address, the theoretical number of distinct address values would be: 4,294,967,296.

New Version: IPv6
- Uses 128-bit addresses, thus with 3.4 x 10^38 possible values.

5.1) IPv6's Simpler Header
- Reduces the overhead needed to process the header.
- Provides better support for opetions through its extension-header mechanism..

5.2) Difficult with IPv6 Deployment
- IPv4 and IPv6 are both Internet Protocol versions, but they are not identical. Their addressing schemes differ, and their packet 
headers have different structures.

THUS: a system that supports IPv4 cannot automatically be assumed to process IPv6 packets in the same way.


IPv6 Header General Structure:
| Field                   | Purpose                     | Key distinction |
|-------------------------|-----------------------------|-----------------|
| Version                 | Identifies the IP version   | IPv6 uses version 6. |
| Differentiated services | Supports service classification and treatment | Relates to service requirements. |
| Flow label              | Groups packets with the same requirements | Identifies a flow, not an endpoint. |
| Payload length          | Indicates the length following the required IPv6 header | Does not include the required header itself. |
| Next header      | Indicates the following extension header or upper-layer protocol | Connects the header chain to the next protocol. |
| Hop limit               | Limits the number of router hops | Performs the same general function as IPv4 TTL. |
| Source address          | Identifies the source endpoint | Uses a 128-bit address. |
| Destination address     | Identifies the intended destination endpoint | Uses a 128-bit address. |


┌──────────────────────────────────────────────────────────────┐
│                       IPv6 HEADER                            │
├──────────────────────────────────────────────────────────────┤
│ Version │ Differentiated Services │ Flow Label               │
├──────────────────────────────────────────────────────────────┤
│ Payload Length │ Next Header │ Hop Limit                     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│              Source Address (128 bits)                       │
│                                                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│           Destination Address (128 bits)                     │
│                                                              │
├──────────────────────────────────────────────────────────────┤
│ Optional Extension Headers (When Needed)                     │
├──────────────────────────────────────────────────────────────┤
│ Data / Upper-Layer Payload                                    │
└──────────────────────────────────────────────────────────────┘

5.3) Optional IPv6 extenstion headers  can provide additional functionality
EX:
: Hop-by-hop options - Miscaellaneous Information for routers
: Destination options - Additional information for the destination
: Routing - Loose list of routers to visit
: Fragmentation - Management of datagram fragments
: Authentication - Verification of the sender's identify
: Encryted Sercurity Payload - Information about the encryted contents


---------------------------------------------------------------------------------------------------------------------------------
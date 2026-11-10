Byte Stuffing:

Q1) Assume the link layer uses the "flag bytes with byte stuffing" approach for framing.
- Beginning of a data frame, with special flag bytes: START
- End of a data frame, with special flag bytes: END
- Special escape bytes: HOW

- Data framed by the link layer on the sender's side:
ITSNOTHOWFASTYOUSTARTBUTHOWWELLYOUENDTHATMATTERS

IMPLEMENT:
START
ITSNOTHOW
HOWFASTYOUHOW
STARTBUTHOW
HOWWELLYOUHOW
ENDTHATMATTERS
END
RESULT: STARTITSNOTHOWHOWFASTYOUHOWSTARTBUTHOWHOWWELLYOUHOWENDTHATMATTERSEND



Q5) Flag bytes with byte stuffing
- Beginning, special flag bytes: BEG
- End, special flag bytes: END
- Special escape bytes: MIS

- Data received by the link layer of a computer:
BEGWHENYOUMAKEAMISMISTAKEENDBEGYOUMISBEGINTOLEARNMOREFROMYOURMISMISTAKEEND

Frame # | Original Data
1       | WHENYOUMAKEAMISTAKE
2       | YOUBEGINTOLEARNMOREFROMYOURMISTAKE
3       | N/A
4       | N/A

NOTE: When you reach a "END" flag without an escape that would be the end a frame.
Also, a "BEG" flag would be the start of a new frame.



---------------------------------------------------------------------------------------------------------------------------------


Bit Stuffing:
Q1) Assume that the link layer uses "flag bits with bit stuffing" approach for framing.
- Beginning & end of data frame, special flag bits: 011110
- Thus, to avoid ocurrence of flag bits, a 0 bit is stuffed every 3 consecutive 1's in the data stream.
NOTE: You stuff one consecutive less than the special flag provide, example: 01111110 flag, should stuff at 5 consecutive 1's.

- Bit stream needed to be framed by the link layer on the sender's side:
011010111110111110011001111001101011111001001111

IMPLEMENT:
(011110)
011010111(0)
110111(0)
11001100111(0)
10011010111(0)
1100100111(0)
1
(011110)

RESULT: 01111001101011101101110110011001110100110101110110010011101011110



Q2) Flag buts with bit stuffing.
- Beginning & end data frame, special flag bits: 01111110
- THUS, to avoid occurrence of flag bits, 0 bit is stuffed for every consecutive 1's in the bit stream.

- Bit stream received by the link layer of a compuer:
011111100111010101111101101111000010101111100001011100101001111110

REMOVE:
(01111110)
01110101011111(0)
11011110000101011111(0)
00010111001010
(01111110)

RESULT: 011101010111111101111000010101111100010111001010



---------------------------------------------------------------------------------------------------------------------------------


Dijkstra Algorithm
NOTE:
• Distance is the the "shortest" known distance from the source node.
THUS: For every interconnecting node the distance increments base on the new distance + old distance.
• From (Subsequent) is the "predecessor node" from the current node.
• Finalized is the verified "shortest distance path" from the source node.

NOTE:
- For nodes that are connected to the current or past node the details would be:
Distance: "inf", From: "-", Finalized?: "No"
ALSO: If the current node connects to an existing node, whose node was defined but not finalized, you can do a comparion if:
new distance < old distance. Thus you can update the old node's distance if the comparison is met.

- Also to decide on the next node, choose the lowest distance.
- If there are multiple lowest, then use the tie breaker indicated on the test.

LASTLY: Check the the incrementation to that node pathway is accurate.
- Careful on the "arrows" it can go on one or both directions.

---------------------------------------------------------------------------------------------------------------------------------


Networking Basics:

Network Topologies:
• Mesh: growth increases the number of direct links rapidly.
• Bus: growth increases competition for a shared medium.
• Ring: growth may increase the number of intermediate nodes on a path.
• Star: growth increases dependence on the central device and its capacity.
• Tree: growth can be distributed across branches, although higher-level links remain important.


- First-Level Domain (TLD): The last segment of the domain name (.com, .org, etc.).
- Second-Level Domain (SLD): The unique name (example in example.com).
- Third-Level Domain (Subdomain): The label that organizes the site (www.).

GOALS of Network Software:
- Reliablility
- Security
- Resource Sharing
- Scalability


Layered Approach:
- Separate organized levels called layers.
- Each performs its responsibilites without requiring the layers above it to understand the internal operation (abstraction).
- Communication is only adjacent, with interfaces(protocols) for each.


OSI (Open System Interconnection) Model: Strength Layer Model Functions, but not protocols.
Seven Layers:
- Application
: User interface (data).
- Presentation
: Formatting data (encryption, etc).
- Session
: Services for connections between sender and receiver.
- Transport
: Communication between the two nodes being delivered (Flow Control & Congestion).
- Network
: Transfer of data through a network. (Routing, Forwarding, Congestion Control).
- Data Link
: Transmission of data over a single link between two nodes. (Error Control and Flow Control).
- Physical
: Network hardward that transmits/receive raw bits over physical communication.


TCP (Transmission Control Protocol) /IP (Internet Protocol) Model: Strength: Protocols
Four Layers:
- Application
: Combines (Application, Presentation, and Session). Protocols: HTTP, STMP, FTP.

- Transport
: End-to-end delivery without underlying networks being implemented. Protocols: TCP & UDP (User Datagram Protocol).

- Internet
: Similar to network layer, sends data across the network & determine the path through routing. Protocol: IP
: Unreliable as the focus is transmission, more reliability is provided by higher layers like transport.

- Link
: Combines Data Link and Physical layer "moving data units over a single link between two hosts", and providing the "interface between 
hosts and transmission links." Protocol: Local Network Topology, Ethernet, Coxial cable.


Five Layer Hybrid
5) Application
- Provides user services
4) Transport (Firewalls, proxy servers)
- Provides end-to-end message delivery
3) Network
- Transfers data units between source and destination
2) Link
- Transfers frames over a single link
1) Physical
- Transfers raw bits over the physical medium.

Protocol Data Units (PDUs) of each Layer:
• Application Layer - Application protocol data unit (APDU) (The message)
• Transport Layer - Segment
• Network Layer - Packet
• Link Layer - Frame
• Physical - Bits


---------------------------------------------------------------------------------------------------------------------------------

WIRESHARK
PING: Test the reachability of a host on an IP network to measure the round-trip time for messages from host to destination and back.
- Sends a ICMP echo request.
DISPLAYS:
SIZE OF PACKET, IPv4 Addresssion of target host, sequence number of packets, TTL (Time to Live), and round-trip time(RRT) in milsecs.

EX: MAC: 422:00:aa:00:00:00


TRACEROUTE: Trace the route that packets take from the local host to a destination host/server in a computer network.
- Uses TTL values through each node until it either reaches its destination or reaches zero
DISPLAYS:
Hop Number (sequence of routers encountered from 1), IP address (hostname of router), RRT for each packet sent to that router (min/avg/max).


---------------------------------------------------------------------------------------------------------------------------------

Networking - Link Layer
1. Connection-Oriented Service
- Also known as connection-based service, establishes a logical connection between the source and destination computers
before transferring data.
- The logical connection represents the intention of communication to begin data exchanges.
ENTIRE PROCESS: Establish -> Transfer Data -> Release


2. Connection-Less Service
- Does not establish a connection between the source and destination before sending data.
- Instead the source sends each data unit when needed. Every unit is handled independently of the other units.


: Reliable Service
- Aims to ensure the accuracy and integrity of communicated data.
- All this consumes resources and generates additional traffic.

: Unreliable Service
- Sends data without checking whether it was correctly received.
THUS: Sender does not require the receiver to acknowledge each transmission. Lost, Delay, Corrupted.



- The Link layer's job is to carry a unit of data across a single link.
- But proposes another issue, the physical layer cannot solve, it delivers a continuous stream of bits, but the receiver needs to
know where one unit ends and the next begins.
SOLUTION: Framing


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


FRAMING:
SOLUTION:
- Encapsulation of each packet received from the network layer into a frame.
THUS: The link layer encapsulates each packet given to it by the network layer into a frame. The frame itself consists of:
• Header
• Packet
• Trailer

IMPLEMENTATION METHODS:
• Byte Count
• Flag bytes with byte stuffing
• Flag bits with bit stuffing
• Physical layer coding violations


- Error-Control Information in a Frame
- The link layer can include extra information in a frame's header and trailer for transmission errors.

The Two Functions:
1) Error Detection
- A technique, which additional information is included with transmitted data so the receiver can determine whether certain
transmission errors have occured.
2) Error Correction
- Additional information is included with transmitted data so that the receiver can both detect and correct certain transmission errors.
THUS: No retransmission needed.


ARP - Network protocol used to find the MAC addresss when the IPv4 is known.
MAC Address - Required for communication over an ethernet-based (LAN).

TWO Applications:
- Layer 3 Network - Uses IP address to identify device on the Network.
- Layer 2 Data Link - Uses MAC address to deilver frames to the correct device on the local network.
---------------------------------------------------------------------------------------------------------------------------------

WIRESHARK:

ARP entries: arp - a
TO add a static ARP entry: arp -s IPv4 MAC
TO remove: arp -d IPv4

---------------------------------------------------------------------------------------------------------------------------------


3. Goals of a Routing Algorithm
- Determines which route should be selected when multiple possible paths exist between a source and a destination.
- Must satisfy several performance and operational goals:

3.1) Correctness:
- Routing algorithm should deliver packets to their intended destinations rather than directing them away.

3.2) Simplicity:
- Should have low overhead.

3.3) Efficiency:
- Should improve network performance by minimizing transmission delay, maximizing throughput, or optimizing comparable performance.

3.4) Robustness:
- Should cope with changes in network topology(network arragnement) and traffic(data being transmitted).

3.5) Stability:
- Should converge to its routing decision within a reasonable amount of time.

3.6) Fairness:
- Should treat different nodes fairly.

NOTE:
- Of the six goals, they can conflict with one another.
THUS: Practical routing algorithm must balance its objectives rather than maximizing all six simulatenously.


4. Types of Routing Algorithms
- Falls into two categories:

4.1) Non-Adaptive (Static) Routing:
- A routing algorithm that uses routes that are fixed offline and stored in routing tables. In network operation, the router consults
at the table to determine where to forward a packet.

THUS: Static routing relies on routes established in advance and stored in tables.


4.2) Adapative (Dynamic) Routing:
- A routing algorithm that changes routes to reflect changes in the state of the network.

For instance changes to the topology or traffic.
- This routing algorithm would typically work towards one or more optimization criteria.

THUS: Is better at accommodating the goal of robustness, at the cost of simplicity and stability.


1. What the Network Layer Does
- Its primary objective is to transfer a packet from its source to its final destination, possibily through multiple network links.

NOTE:
- The link layer transfers data across a single link between two adjacent nodes.
THUS: The link layer handles transmission across each individual link. Meanwhile the network layer is concerned with end-to-end delivery.
Thus, lower layers (link) deals with individual steps to transmit data across particular links rather than selecting and managing the entire route.


- The Three Responsibilities of the Network Layer:
1.1) Routing and Forwarding
- Two distinct operating:
• Routing - Identifies the path the packet should take from its source to its destination.
• Forwarding - The process that an intermediate node sends a packet to the next node along the selected path.

THUS: Routing decides the route, forwarding executes it, one node at a time.


1.2) Congestion Control
- The network layer's responsibility to avoid network congestion and recover from it when it occurs.
EX: Computers sending packets through the same router at the same time. Results in longer delays, packet drops, and reduced throughput.


1.3) Internetworking
- Supporting the connection of multiple individual networks to operate as a larger network of networks.
THUS, an interconnection rather than a single system.

TWO REQUIREMENTS:
1) Uniform addressing: Must be provided across the network of networks. Allowing a computer to be addressed consistently
regardless of which constituent network it belongs to.

2) Hide individual network details: From the transport layer, such as physical network type, network topology, etc.
THUS, the transport layer must specify a computer's address while not managing the internal structure of every network the data crosses.


OVERALL: The network layer provides consistent manner of communication across different networks, while their hiding internal
differences from higher layers.




NOTE: Network Layer aspects are predominant

1. Congestion
- Occurs when a network receives more packets than it can handle.
AS a RESULT:
- Excessive delays: Packets to travel through the network.
- Packet drops: Routers may discard packets, when they cannot accommodate additional traffic.
- Overloaded network resources: Network devices can't process the volume of incoming traffic.


---------------------------------------------------------------------------------------------------------------------------------


2. Congestion Control
- Prevents or manages congestion conditions so the network operates efficiently.

2.2) Layers Responsible for Congestion Control
- Both the transport layer and network layer share the responsibility.

2.3) Three General Approaches to Congestion Control
a) Traffic-aware routing - Direct traffic away from congested areas.
b) Traffic Throttling - Reduce the load placed on the network.
c) Provisioning - Increase the network's ability to accommodate traffic


3. Traffic-Aware Routing
- A routing approach that considers the load and directs traffic away from hotspots (already/soon to be congested areas).

CONSIDERS:
- Network Topology: Which routes are available.
- Current Traffic conditiosn: How heavily loaded are those routes.


EX:
Path CF: A heavily loaded route.
Path EI: A lightly loaded route.

THUS: Approach identifies
1) Path CF becomes congested.
2) Routing mechanism detects its traffic exceeds a threshold.
3) Traffic is redirected from CF to EL.
4) Network attempts to reduce the load on CF using less loaded alternative.

APPROACH ISSUE: Oscillation


3.1) Issue: Oscillation
- When routing decisions repeatedly shift traffic between paths, causing congestion to move from one path to another.

EX:
- CF becomes congested. The routing mechanism identifies it as a hotspot.
- Traffic shifts to EI. Routers redirect traffic away from CF.
- EI becomes congested. Too much traffic has been redirected to the alternative path.
- CF becomes lightly loaded. Because traffic has moved away, CF may no longer appear congested.
- Traffic shifts back to CF. The routing mechanism may redirect traffic to CF again.
- The cycle repeats. Congestion can continue shifting between the two paths.

THUS: Redirecting to much traffic at once can overload the alternative route.


3.2) Solutions
a) External Traffic Engineering
-  Examines the broader network conditions and adjust routing policies gradually, instead of an immediate swtich of all traffic.
THUS: Gradually Change.


b) Distribute Traffic
- Using multiple available paths simultaneously.
EX: Send some traffic through CF and the remaining through EI.
THUS: Prevents one path from becoming overloaded.


6. Load Shedding
- An approach in which routers drop packets in the event the network is overwhelmed and other approaches aren't enough.
THUS: A last resort.

6.1) Which Packets should be Dropped?
- Depends on the app using the network. As different apps place different values on data.
EX1) File Transfer
- An older packet would be more valuable than a newer packet because missing early data prevents the destination from having a
complete sequence. Thus, order would matter.

EX2) Real-Time Streaming
- Receiving old data may becoming less useful and stale, than receing current data. Like a movie.
THUS: Newer packets over older.







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


ICMP (Internet Control Message Protocol) and UDP (User Datagram Protocol) are both non-connection-oriented protocols used in networking, but they operate at different layers and serve completely different purposes.

Key Differences

• OSI/TCP-IP Layer:
	• ICMP: Operates at Layer 3 (Network Layer). It is encapsulated directly inside IP packets.
	• UDP: Operates at Layer 4 (Transport Layer). It sits on top of IP to deliver data to specific applications.
• Port Numbers:
	• ICMP: Does not use port numbers because it does not connect to an application or service.
	• UDP: Uses specific port numbers (e.g., UDP port 53 for DNS) to route data to the right application on a host.
• Primary Purpose:
	• ICMP: Used for network diagnostics, status messages, and error reporting—most famously for tools like ping (echo requests/replies) and traceroute.
	• UDP: Used to send fast, lightweight application data where low latency matters more than guaranteed delivery or strict ordering (e.g., live video streaming, VoIP, and online gaming).
• Data Payload:
	• ICMP: Not meant for general application data exchange; it carries control messages and diagnostic feedback.
	• UDP: Designed to transport actual application payloads quickly without a handshake
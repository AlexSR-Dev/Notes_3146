Module 12-1 Part 12: The Network Layer and Routing Basics

- Identify the distinction between the link layer and the network layer.

GOALS:
• Explain the purpose of the network layer and why it is the lowest layer concerned with end-to-end delivery.
• Describe the network layer's three primary responsibilities: routing and forwarding, congestion control, and internetworking.
• Distinguish routing from forwarding.
• Explain store-and-forward packet switching and how packets move through the protocol layers at intermediate nodes.
• Identify and explain the goals of a routing algorithm.
• Distinguish non-adaptive (static) routing from adaptive (dynamic) routing.


---------------------------------------------------------------------------------------------------------------------------------


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


---------------------------------------------------------------------------------------------------------------------------------


2. Routing Basics
- Identifying a path between the two endpoints of a communication.

- Explains what occurs to packets at every node.

2.1) Store-and-Forward Packet Switching
Step 1: The link layer receives the frame.
- When a frame arrives at a node, the link layer receives the transmission. Waits until the entire frame arrives.
Frame - Unit of data handled by the link layer and carries the packet the network layer needs to process.

Step 2: The link layer checks the frame.
- When the complete frame arrives, the link layer verifies its integrity using the "error-control" mechanisms.
- To detect whether the received frame has been corrupted during transmission.

Step 3: The packet is passed to the network layer.
- Link layer passes the encapsulated packet to the network layer.
- Network layer determines which outgoing link should be used to move the packet toward its destination.

Step 4: The packet is forwarded to the next node.
- Networ layer passes the packet toward the appropriate outgoing link. Then the link layer handles transmission across that link.


NOTE: This whole process repeats at subsequent intermediate nodes until the packet reaches its destination.
As the name implies the behavior of each node, store the complete frame, prior to forwarding.



2.2) How Far Up the Protocol Stack Does a Packet Travel?
• At an intermediate node, the packet travels up to the network layer.
• At the destination node, the packet is passed up to the transport layer.

THUS: Intermediate nodes only needs up to the network layer for ordinary routing and forwarding.


2.3) Example Network: Following a Packet from H1 to H2
The diagram contains:
- H1: The source host on the left.
- P1: A process running on H1.
- H2: The destination host on the right.
- P2: A process running on H2.
- Routers A, B, C, D, and E: Routers inside the ISP's equipment network.
- Router F: A router outside the ISP network that connects toward H2.
- Links: Connections between the routers and hosts.
- ISP: Internet Service Provider.

Step 1: H1 sends a packet
- Passes through router A.

Step 2: Router A chooses the next router
- From A, the packet can travel to either B or C.
- Routing decision determines the path, while forwardin sends the packet along that path.

Step 3: Router C forwards the packet to E.
Step 4: Router E forwards the packet to F.
Step 5: Router F connecst the packet to H2.

THUS:
- Multiple routes can exist.
- Networks can cross ownership boundaries.
- Forwarding occurs at individual routers.
- Network layer provides end-to-end delivery.


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


---------------------------------------------------------------------------------------------------------------------------------


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


---------------------------------------------------------------------------------------------------------------------------------


5. Routing Algorithm Examples
- Approaches

5.1) Shortest Path Routing
- Selects a route according to a path-length or path-cost criterion.
-  Based on a measure of path cost or length.

5.2) Flooding:
- A packet is forwarded across multiple outgoing links according to the algorithms forwarding rules, rather than
being restricted to a single preselected path.


5.3) Distance Vector Routing
- Routers maintaining information about distances or costs to destinations and exchanging routing information with neighboring routers.

5.4) Link State Routing
- Routers build information about network links and use that information to determine routes.

5.5) Hierarchical Routing
- Organizes routing responsibilities or network destinations into levels or groups.

---------------------------------------------------------------------------------------------------------------------------------
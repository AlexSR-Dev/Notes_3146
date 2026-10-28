Module 12-2 Part 14: Congestion Control

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
c) Provisioning - Increase the network's ability to accommodate traffic.


---------------------------------------------------------------------------------------------------------------------------------


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


---------------------------------------------------------------------------------------------------------------------------------


4. Traffic Throttling
- Routers "determine" impending congestion and "notify" senders that they should reduce their transmission rates.
NOTE: Routers can monitor their queues, in which "determination" and "notification" occurs based on the status of that queue.

REDUCTION APPROACHES:
4.1) Choke Packets
- Is a packet sent to a "sender" to notify it that it should reduce its transmission rate.

4.2) Explicit Congestion Notification (ECN)
- Marks an existing packet rather than send a separate choke packet.

PROCESS:
- A sender transmits a packet through the network.
- The packet passes through a congested router.
- The router sets an appropriate congestion-notification flag in the packet.
- The marked packet continues toward its destination.
- The destination recognizes that congestion has been indicated.
- The destination informs the original sender through an acknowledgement.

THUS: This acknowledgement provides a notification to the sender from the destination of congestion indications.


---------------------------------------------------------------------------------------------------------------------------------


5. Admission Control
RECALL: Connection-oriented service, a sender establishes a connection with a destination before sending a stream of packets
THUS: This approach, determines whether a new connection should be accepted into the network. If the network is congested it will be
refused from being the new connection.

PROCESS:
- The sender requests a connection.
- The network considers its current traffic conditions.
- The network determines whether it can accommodate the new connection.
- If the network can accommodate the connection without causing congestion, the connection can be accepted.
- If the network cannot accommodate the connection without congestion, the connection can be refused.


5.1) How a Network Determines if a new connection causes Congestion?
- Requires a manner to represent the current traffic state.
- Possible approaches: Leaky bucket approach.


---------------------------------------------------------------------------------------------------------------------------------


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


---------------------------------------------------------------------------------------------------------------------------------
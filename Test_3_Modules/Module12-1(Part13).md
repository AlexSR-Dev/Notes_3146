Module 12-1 Part 13: Shortest Path Routing and Dijkstra's Algorithm

- Covers a routing algorithm approach: Shortest Path Routing.

GOALS:
• Explain what “shortest” can mean in shortest path routing
• State what Dijkstra’s algorithm computes
• Execute Dijkstra’s algorithm by hand on a small network, building the table row by row
• Read a completed table to recover both the distance and the actual path to any node
• Explain hierarchical routing and why it is needed
• Explain when routing decisions are made under connection-less and connection-oriented service


---------------------------------------------------------------------------------------------------------------------------------


1. Shortest Path Routing
- Routing method that directs packets along the shortest path between a source and a destination node.
- Calculating shorteset paths are stored in a routing table for use when forwarding packets.


1.1) What Does "Shortest" Mean?
- Shortest is not just physical distance, but on how the network measures the cost of a route.
Measurements:
• Lowest Number of Hops - The route with the fewest links.
• Shorest Geographic Distance - The route that covers the least physical distance.
• Lowest Average Delay - The route expected to deliver data with the least delay.
• Combination of factors - The route with the lowest combined cost.


NOTE: Each link receives a "weighted distance", or cost to represent the chosen measurements.


---------------------------------------------------------------------------------------------------------------------------------


2. Dijkstra's Algorithm
- Finds the shortest path from a specified source node to every other node in a network.
Not just one detination, but determines the shortest distances to all reachable nodes.

NOTE: A cell in BLUE bold means finalized. THUS, its the established shortest distance to that node and will never change again.


2.1) Example Network
- Eight nodes: A, B, C, D, E, F, G, and H.
- Each connection has a numerical distance. Objective is to calculate the shortest path from the source node A to every other node.

Link Distances:
| Connected nodes | Link distance |
| ---             |---:|
| A–B             | 2 |
| A–G             | 6 |
| B–C             | 7 |
| B–E             | 2 |
| E–F             | 2 |
| E–G             | 1 |
| F–C             | 3 |
| F–H             | 2 |
| G–H             | 4 |
| H–D             | 2 |
| C–D             | 3 |


2.2) How the Table Works
- The algorithms process is recored in a table. Each column represents a node, and each row represents a step in the algoritm.

- Each table entry contains two pieces of information:
A) Distance: The shortest distance currenly known from source A to the node represented by the column.
B) Subscript: Naming the node right before it on the currently known shortest path.

FORMAT: Distance(Predecessor)
EX: 4(B)
- Means the current shortest known distance from A to the node is 4, and the path reaches the node through B.

EXS:
| Symbol or notation | Meaning |
|--------------------|---------|
|   0(A)    | The source node A has distance 0 from itself. |
|   2(A)    | The node is reached from A with a total distance of 2. |
|   4(B)    | The current distance is 4, with B as the predecessor. |
| Infinity  | No path to that node has been discovered yet. |
| Finalized entry | The algorithm has established the shortest distance to that node, so its distance will not change. |



2.3) The Procedure
- Every iteration follows the same three main actions.

Step 1: Choose the next node to explore:
- Among all node that have not been finalized, select the node with the smallest known distance from A.

Step 2: Finalize the Selected Node:
- Copy forward all previously finalized entries, to the newly chosen node to finalized.

Step 3: Update the Distances of its neighboring nodes:
- With (New Distance = Distance to current node + Link Distance).
• If the candidate is smaller, replace the old distance and update the predecessor.
• If the candidate is larger, retain the existing distance.
- All other entries are carried forward unchanged.


---------------------------------------------------------------------------------------------------------------------------------


3. The Complete Trace

3.1) Row A - Exploring the Source
- Initialize the table using the direct connections from the source node A.
The distance from A to itself is 0. From A, there are direct links to B and G.
- A → A: distance 0.
- A → B: distance 2.
- A → G: distance 6.
- All other nodes initially have a distance of infinity.

Table:
| Step | A   | B   | C | D | E | F | G   | H |
|---   |---  |---  |---|---|---|---|---  |---|
| A    | 0(A)| 2(A)| ∞ | ∞ | ∞ | ∞ | 6(A)| ∞ |



3.2) Row B - Exploring B
- B has a known distance of 2 from A, thus the next node to explore.
- B connects to A, C, and E. Since A is already finalized, we focus on C and E.

Update C
- The distance from A to B is 2, and the link from B to C has a distance of 7.
Calculate the candidate distance:
d(C) = d(B) + w(B,C)
d(C) = 2 + 7 = 9
RECORD: C = 9(B)
- The predecessor is B because the candidate route reaches C through B.


Update E
- The distance from A to B is 2, and the link from B to E has a distance of 2.
d(E) = d(B) + w(B,E)
d(E) = 2 + 2 = 4
RECORD: E = 4(B)


Updated Table:
| Step | A    | B    | C    | D | E    | F | G    | H |
|---   |---   |---   |---   |---|---   |---|---   |---|
| A    | 0(A) | 2(A) | ∞    | ∞ | ∞    | ∞ | 6(A) | ∞ |
| B    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | ∞ | 6(A) | ∞ |



3.3) Row E - Exploring E
- E has a distance of 4 from A. With neighnors that are F and G.

Update F
- The link from E to F has a distance of 2.
d(F) = d(E) + w(E,F)
d(F) = 4 + 2 = 6
RECORD: F = 6(E)


Upadte G
- There are two known ways to reach G:
• Directly from A: distance 6.
• Through E: distance 4+1=5.
- Calculate the candidate distance through E:
d(G) = d(E) + w(E,G)
d(G) = 4 + 1 = 5
Compare distances: 5 < 6
- The route through E is shorter, thus replace the previous entry 6(A) with 5(E)

Updated Table:
| Step | A    | B    | C    | D | E    | F    | G    | H |
|---   |---   |---   |---   |---|---   |---   |---   |---|
| A    | 0(A) | 2(A) | ∞    | ∞ | ∞    | ∞    | 6(A) | ∞ |
| B    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | ∞    | 6(A) | ∞ |
| E    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | 6(E) | 5(E) | ∞ |

NOTE:
A -> G
- Has a distance of 6.
A -> B -> E -> G
- Has a distance of 2 + 2 + 1 = 5



3.4) Row G - Exploring G
- G is explored because its distance of 5 is the smallest among the remaining unfinalized nodes.
- Its neigbor is H, connected to G by a link with distance 4.
d(H) = d(G) + w(G,H)
d(H) = 5 + 4 = 9
RECORD: H = 9(G)

Current route to H: A -> B -> E -> G -> H, with a total distance of 9.

Updated Table:
| Step | A    | B    | C    | D | E    | F    | G    | H |
|---   |---   |---   |---   |---|---   |---   |---   |---|
| A    | 0(A) | 2(A) | ∞    | ∞ | ∞    | ∞    | 6(A) | ∞ |
| B    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | ∞    | 6(A) | ∞ |
| E    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | 6(E) | 5(E) | ∞ |
| G    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | 6(E) | 5(E) | 9(G) |



3.5) Row F - Exploring F
- F has a distance of 6 from A. Its neigbors are C and H

Update C
- The link from F to C has a distance of 3.
d(C) = d(F) + w(F,C)
d(C) = 6 + 3 = 9

- C already has a known distance of 9 through B.
COMPARE: 9 = 9.
- The new route does not imporve the existing distance, thus the entry remains: C = 9(B)
- The predecessor remains the same.


Update H
- The link from F to H has a distance of 2.
d(H) = d(F) + w(F,H)
d(H) = 6 + 2 = 8

- H previously had a distance of 9 through G.
COMPARE: 8 < 9
The route through F is shorter, so update: H = 8(F)

Updated Table:
| Step | A    | B    | C    | D | E    | F    | G    | H |
|---   |---   |---   |---   |---|---   |---   |---   |---|
| A    | 0(A) | 2(A) | ∞    | ∞ | ∞    | ∞    | 6(A) | ∞ |
| B    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | ∞    | 6(A) | ∞ |
| E    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | 6(E) | 5(E) | ∞ |
| G    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | 6(E) | 5(E) | 9(G) |
| F    | 0(A) | 2(A) | 9(B) | ∞ | 4(B) | 6(E) | 5(E) | 8(F) |



3.6) Row H - Exploring H
- H has a distance of 8. Its neighbor is D, connected by a link with distance 2.
d(D) = d(H) + w(H,D)
d(D) = 8 + 2 = 10
RECORD: D = 10(H)

Known route to D: A -> B -> E -> F -> H -> D
Total Distance: 2 + 2 + 2 + 2 + 2 = 10

Updated Table:
| Step | A    | B    | C    | D     | E    | F    | G    | H |
|---   |---   |---   |---   |---    |---   |---   |---   |---|
| A    | 0(A) | 2(A) | ∞    | ∞     | ∞    | ∞    | 6(A) | ∞ |
| B    | 0(A) | 2(A) | 9(B) | ∞     | 4(B) | ∞    | 6(A) | ∞ |
| E    | 0(A) | 2(A) | 9(B) | ∞     | 4(B) | 6(E) | 5(E) | ∞ |
| G    | 0(A) | 2(A) | 9(B) | ∞     | 4(B) | 6(E) | 5(E) | 9(G) |
| F    | 0(A) | 2(A) | 9(B) | ∞     | 4(B) | 6(E) | 5(E) | 8(F) |
| H    | 0(A) | 2(A) | 9(B) | 10(H) | 4(B) | 6(E) | 5(E) | 8(F) |



3.7) Row C - Exploring C
- C has a known distance of 9 through B. Its neighbor is D, connected by a link with distance 3.
d(D) = d(C) + w(C,D)
d(D) = 9 + 3 = 12

- D already has a known distance of 10 through H.
COMPARE: 12 > 10
- The route through C is longer, so D remains unchanged: D = 10(H)

Update Table:
| Step | A    | B    | C    | D     | E    | F    | G    | H |
|---   |---   |---   |---   |---    |---   |---   |---   |---|
| A    | 0(A) | 2(A) | ∞    | ∞     | ∞    | ∞    | 6(A) | ∞ |
| B    | 0(A) | 2(A) | 9(B) | ∞     | 4(B) | ∞    | 6(A) | ∞ |
| E    | 0(A) | 2(A) | 9(B) | ∞     | 4(B) | 6(E) | 5(E) | ∞ |
| G    | 0(A) | 2(A) | 9(B) | ∞     | 4(B) | 6(E) | 5(E) | 9(G) |
| F    | 0(A) | 2(A) | 9(B) | ∞     | 4(B) | 6(E) | 5(E) | 8(F) |
| H    | 0(A) | 2(A) | 9(B) | 10(H) | 4(B) | 6(E) | 5(E) | 8(F) |
| C    | 0(A) | 2(A) | 9(B) | 10(H) | 4(B) | 6(E) | 5(E) | 8(F) |



3.8) Row D - Finalizing the Last Node
- D has a known distance of 10, which is the only remaining unfinalized distance after C is explored.
- D is finalized, and the algorithm is complete.

Final Distances from A are:
| Destination | Final distance | Predecessor |
|---          |---:            |---|
| A           | 0              | None; source node |
| B           | 2              | A |
| C           | 9              | B |
| D           | 10             | H |
| E           | 4              | B |
| F           | 6              | E |
| G           | 5              | E |
| H           | 8              | F |


NOTE: A cell in BLUE bold means finalized. THUS, its the established shortest distance to that node and will never change again.

---------------------------------------------------------------------------------------------------------------------------------


4. Reading the Results
- The final row tells us the shortest distance to each destination. The subscripts tell us how to recontruct the actual path
backwards.

EX:
- Shortest Path From A to B
B = 2(A)
- The predecessor of B is A, so the path is direct: A -> B
- The total distance is: 2

- Shortest Path From A to C
C = 9(B)

- Now inspect B: B = 2(A)
- THUS, the path is: A -> B -> C
- Verify the total distance:
A -> B = 2
B -> C = 7
2 + 7 = 9

OVERALL: The final distance tells you the cost of the shortest route, the subscript/predecessor chain tells you which route produces
that cost.


---------------------------------------------------------------------------------------------------------------------------------


5. Hierarchical Routing
- As networks grows, routing table become larger in proportion to the information routers need for destinations. Thus becomes difficult 
to maintain.

Hierarchical Routing addresses this issue by:
- Dividing the network into smaller regions.
- Each router maintains detailed information about its own region.
- Designated routers handle communication with destinations outside that region.

How it works:
Step 1: Organize routers into regions.
Step 2: Maintaing the detailed information with each region.
Step 3: Forward packets destined outside the region.
Step 4: Extend the hierarchy when necessary.
- Several regions form a cluster, several clusters form a zone, etc.

THUS: No router needs to hold details of the entire network, only its own region and where to send everything else.


---------------------------------------------------------------------------------------------------------------------------------


6. When are Routing Decisions Made?
- Depends on the type of service, whether the network uses connection-less service or connection-oriented service.

6.1) Connection-Less Service
- Routing decisions are made independently for every packet.
THUS: Packets travelling from the same source to the same destination may follow different routes.
BUT: The route taken by each packet depends on routing algorithm or network's state when the choice is made.


6.2) Connection-Oriented Service
- Routing decision is made once during the connection establishment phase.
After connection is established, all packets from that connection uses the same route.

NOTE: These describe when routing decisions occur. Does not change the meaning of a shortest path or the
calculations performed by the Dijkstra's algorithm.


---------------------------------------------------------------------------------------------------------------------------------
Module 10-1 Parts 4-6 - The OSI Model, TCP/IP & Wireshark

Introduction of the Two Network Models:
- The previous lesson introduced "layering" the principle of managing the communication between computers.
Part 4 builds upon this principle with two reference models: the OSI reference model, which has seven layers,
and the TCP/IP reference model, which has four layers.

The main goal is understanding which networking responsibility belongs to which layer, how the layers cooperate, and how
data is packaged for transmission.


---------------------------------------------------------------------------------------------------------------------------------


1. The OSI Reference Model:
- OSI (Open Systems Interconnection), divides network communication into seven layers.
This model specifies the function of each layer without a set of "protocols", "services, or interfaces."
THUS: It describes responsibilities, not implementations!
-  Useful as a conceptual framework for studying networking.

- The seven layers from top to bottom:


1.1) Application Layer:
- Provides the user's interface to networking services such as email.
- This layer holds the actual "message/data to be communicated", the lower layers perform the delivery.


1.2) Presentation Layer:
- Handles the "formatting and representation for data" to be communicated.
Can be: Encryption / decryption, compression / decompression, formatting.


1.3) Session Layer:
- Provides services for initiating, maintaining, and ending a connection / session between the sender and receiver computers.


1.4) Transport Layer:
- Provides "end-to end message delivery'. Thus focuses on the communication between the source and destination computers being delivered.
- Two important control functions:
    a) Flow Control - Regulates the rate at which data transferred so that a receiver or communication process is not overwhelmed.
    b) Congestion control - Prevents or manages excessive traffic that overloads the network.


1.5) Network Layer:
- Controls the "transfer of data units through the network" and determines how data moves from the source to the destination,.
- Principal Responsibilities:
    a) Routing - Determines the path that data should take from source to destination.
    b) Forwarding - Passing a data unit to the next node / router along the chosen path.
    c) Congestion Control - Shares responsibility for managing network congestion with the transport layer.


1.6) Data Link Layer:
- Transmits data over a "single link between two nodes."
- Does not need to be the endpoints of the communication, they can be neighboring devices along a longer route.
- Principal Responsibilities:
    a) Error control and flow control over the link.
    b) Determines when the computer has right to access the physical medium. Function handled with the "Medium Access Control (MAC) sublayer."


1.7) Physical Layer:
-  Consists of the basic networking hardware and handles the transmission and reception of raw bits over the physical communication 
channel.
- Principal Responsibilities:
    a) Transmitting and receiving raw bits.
    b) Determining how a physical connection is established.
    c) Determining the mode of transmission.


OVERALL: Participating computer uses the same conceptual seven-layer structure in the OSI model.
- Data travels down the layers on the sender's side and up the layers on the receiver's side.


---------------------------------------------------------------------------------------------------------------------------------


2. The TCP / IP Reference Model
- Has four layers and was originated in ARPANET, a research network sponsored by the U.S. Department of Defense.

- The model is named after two of its primary protocols:
    a) Transmission Control Protocol (TCP)
    b) Internet Protocol (IP)

- Four layers numbered from bottom upward:
4) Application
3) Transport
2) Internet
1) Link


2.1) Application Layer
- The TCP/IP application layer combines functions that the OSI model separates into three layers:
- Application
- Presentation
- Session

- Three widely used application-layer protocols:
• Simple Mail Transfer Protocol (SMTP) - Email
• Hypertext Transfer Protocol (HTTP) - World Wide Web
• File Transfer Protocol (FTP) - Transferring files between computers


2.2) Transport Layer
- Provides end-to-end delivery independently of the underlying network.
Also provides reliability when needed and supoprts "error control, flow control, and congestion control."

- Widely Used Transport Protocols:
• Transmission Control Protocol (TCP)
• User Datagram Protocol (UDP)

APPLICATION: Apps can use transport services to communicate between computers without having to implement all the underlying
network-delivery functions themselves.


2.3) Internet Layer
- Corresponds to the OSI network layer.
- Responsible for sending data units across the network and determining the path they should follow through routing.

- Critical characteristic of this layer it provides "unreliable, or best effort, service." Meaning that the layer does not
guarantee that data may arrive at its destination or within the original order.
REASON: This layer concentrates on moving data across the network, any additional reliablity required by an application can be provided
by a higher layer, like the transport layer.

- Principal Protocol:
• Internet Protocol (IP)


2.4) Link Layer
- Roughly combines the functions of the OSI data link and physical layers.
- Responsible for "moving data units over a single link between two hosts", and providing the "interface between hosts and transmission 
links."

- Principal Protocol:
• Local Network Topology


---------------------------------------------------------------------------------------------------------------------------------


3. Comparing the Models and the Five-Layer Hybrid
• OSI's Strength is its layered model:
- Provides a structured framework for understanding networking responsibilities, excluding the presentation and session layers.

• TCP/IP's Strength is its protocols:
- Identifies the specific protocols central to practical network communication.

- Since these strengths complement each other, the adoption of a five-layer hybrid model is used with OSI's layering discipline
while dropping the two layers mentioned. In addition to TCP/IP protocols used in practical communication.

5) Application
- Provides user services

4) Transport
- Provides end-to-end message delivery

3) Network
- Transfers data units between source and destination

2) Link
- Transfers frams over a single link

1) Physical
- Transfers raw bits over the physical medium.


---------------------------------------------------------------------------------------------------------------------------------


4. Peer Layer Communication
- What happens to data as it travels down the sender's five-layer stack and up the receiver's stack.
REMAINDER: Data does not travel directly from one peer layer to the other. Instead it must be passed down through the sender's
stack, across the network, and up through the receiver's stack.

CONCEPTUALLY:
• Vertical arrows, represents the actual path through adjacent layers.
• Horizontal dashed arrows, represent the logical protocol relationship betweem corresponding layers.


4.1) Going Down the Sender's Stack
- "M" represents the original message and "H_n" represents a header added by layer n. "T_2" for the data link layer's trailer.

a) Layer 5 - Application
- Starts with the message as a single unit, "M".
: M

b) Layer 4 - Transport
- This layer adds its own header "H_4" to the message. This new information is required by this layer's protocol.
: H_4  M

c) Layer 3 - Network
- May divide the incoming data into multiple pieces if the data is too large for a single network-layer unit.
- In this instance, the original message is split into "M_1" and "M_2", each part receives its own network-layer header "H_3".
NOTE: One Transport segment can become multiple network-layer packets.
: H_3  H_4  M_1
: H_3  M_2


d) Layer 2 - Link
- Adds a header "H_2" and a trailer "T_2" to each network-layer unit.
: H_2  H_3  H_4  M_1  T_2
: H_2  H_3  M_2  T_2


e) Layer 1 - Physical
- Transmits the resulting data as raw bits across the physical medium.
- Concerned with the transmission of binary information, than higher-level message structure.



4.2) Comming Up the Receiver's Stack
- The receiving computer, runs the process in reverse. Each layer interprets and removes the information added by its
corresponding layer on the sender's side.

d) Layer 2 - Link: Reads and processes "H_2" and "T_2", then passes the remaining data upward.
c) Layer 3 - Network: Processes "H_3" and reassembles the divided pieces.
b) Layer 4 - Transport: Processes "H_4", handling the transport-layer information.
a) Layer 5 - Application: Receives the original message, "M".

- Each header is meaningful to its corresponding layer. Thus other layers do not need to understand the interal meaning of
that header to perform their own responsibilites. Just like same analogy in Part 3, but instead in the 5-layer model and its protocol 
headers.



4.3) Protocol Data Units (PDUs)
- Is the unit of data associated with a particular networking layer. Since data moves down the stack, each layer handles the unit
provided by the layer above and may add its own protocol information.

Protocol Units of each Layer:
• Application Layer - Application protocol data unit (APDU) (The message)
• Transport Layer - Segment
• Network Layer - Packet
• Link Layer - Frame
• Physical - Bits

NOTE:
- One segment can become multiple packets when the network layer divides it into smaller units. 
- Each packet is then encapsulated in a frame for transmission over an individual link.


---------------------------------------------------------------------------------------------------------------------------------
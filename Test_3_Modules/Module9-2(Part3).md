Module 9-2 Part 3 - Network Software, Layering, and Protocals

GOALS:
• Explain what network software is and identify the source and destination of a communication
• State the main design goals of network software
• Explain what layering means and what each layer provides to the layers around it
• Describe the path data takes down the layers on the sender and up the layers on the receiver
• Explain what a protocol is and why corresponding layers can understand each other without interacting directly
• Use the philosopher-translator-secretary analogy to explain layering, per-layer information, and layer independence


---------------------------------------------------------------------------------------------------------------------------------


1. What Network Software Is
- Specialized software that enables a computer to send and receive data over a network.

Two Roles:
- Sender/Source Computer: The computer that initiates a data transfer.
- Receiver/Destination Computer: The computer intended to receive the data "eventually".

- "Eventually", as data does not travel directly from the sender to the receiver, but passes through several intermediate
devices to the endpoints of communication.


---------------------------------------------------------------------------------------------------------------------------------


2. Design Goals of Network Software
- The following goals are what good network software must accomplish

2.1) Reliablility
- Should operate correctly and provide the expected service.
Thus, communication should not be dependent of unpredictably or incorrect results.
APPLICATION: A file transfer should provide the intended file contents, then being corrupted or mistreated.


2.2) Security
- Should protect data against unauthorized access or other security threats.


2.3) Resource Sharing
- Should make efficient use of the resources available across the network.
APPLICATION: Multiple devices sharing access to a printer, rather tahn duplication.


2.4) Scalability
- Should continue to support communication as the network increases in size.

- If a single monolithic program implemented all these responsibilities, the program would become difficult to develop, test,
maintain, and modify.
THUS: The design approach.


---------------------------------------------------------------------------------------------------------------------------------


3. Network Software Design: The Layered Approach
- Layered design divides the responsibilities of network software into separate, organized levels called "layers".
- Thus less complexity and each layer is responsible for a particular group of related tasks and cooperates with the layers immediately around it.

3.1) Each Layer Abstacts a Set of Services
- A layer performs its responsibilites without requiring the layers above it to understand the internal operation (abstraction).
- Thus apps can use the services provided by the networking system without implementing it itself.


3.2) Each layer provides a well-defined interface
- Interface: How one layer can access the services provided by another.
- The layer above can request a service through that interface without knowing anything about the internals from lower layers.


3.3) Each Layer Interacts Directly Only With Adjacent Layers
- Layer communicates directly with the layer immediately above and below it.

THUS: If one layer depends on internal implementation of every other layer, a change in one area could require changes throughout the system.


---------------------------------------------------------------------------------------------------------------------------------


4. The Layered Approach Across Two Computers
- Computers in communication run corresponding networking layers.
The sending and receiving computers thus have matching layer structure, allowing for compatibility of rules.

4.1) How Data Moves through the Layers
- Sender-side processing: Data starts at an upper layer and moves downward. Each layer performs its responsibilities and passes
the result to the layer below.

- Transmission: The bottome layer sends the data acress the physical medium connecting the computers.

- Receiver-side processing: Data enters the receiving computer's bottom layer and moves upwards. Each layer handles the info
relevant to it and passes the remaining data towards the top layer.


4.2) Protocols
- Each layer has a protocol, an agreed set of rules to interpret and exchange information. Thus a shared language in the same layer.
THUS: A protocal defines the rules for communication between corresponding layers of different devices.
HOWEVER, the layered system provides the actual delivery path.


---------------------------------------------------------------------------------------------------------------------------------


5. The Philosopher-Translator-Secretary Analogy
- Demonstrating how layering, protocols, and independence work together.

5.1) The Setup
- Two philosopers need to communicate:
- Philosopher A: Speaks Hindi and English.
- Philosopher B: Speaks French.

- Translators: Each philosopher has a translator. The translators agree to use Dutch as their common language.
- Secretaries: Each side has a secretary who handles message transmission. The secretaries agree to use fax.

Mapping the Analogy to Network Layers:
Layer 3 - Philosopher
- The source and recipient of the original message.
- NOTE: Communicate own language.

Layer 2 - Translator
- Converts messages between the philosophers' languages and their shared language.
- NOTE: Common protocol is Dutch.

Layer 1 - Secretary
- Handles the practical transmission and receipt of messages.
- NOTE: Communication medium is Fax.


THUS: The order is sequential from the source side from top to bottom, and the receiving side would follow the same responsibilities
but in reverse order.



5.2) Sending a Message
- Suppose Philosopher A wants to send a message to Philosopher B.

1. Layer 3 - The philosopher creates the message.
- Philosopher A composes the message in their own language and is passed down to the translator.

2. Layer 2 - The translator converts the message.
- Translator converts the message into Dutch, per the protocol agreed to. Also, attaches a label to the lanuage used, for the intended
receiving translator.

3. Layer 1 - Teh secretary prepares the transmission.
- Uses the agreed fax protocol with additional fax-related details for transmission. Then message is sent over the physical communication
medium.



5.3) Receiving a Message
- At location B, the same process occurs in reverse.

1. Layer 1 - The secretary receives the fax.
- Secretary understands the fax-related information, removes details that aren't needed and passes the remaining message upward.

2. Layer 2 - The translator interprets the language information.
- Translator reads the label indicating the message is in Dutch, removes the label, and translates the message into French.

3. Layer 3 - The philosophy receives the message.
- Philosopher B receives the message in French and is understood.

NOTE: Added information is called a "header" and the add-then-strip pattern is encapsulation and decapsulation.
THUS: Encapsulation adds layer-specific information on the sending side; decapsulation processes and removes that information
on the receiving side.



5.4) Two Observations

1. A layer's protocol can be opaque to other layers
- The philosophers may use a private code and the translators and secretaries do not need to understand that private code,
only to perform their own assigned functions.
THUS: A protocol at one layer can be opaque to the other layers; in that they does not need to know the protocol's internal meaning
to peform their responsibilities.


2. A layer's protocol can change without requiring other layers to change.
- For instance, changing Dutch to Spanish.
The translator agreed to another common protocol. The philosopher can continue using their own languages, and the secretarties
can continue using fax, provided the translators maintain the required interfaces.

- Or changing faxc to email.
- The secretaries adopt email instead of fax, the rest of the layers continue without change.


- This independence makes the system easier to maintain and evolve.
NOTE: Change must preserve the interfaces and serviuces on which neighboring layers depend, if those changes are incompatible,
other components may require modifications.


OVERALL: Allows for a reliable, secure, and scalable system.


---------------------------------------------------------------------------------------------------------------------------------
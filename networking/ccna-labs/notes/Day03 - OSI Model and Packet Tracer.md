# Day 03 --- OSI Model and Packet Tracer

**Date:** 2026-09-08\
**Course:** CCNA 200-301\
**Lab:** `Day 03 Lab - OSI Model.pkt`

> Today I focused on understanding the TCP/IP and OSI models, how data
> is encapsulated and decapsulated, how PDUs are named at different
> layers, and how Packet Tracer Simulation Mode can be used to observe
> real protocol traffic.

------------------------------------------------------------------------

## Introduction

-   The Internet Protocol Suite, commonly called **TCP/IP**, is a family
    of protocols used for communication across networks and the
    Internet.
-   A networking model groups related networking responsibilities into
    layers so that it is easier to understand **what each protocol does
    and where it operates**.
-   Layering gives us a structured way to reason about network
    communication without needing to understand every protocol at once.
-   The model is a conceptual framework rather than a strict rule: real
    protocols do not always fit perfectly into only one layer.

------------------------------------------------------------------------

## Protocols and Standards

-   A **protocol** is a set of rules that defines how devices
    communicate.
-   Protocols specify things such as message formats, addressing,
    delivery behavior, and how devices interpret received information.
-   Modern networks rely heavily on **vendor-neutral standards**,
    allowing equipment from different manufacturers to interoperate.
-   Important standards organizations include:
    -   **IEEE** --- technologies such as Ethernet and Wi-Fi.
    -   **IETF** --- Internet protocols such as IP, TCP, UDP, HTTP, DNS,
        and many others.
-   The IETF publishes technical standards as **RFCs (Request for
    Comments)**.

------------------------------------------------------------------------

## A Bit of History

-   Early computer-networking work began in the 1960s, including ARPANET
    research funded by the U.S. Department of Defense's ARPA.
-   **Vint Cerf and Bob Kahn** were major contributors to the
    development of TCP/IP.
-   TCP and IP eventually became dominant because the protocols were
    developed and published as open standards rather than being tied to
    a single vendor.
-   The open nature of TCP/IP helped create the interoperable Internet
    we use today.

------------------------------------------------------------------------

# Layered Models

## Why Use Layers?

A layered model separates networking responsibilities into smaller,
easier-to-understand jobs.

Each layer:

1.  Performs a specific function.
2.  Uses services provided by the layer below.
3.  Provides services to the layer above.
4.  Can often be changed or replaced without redesigning the entire
    networking stack.

This separation creates **modularity**.

For example, an application such as HTTP does not need to know the
electrical details of Ethernet cables. Similarly, Ethernet does not need
to understand the meaning of an HTTP request.

------------------------------------------------------------------------

## Analogy: Sending a Letter

The layered approach can be compared with sending a physical letter.

Different parts of the process care about different information:

-   **Content** --- what the message says.
-   **Recipient** --- which person or application should receive it.
-   **Address** --- where the destination is located.
-   **Local delivery** --- how the message is delivered within the local
    area.
-   **Infrastructure** --- the physical path used to transport it.

The important idea is that each part has a different responsibility,
while all parts work together to deliver the final message.

------------------------------------------------------------------------

## Analogy: Building a Layered Model

The analogy can be converted into networking layers:

  Analogy          Networking responsibility
  ---------------- ---------------------------
  Content          Application
  Recipient        Transport
  Address          Internet / Network
  Local delivery   Data Link / Local Network
  Infrastructure   Physical

Depending on the model being used, local delivery and infrastructure may
be combined or separated.

This is why different resources may show a **4-layer TCP/IP model** or a
**5-layer model**.

------------------------------------------------------------------------

# TCP/IP 5-Layer Model

The five-layer model used in today's lab is:

  ------------------------------------------------------------------------------------
                  Layer TCP/IP 5-layer   Main responsibility          Examples
                        name                                          
  --------------------- ---------------- ---------------------------- ----------------
                      5 Application      Application-to-application   HTTP, FTP, DNS,
                                         communication                DHCP

                      4 Transport        Process-to-process delivery  TCP, UDP

                      3 Internet         Host-to-host delivery across IP
                                         networks                     

                      2 Local Network /  Hop-to-hop delivery on a     Ethernet
                        Data Link        local network                

                      1 Physical         Transmission of bits as      Copper, fiber,
                                         signals                      radio
  ------------------------------------------------------------------------------------

### Important naming difference

The course material uses **Local Network** and **Internet** for the
TCP/IP model.

The common adapted 5-layer model often uses:

1.  Physical
2.  Data Link
3.  Network
4.  Transport
5.  Application

These describe essentially the same networking responsibilities, but the
terminology differs.

------------------------------------------------------------------------

# Layer 1 --- Physical

The **Physical layer** is responsible for transmitting and receiving raw
**bits**.

It deals with the physical representation of data, including:

-   Electrical signals.
-   Optical signals.
-   Radio signals.
-   Cables.
-   Connectors.
-   Physical interfaces.
-   Signal levels.
-   Link speeds.

At this layer, the actual contents of the message are not interpreted.
The information is transmitted as a stream of bits over the physical
medium.

In Packet Tracer, Layer 1 information can be seen as the **physical
port/interface** involved in transmitting the data.

------------------------------------------------------------------------

# Layer 2 --- Local Network / Data Link

Layer 2 provides **hop-to-hop delivery** across a local network.

Important concepts include:

-   **Ethernet**
-   **MAC addresses**
-   Ethernet frames
-   Switches
-   Layer 2 headers and trailers
-   Local-network forwarding
-   STP

A **hop** is one step between network devices.

For example:

``` text
PC1 → SW2 → SW1 → R1
```

Each local-network hop can involve a Layer 2 frame.

Switches primarily operate at Layer 2 and use MAC addresses to make
forwarding decisions.

### STP observed in the lab

Packet Tracer Simulation Mode showed **STP BPDUs** moving between the
switches.

The STP traffic demonstrated that:

-   STP operates at Layer 2.
-   Switches exchange control frames.
-   The Layer 2 information contains Ethernet frame details.
-   The physical layer carries the resulting bits over the interface.

------------------------------------------------------------------------

# Layer 3 --- Internet / Network

Layer 3 is responsible for communication between hosts across multiple
networks.

The main protocol is **IP**.

Important concepts include:

-   IP addresses.
-   Source and destination IP addresses.
-   Routing.
-   Routers.
-   Multiple interconnected networks.

Routers primarily operate at Layer 3 and use destination IP information
to decide where packets should be forwarded.

### Lab topology

The Packet Tracer topology used two IP networks:

``` text
192.168.1.0/24
10.0.0.0/24
```

The topology included:

``` text
SRV1 ── SW1 ── R1 ── R2
          │
          SW2 ── PC1
```

Observed addressing included:

-   **SRV1:** `192.168.1.100`
-   **PC1:** `192.168.1.10`
-   **R1:** `192.168.1.1`
-   **R1--R2 network:** `10.0.0.0/24`
-   **R1 on the R1--R2 link:** `10.0.0.1`
-   **R2 on the R1--R2 link:** `10.0.0.2`
-   **PC1 default gateway:** `192.168.1.1`

------------------------------------------------------------------------

# Layer 4 --- Transport

The Transport layer provides communication between **application
processes**.

It uses **port numbers** to identify the destination process.

The two major transport protocols are:

### TCP

TCP provides features such as:

-   Connection-oriented communication.
-   Reliable delivery.
-   Sequencing.
-   Acknowledgments.
-   Flow control.

### UDP

UDP provides:

-   Connectionless communication.
-   Lower protocol overhead.
-   No built-in reliability or ordering.

### DHCP example observed

DHCP uses UDP.

The Packet Tracer capture showed:

``` text
Source UDP port:      68
Destination UDP port: 67
```

This represents a DHCP client sending traffic toward a DHCP server.

------------------------------------------------------------------------

# Layer 5 --- Application

The Application layer defines how application processes format and
interpret network data.

Examples include:

-   HTTP
-   FTP
-   DNS
-   DHCP
-   SMTP
-   SSH

In the 5-layer TCP/IP model, the Application layer combines
responsibilities that the OSI model separates into:

-   OSI Layer 5 --- Session
-   OSI Layer 6 --- Presentation
-   OSI Layer 7 --- Application

Therefore, when looking at Packet Tracer's **OSI Model** information, it
is possible to see the application protocol while the TCP/IP model
conceptually treats it as one Application layer.

------------------------------------------------------------------------

# TCP/IP Model vs OSI Model

The OSI model contains seven layers:

    OSI OSI Layer      Common 5-layer equivalent
  ----- -------------- ---------------------------
      7 Application    Application
      6 Presentation   Application
      5 Session        Application
      4 Transport      Transport
      3 Network        Network
      2 Data Link      Data Link
      1 Physical       Physical

### Key point

The **OSI model is mainly a reference and learning model**, while the
**TCP/IP protocol suite is the practical foundation of Internet
networking**.

------------------------------------------------------------------------

# Encapsulation

**Encapsulation** is the process of adding protocol information as data
moves **down the networking stack**.

Starting with application data:

``` text
Data
  ↓
L4 header + Data
  ↓
L3 header + L4 header + Data
  ↓
L2 header + L3 header + L4 header + Data + L2 trailer
  ↓
Bits
```

Each layer adds information required for its own job.

### Example

An HTTP message can be encapsulated as:

``` text
Application:
    HTTP data

Transport:
    TCP header + HTTP data

Internet:
    IP header + TCP header + HTTP data

Data Link:
    Ethernet header + IP header + TCP header + HTTP data + Ethernet trailer

Physical:
    Bits/signals
```

------------------------------------------------------------------------

# Decapsulation

**Decapsulation** is the reverse process.

When the destination receives the bits:

``` text
Bits
  ↓
Ethernet frame
  ↓
IP packet
  ↓
TCP segment
  ↓
Application data
```

Each layer examines and removes the information belonging to that layer
before passing the remaining payload upward.

The destination therefore reconstructs the original application data.

------------------------------------------------------------------------

# Protocol Data Units (PDUs)

A **PDU (Protocol Data Unit)** is the unit of data handled by a
particular layer.

## Layer 4 --- Segment or Datagram

``` text
+----------------+----------------+
|   L4 Header    |      Data      |
+----------------+----------------+
```

-   TCP data is commonly called a **segment**.
-   UDP data is commonly called a **datagram**.

The L4 header contains transport information such as source and
destination ports.

------------------------------------------------------------------------

## Layer 3 --- Packet

``` text
+-----------+----------------+----------------+
| L3 Header |   L4 Header    |      Data      |
+-----------+----------------+----------------+
```

The Layer 3 packet contains IP addressing information.

------------------------------------------------------------------------

## Layer 2 --- Frame

``` text
+-----------+-----------+----------------+-----------+------------+
| L2 Header | L3 Header |   L4 + Data     | L2 Trailer|
+-----------+-----------+----------------+-----------+------------+
```

An Ethernet frame contains:

-   Layer 2 header.
-   Layer 3 packet as payload.
-   Layer 2 trailer.

### PDU naming to remember

``` text
Application → Data
Transport   → Segment / Datagram
Network     → Packet
Data Link   → Frame
Physical    → Bits
```

------------------------------------------------------------------------

# Payload

The **payload** is the portion of a PDU carried as data by that layer.

For example:

-   At Layer 4, application data is the payload.
-   At Layer 3, the Layer 4 segment/datagram is the payload.
-   At Layer 2, the Layer 3 packet is the payload.

A layer adds its own header around the payload it receives from the
layer above.

------------------------------------------------------------------------

# Adjacent-Layer Interaction

Adjacent layers interact vertically inside a host.

Each layer:

-   **Uses the service of the layer below it.**
-   **Provides a service to the layer above it.**

For example:

``` text
Application
    ↑ service provided by Transport
Transport
    ↑ service provided by Internet
Internet
    ↑ service provided by Data Link
Data Link
    ↑ service provided by Physical
Physical
```

A useful way to remember this is:

> **Lower layers provide services; upper layers use those services.**

------------------------------------------------------------------------

# Same-Layer Interaction

Same-layer interaction describes the logical communication between
corresponding layers on different devices.

For example:

``` text
PC1                           SRV1

Application  ←────────────→  Application
Transport    ←────────────→  Transport
Internet     ←────────────→  Internet
Data Link    ←────────────→  Data Link
Physical     ←────────────→  Physical
```

The actual bits do not magically travel directly from one Layer 4
process to another. Instead, the data is encapsulated through the lower
layers, transmitted over the network, and then decapsulated at the
destination.

### Addressing at different layers

-   **Layer 4:** Port number identifies the destination
    application/process.
-   **Layer 3:** IP address identifies the destination host.
-   **Layer 2:** MAC address identifies the next-hop interface on the
    local network.
-   **Layer 1:** Physical signals carry the bits.

------------------------------------------------------------------------

# Separation of Layers

Layer separation makes networking modular.

For example:

-   An application can use TCP without needing to know how Ethernet
    transmits bits.
-   IP can operate over Ethernet without needing to understand
    application data.
-   Ethernet can carry IP packets without understanding the application
    protocol inside them.
-   A change at one layer can often be made without changing all other
    layers.

This modularity is one of the most important reasons layered networking
models are useful.

------------------------------------------------------------------------

# Packet Tracer Simulation Mode

Today I used **Cisco Packet Tracer Simulation Mode** to monitor protocol
traffic rather than simply watching the topology in real time.

Simulation Mode makes it possible to:

1.  Generate network traffic.
2.  Pause the traffic.
3.  Inspect individual events.
4.  Open a PDU.
5.  Examine information by OSI layer.
6.  Follow a packet as it moves through the topology.

This makes the OSI model much easier to understand because the
theoretical layers can be connected to actual packets and frames.

------------------------------------------------------------------------

# What I Monitored

## 1. STP Traffic

I observed **STP** events between the switches.

The PDU information showed Layer 2 Ethernet information, demonstrating
that STP is associated with the Data Link layer.

The simulation also showed the frame being transmitted through the
physical interfaces.

------------------------------------------------------------------------

## 2. OSPF Traffic

I observed **OSPF Hello** traffic generated by R1.

The Packet Tracer PDU information showed:

``` text
Protocol: OSPF HELLO
Source IP: R1
Destination IP: 224.0.0.5
```

Important observation:

-   OSPF is carried inside IP at Layer 3.
-   `224.0.0.5` is the IPv4 multicast address used by OSPF routers.
-   The packet also contains Layer 2 Ethernet information.
-   The physical interface is responsible for transmitting the resulting
    bits.

This was a useful example of seeing multiple layers involved in
transporting a single protocol message.

------------------------------------------------------------------------

## 3. DHCP Traffic

I also monitored DHCP traffic generated by PC1.

The DHCP Discover packet showed:

``` text
Source IP:      0.0.0.0
Destination IP: 255.255.255.255

Source UDP port:      68
Destination UDP port: 67
```

This happens because a client that does not yet have an IP address
cannot initially use a normal source IP address.

The DHCP Discover is broadcast so that the client can locate a DHCP
server on the local network.

### Packet Tracer observation

The PDU information showed:

``` text
Layer 7: DHCP
Layer 4: UDP, source port 68 → destination port 67
Layer 3: IP, 0.0.0.0 → 255.255.255.255
Layer 2: Ethernet frame
Layer 1: Physical port/interface
```

This was a practical demonstration of encapsulation across multiple
layers.

------------------------------------------------------------------------

# DHCP Release Observation

I also used the PC command prompt to run:

``` text
ipconfig /release
```

The result showed:

``` text
IP Address:       0.0.0.0
Subnet Mask:      0.0.0.0
Default Gateway:  0.0.0.0
DNS Server:       0.0.0.0
```

This demonstrated that releasing the DHCP lease removes the currently
assigned IPv4 configuration from the PC.

Before the release, PC1 had:

``` text
IPv4 Address:    192.168.1.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.1.1
```

------------------------------------------------------------------------

# Important Packet Tracer Observations

The simulation screenshots helped connect theory to actual network
events.

### Observation 1 --- Protocols use multiple layers

An application protocol such as DHCP is not transmitted directly as raw
application data.

It is encapsulated through:

``` text
DHCP
 ↓
UDP
 ↓
IP
 ↓
Ethernet
 ↓
Physical transmission
```

### Observation 2 --- Different protocols operate at different layers

Examples from today's lab:

  Protocol / technology   Main layer
  ----------------------- ----------------------------------------------
  DHCP                    Application
  HTTP                    Application
  FTP                     Application
  TCP                     Transport
  UDP                     Transport
  IP                      Internet / Network
  OSPF                    Network layer/control protocol carried in IP
  Ethernet                Data Link
  STP                     Data Link
  Physical Ethernet       Physical

### Observation 3 --- Packet Tracer exposes layer information

The **PDU Information** window can show:

-   Application protocol information.
-   Transport headers.
-   Source/destination IP addresses.
-   Ethernet headers.
-   Physical ports/interfaces.

This makes it possible to trace how one message is represented at
different layers.

------------------------------------------------------------------------

# Screenshots / Lab Evidence

The following screenshots were captured during today's study and are
stored in the repository under:

`notes/images/day-03/`

## TCP/IP Model

![TCP/IP 5-layer
model](images/day-03/Screenshot%202026-09-08%20101820.png)

The diagram compares the TCP/IP model with the common 5-layer model and
shows how the layers map across the network path.

## Encapsulation and Decapsulation

![Encapsulation and
decapsulation](images/day-03/Screenshot%202026-09-08%20101833.png)

This illustrates headers and trailers being added during encapsulation
and removed during decapsulation.

## Protocol Data Units

![Protocol Data
Units](images/day-03/Screenshot%202026-09-08%20101908.png)

This shows the names of PDUs at different layers: segment/datagram,
packet, and frame.

## Adjacent- and Same-Layer Interaction

![Adjacent and same-layer
interaction](images/day-03/Screenshot%202026-09-08%20102009.png)

This demonstrates how each layer communicates logically with the
corresponding layer on another host and provides services to adjacent
layers.

## STP Simulation

![STP simulation in Packet
Tracer](images/day-03/Screenshot%202026-09-08%20104938.png)

Packet Tracer Simulation Mode was used to inspect STP traffic and its
Layer 2 information.

## OSPF Simulation

![OSPF simulation in Packet
Tracer](images/day-03/Screenshot%202026-09-08%20105231.png)

This capture shows OSPF Hello traffic and the Layer 3 IP information
associated with it.

## PC IP Configuration

![PC IP
configuration](images/day-03/Screenshot%202026-09-08%20105418.png)

The PC command prompt shows the IPv4 configuration obtained by PC1.

## DHCP Simulation

![DHCP simulation](images/day-03/Screenshot%202026-09-08%20105557.png)

Packet Tracer Simulation Mode shows DHCP traffic moving through the
topology.

## DHCP Release

![DHCP release](images/day-03/Screenshot%202026-09-08%20105616.png)

After running `ipconfig /release`, the IPv4 configuration is reset to
`0.0.0.0`.

## DHCP PDU Details

![DHCP PDU details](images/day-03/Screenshot%202026-09-08%20105909.png)

The PDU details show DHCP at the application layer and UDP ports 68 → 67
at the transport layer, along with the Layer 3 and Layer 2 information.

------------------------------------------------------------------------

# What I Learned Today

-   I learned why networking is represented using **layered models**.
-   I learned the responsibilities of the **five TCP/IP layers**.
-   I learned how the **OSI 7-layer model** relates to the common
    5-layer model.
-   I learned that Layer 4 uses **port numbers** to identify
    applications/processes.
-   I learned that Layer 3 uses **IP addresses** to identify hosts
    across networks.
-   I learned that Layer 2 uses **MAC addresses** for local-network
    delivery.
-   I learned that Layer 1 is responsible for transmitting **bits as
    physical signals**.
-   I learned the difference between **encapsulation** and
    **decapsulation**.
-   I learned the PDU names:
    -   Data
    -   Segment / Datagram
    -   Packet
    -   Frame
    -   Bits
-   I learned the difference between **payload** and protocol
    headers/trailers.
-   I learned the difference between **adjacent-layer interaction** and
    **same-layer interaction**.
-   I learned how Packet Tracer Simulation Mode can expose the OSI-layer
    information of real protocol events.
-   I observed **STP, OSPF, and DHCP** traffic in the simulation.
-   I used `ipconfig` and `ipconfig /release` on PC1 to inspect and
    modify its IP configuration.

------------------------------------------------------------------------

# Key Takeaways

## Remember the Layer Responsibilities

``` text
Layer 5 — Application
    What application is communicating?

Layer 4 — Transport
    Which process/application? → Port number

Layer 3 — Internet / Network
    Which host/network? → IP address

Layer 2 — Data Link / Local Network
    Which local interface/next hop? → MAC address

Layer 1 — Physical
    How are the bits transmitted?
```

## Remember Encapsulation

``` text
DATA
 ↓
SEGMENT / DATAGRAM
 ↓
PACKET
 ↓
FRAME
 ↓
BITS
```

## Remember Decapsulation

``` text
BITS
 ↓
FRAME
 ↓
PACKET
 ↓
SEGMENT / DATAGRAM
 ↓
DATA
```

------------------------------------------------------------------------

# Commands Practiced

### Display IP configuration

``` text
ipconfig
```

### Release DHCP configuration

``` text
ipconfig /release
```

The release operation removes the current DHCP-assigned IPv4
configuration from the PC.

------------------------------------------------------------------------

# Lab Topology Summary

``` text
                    10.0.0.0/24
             ┌─────────────────────┐
             │                     │
          R1 .1                  R2 .2
             │
             │ 192.168.1.1
             │
           SW1
          /   \
       SRV1   SW2
      .100      │
                │
              PC1
             .10
```

The lab used:

-   2 routers --- R1 and R2
-   2 switches --- SW1 and SW2
-   1 server --- SRV1
-   1 PC --- PC1
-   Ethernet connections
-   Two IPv4 networks
-   Packet Tracer Simulation Mode

------------------------------------------------------------------------

# Review Questions

### 1. What is encapsulation?

Encapsulation is the process of adding protocol headers/trailers as data
moves down the networking stack.

### 2. What is decapsulation?

Decapsulation is the process of removing those headers/trailers as the
received data moves up the stack.

### 3. What is a Layer 4 PDU?

A **TCP segment** or **UDP datagram**.

### 4. What is a Layer 3 PDU?

A **packet**.

### 5. What is a Layer 2 PDU?

A **frame**.

### 6. What identifies an application at Layer 4?

A **port number**.

### 7. What identifies a host at Layer 3?

An **IP address**.

### 8. What identifies an interface on the local network?

A **MAC address**.

### 9. What does Layer 1 transmit?

Bits represented as electrical, optical, or radio signals.

### 10. Why does DHCP initially use `0.0.0.0` as the source IP?

The client does not yet have a valid IPv4 address, so it uses `0.0.0.0`
as the source during the initial DHCP process.

### 11. What UDP ports are used by DHCP?

The client uses **UDP 68** and the server uses **UDP 67**.

### 12. What is the difference between adjacent-layer and same-layer interaction?

Adjacent-layer interaction is the service relationship between
neighboring layers on a device. Same-layer interaction is the logical
communication between corresponding layers on different devices.

------------------------------------------------------------------------

# Conclusion

Today connected the **theory of networking layers** with **actual packet
behavior in Cisco Packet Tracer**.

The most important concept was that application data does not travel
through the network as one simple object. It is progressively
encapsulated:

``` text
Application Data
      ↓
Transport Header
      ↓
IP Header
      ↓
Ethernet Header + Trailer
      ↓
Physical Bits
```

At the destination, the process is reversed through decapsulation.

The simulation exercises made it possible to observe this process with
real examples:

``` text
STP   → Layer 2
OSPF  → Layer 3
DHCP  → Layer 7/Application
UDP   → Layer 4
IP    → Layer 3
Ethernet → Layer 2
Physical transmission → Layer 1
```

The TCP/IP and OSI models are therefore useful mental models for
understanding **where a protocol operates, what information it adds, how
devices process that information, and how data moves from one
application to another across a network**.

------------------------------------------------------------------------

## Today's Progress

-   [x] Reviewed TCP/IP and OSI layered models
-   [x] Understood the responsibilities of Layers 1--5
-   [x] Compared TCP/IP 5-layer and OSI 7-layer models
-   [x] Studied encapsulation and decapsulation
-   [x] Studied PDU terminology
-   [x] Studied payloads, headers, and trailers
-   [x] Studied adjacent-layer interaction
-   [x] Studied same-layer interaction
-   [x] Used Packet Tracer Simulation Mode
-   [x] Monitored STP traffic
-   [x] Monitored OSPF Hello traffic
-   [x] Monitored DHCP traffic
-   [x] Inspected OSI-layer PDU information
-   [x] Used `ipconfig`
-   [x] Used `ipconfig /release`
-   [x] Captured and organized screenshots as lab evidence

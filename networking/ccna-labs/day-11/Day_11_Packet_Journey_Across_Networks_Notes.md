# Day 11 — Packet Journey Across Networks

## Overview

Today I focused on understanding how a packet travels from one device to another across multiple networks.

This session was primarily **theory + simulation walkthrough**. I did not perform a separate hands-on Packet Tracer lab today. The goal was to understand what happens to **IP addresses, MAC addresses, Ethernet frames, ARP, and packets at each hop**.

The main idea:

> **IP addresses identify the end-to-end source and destination, while MAC addresses change at every Layer 2 hop.**

---

## 1. Network Topology

Example topology used for the packet journey:

```text
PC1
192.168.1.1
MAC: 1111
   |
   | 192.168.1.0/24
   |
  SW1
   |
R1 G0/2
IP: 192.168.1.254
MAC: AAAA
   |
R1 G0/0
IP: 192.168.12.1
MAC: BBBB
   |
   | 192.168.12.0/24
   |
R2 G0/0
IP: 192.168.12.2
MAC: CCCC
   |
R2 G0/1
IP: 192.168.24.2
MAC: DDDD
   |
   | 192.168.24.0/24
   |
R4 G0/1
IP: 192.168.24.4
MAC: EEEE
   |
R4 G0/2
IP: 192.168.4.254
MAC: FFFE
   |
  SW4
   |
PC4
192.168.4.1
MAC: 4444
```

### Address Summary

| Device | Interface | IP Address | MAC Address |
|---|---|---|---|
| PC1 | NIC | 192.168.1.1 | 1111 |
| R1 | G0/2 | 192.168.1.254 | AAAA |
| R1 | G0/0 | 192.168.12.1 | BBBB |
| R2 | G0/0 | 192.168.12.2 | CCCC |
| R2 | G0/1 | 192.168.24.2 | DDDD |
| R4 | G0/1 | 192.168.24.4 | EEEE |
| R4 | G0/2 | 192.168.4.254 | FFFE |
| PC4 | NIC | 192.168.4.1 | 4444 |

---

# 2. The Main Question

PC1 wants to communicate with PC4:

```text
Source IP      = 192.168.1.1
Destination IP = 192.168.4.1
```

PC1 and PC4 are on different networks.

Therefore, PC1 cannot send the Ethernet frame directly to PC4.

Instead:

```text
PC1
 ↓
Default Gateway R1
 ↓
R2
 ↓
R4
 ↓
PC4
```

The packet is routed hop-by-hop.

---

# 3. Step 1 — PC1 Creates the Packet

PC1 creates an IP packet.

```text
Source IP      : 192.168.1.1
Destination IP : 192.168.4.1
```

PC1 checks whether `192.168.4.1` belongs to its local subnet.

PC1 is in:

```text
192.168.1.0/24
```

PC4 is in:

```text
192.168.4.0/24
```

They are different networks.

Therefore PC1 sends the packet to its **default gateway**:

```text
Default Gateway:
192.168.1.254
```

---

# 4. Step 2 — ARP Resolves the Default Gateway MAC

PC1 knows the gateway IP:

```text
192.168.1.254
```

But it needs the gateway's MAC address before it can create the Ethernet frame.

PC1 sends an ARP Request:

```text
Who has 192.168.1.254?
Tell 192.168.1.1
```

The ARP request is broadcast.

```text
Destination MAC:
FFFF.FFFF.FFFF
```

The switch forwards the broadcast within the local broadcast domain.

R1 receives the ARP request because `192.168.1.254` belongs to R1.

R1 replies:

```text
192.168.1.254 = AAAA
```

PC1 can now build the Ethernet frame.

---

# 5. Step 3 — PC1 Sends the Frame to R1

The first Ethernet frame looks like:

```text
Source MAC      = 1111
Destination MAC = AAAA

Source IP       = 192.168.1.1
Destination IP  = 192.168.4.1
```

Important:

The destination MAC is **R1's MAC**, not PC4's MAC.

Why?

Because PC4 is on another network.

PC1's first Layer 2 destination is its default gateway.

---

# 6. Step 4 — R1 Receives the Frame

R1 receives the Ethernet frame.

R1 removes the Layer 2 Ethernet header and examines the IP packet.

```text
Source IP      = 192.168.1.1
Destination IP = 192.168.4.1
```

R1 checks its routing table.

It determines that the packet must continue toward R2.

Example next hop:

```text
192.168.12.2
```

R1 now needs the MAC address associated with the next-hop IP.

---

# 7. Step 5 — R1 Uses ARP to Find R2

R1 sends an ARP request on the `192.168.12.0/24` network:

```text
Who has 192.168.12.2?
```

R2 replies:

```text
192.168.12.2 = CCCC
```

R1 can now create a new Ethernet frame.

```text
Source MAC      = BBBB
Destination MAC = CCCC

Source IP       = 192.168.1.1
Destination IP  = 192.168.4.1
```

Notice what changed:

```text
MAC addresses → changed
IP addresses  → stayed the same
```

---

# 8. Step 6 — R2 Forwards the Packet

R2 receives the frame.

It removes the Ethernet header and checks the destination IP:

```text
192.168.4.1
```

R2 checks its routing table and determines that the packet should be forwarded toward R4.

Next hop:

```text
192.168.24.4
```

R2 needs R4's MAC address.

---

# 9. Step 7 — R2 Uses ARP to Find R4

R2 sends an ARP request:

```text
Who has 192.168.24.4?
```

R4 responds:

```text
192.168.24.4 = EEEE
```

R2 creates a new Ethernet frame:

```text
Source MAC      = DDDD
Destination MAC = EEEE

Source IP       = 192.168.1.1
Destination IP  = 192.168.4.1
```

Again:

```text
MAC addresses → changed
IP addresses  → unchanged
```

---

# 10. Step 8 — R4 Finds PC4

R4 sees that:

```text
192.168.4.1
```

belongs to its directly connected network:

```text
192.168.4.0/24
```

R4 still needs PC4's MAC address if it is not already in the ARP cache.

R4 sends:

```text
Who has 192.168.4.1?
```

PC4 replies:

```text
192.168.4.1 = 4444
```

R4 can now send the final Ethernet frame.

```text
Source MAC      = FFFE
Destination MAC = 4444

Source IP       = 192.168.1.1
Destination IP  = 192.168.4.1
```

---

# 11. Step 9 — PC4 Receives the Packet

PC4 receives the Ethernet frame.

The destination MAC is its own:

```text
4444
```

PC4 processes the frame and examines the IP packet.

```text
Source IP      = 192.168.1.1
Destination IP = 192.168.4.1
```

The destination IP matches PC4's IP address.

The packet has reached its destination.

---

# 12. The Most Important Pattern

The complete journey can be simplified like this:

```text
PC1 → R1 → R2 → R4 → PC4
```

### IP addresses

```text
192.168.1.1 → 192.168.4.1
```

These remain the same throughout the normal routing process described here.

### MAC addresses

The MAC addresses change at every Layer 2 segment:

```text
PC1 → R1
1111 → AAAA

R1 → R2
BBBB → CCCC

R2 → R4
DDDD → EEEE

R4 → PC4
FFFE → 4444
```

This is one of the most important concepts to remember for CCNA.

---

# 13. MAC vs IP Address

| Address | Main Purpose | Changes at Each Router? |
|---|---|---|
| MAC Address | Local Layer 2 delivery | Yes |
| IP Address | Layer 3 logical addressing | Normally no |
| Source MAC | Identifies sender on current LAN | Yes |
| Destination MAC | Identifies next Layer 2 destination | Yes |
| Source IP | Original IP sender | Normally no |
| Destination IP | Final IP destination | Normally no |

### Easy Memory Trick

```text
MAC = Next Hop
IP  = Final Destination
```

For the packet traveling from PC1 to PC4:

```text
IP:
PC1 -------------------------------> PC4
192.168.1.1                         192.168.4.1

MAC:
PC1 → R1 → R2 → R4 → PC4
```

---

# 14. Encapsulation

Encapsulation means adding headers as data moves down the networking stack.

A simplified view:

```text
Application Data
       ↓
TCP/UDP Segment
       ↓
IP Packet
       ↓
Ethernet Frame
       ↓
Bits
```

At the source:

```text
Data
 ↓
Segment
 ↓
Packet
 ↓
Frame
```

The frame is transmitted over the physical network.

---

# 15. De-encapsulation at a Router

When a router receives an Ethernet frame:

```text
Ethernet Frame
      ↓
Remove Layer 2 header
      ↓
Inspect IP packet
      ↓
Routing table lookup
      ↓
Build new Layer 2 frame
      ↓
Forward
```

The router does not simply forward the original Ethernet frame unchanged.

It removes the old Layer 2 framing and creates new Layer 2 framing for the next link.

---

# 16. What Switches Do

Switches operate primarily at Layer 2.

A switch:

- Learns source MAC addresses.
- Stores them in a MAC address table.
- Looks at destination MAC addresses.
- Forwards frames out the appropriate interface.
- Floods unknown unicast/broadcast traffic according to switching rules.
- Does not normally change the source/destination MAC addresses in the frame.

Example:

```text
PC1 → SW1 → R1
```

The switch learns something similar to:

```text
MAC Address 1111 → Port connected to PC1
MAC Address AAAA → Port connected to R1
```

---

# 17. What Routers Do

Routers operate at Layer 3.

A router:

1. Receives a frame.
2. Removes the Layer 2 header/trailer as appropriate.
3. Examines the destination IP.
4. Performs a routing-table lookup.
5. Determines the outgoing interface/next hop.
6. Resolves the next-hop MAC when required.
7. Creates a new Layer 2 frame.
8. Forwards the packet.

Simplified:

```text
Receive
   ↓
De-encapsulate
   ↓
Check Destination IP
   ↓
Routing Table Lookup
   ↓
Next Hop
   ↓
ARP if required
   ↓
Re-encapsulate
   ↓
Forward
```

---

# 18. ARP — Why Is It Needed?

ARP is used on IPv4 Ethernet networks to resolve:

```text
IPv4 Address → MAC Address
```

Example:

```text
192.168.1.254 → AAAA
```

ARP is relevant when a device knows the IP address of the local next-hop device but needs its MAC address for Ethernet delivery.

---

# 19. ARP Broadcast and Reply

### ARP Request

```text
Source MAC:
PC1 = 1111

Destination MAC:
FFFF.FFFF.FFFF

Question:
Who has 192.168.1.254?
```

### ARP Reply

```text
Source MAC:
R1 = AAAA

Destination MAC:
PC1 = 1111

Answer:
192.168.1.254 is AAAA
```

The ARP request is broadcast.

The normal ARP reply is unicast to the requester.

---

# 20. Same Network vs Different Network

This is another important CCNA concept.

## Same Network

Suppose PC1 wants to communicate with PC3 and both are in the same subnet.

PC1 can send the frame directly to PC3.

```text
PC1 → SW1 → PC3
```

The frame can look like:

```text
Source MAC      = PC1 MAC
Destination MAC = PC3 MAC

Source IP       = PC1 IP
Destination IP  = PC3 IP
```

The default gateway is not required for the initial delivery.

---

## Different Network

For PC1 communicating with PC4:

```text
PC1 → SW1 → R1 → R2 → R4 → SW4 → PC4
```

The first destination MAC is the default gateway's MAC.

```text
Source MAC      = PC1
Destination MAC = R1
```

The destination IP is still PC4.

---

# 21. Packet Journey Table

| Hop | Source MAC | Destination MAC | Source IP | Destination IP |
|---|---|---|---|---|
| PC1 → R1 | 1111 | AAAA | 192.168.1.1 | 192.168.4.1 |
| R1 → R2 | BBBB | CCCC | 192.168.1.1 | 192.168.4.1 |
| R2 → R4 | DDDD | EEEE | 192.168.1.1 | 192.168.4.1 |
| R4 → PC4 | FFFE | 4444 | 192.168.1.1 | 192.168.4.1 |

### Key observation

The Layer 2 addresses change:

```text
1111 → AAAA
BBBB → CCCC
DDDD → EEEE
FFFE → 4444
```

The Layer 3 addresses remain:

```text
192.168.1.1 → 192.168.4.1
```

---

# 22. Return Traffic

When PC4 replies to PC1, the direction reverses:

```text
PC4 → R4 → R2 → R1 → PC1
```

The IP addresses reverse:

```text
Source IP      = 192.168.4.1
Destination IP = 192.168.1.1
```

The Ethernet MAC addresses also change at each hop.

If the required ARP entries are already cached, the devices can use the cached mappings instead of generating a new ARP request immediately.

ARP cache entries can expire, so ARP is not permanently avoided.

---

# 23. Useful Verification Commands

### Windows PC

```text
ipconfig /all
```

Useful for checking:

- IPv4 address
- MAC address
- Default gateway

### Windows ARP table

```text
arp -a
```

Useful for viewing learned IPv4-to-MAC mappings.

### Cisco IOS

```text
show ip interface brief
```

Shows interface status and IP addresses.

```text
show interfaces g0/0
```

Can be used to inspect interface details including the hardware address.

```text
show arp
```

Shows the router's ARP table.

```text
show ip route
```

Shows the routing table.

```text
ping 192.168.4.1
```

Tests IP connectivity to the destination.

---

# 24. CCNA Quiz Insights

When asked to identify source and destination MAC addresses, first ask:

### Question 1: Is the destination on the same network?

If yes:

```text
Destination MAC = Destination device MAC
```

If no:

```text
Destination MAC = Default Gateway MAC
```

### Question 2: Which link are we looking at?

MAC addresses are **link-local**.

For example:

```text
PC1 → R1
Source      = PC1 MAC
Destination = R1 G0/2 MAC
```

But on the next link:

```text
R1 → R2
Source      = R1 outgoing interface MAC
Destination = R2 incoming interface MAC
```

### Question 3: What happens to the IP addresses?

For this normal IPv4 forwarding example:

```text
Source IP      = 192.168.1.1
Destination IP = 192.168.4.1
```

They remain the same as the packet crosses the routers.

---

# 25. Common Mistakes to Avoid

## Mistake 1 — Sending directly to PC4's MAC

Wrong idea:

```text
PC1 → PC4 MAC
```

PC1 is not on PC4's LAN.

Correct:

```text
PC1 → Default Gateway R1
```

---

## Mistake 2 — Thinking MAC addresses are end-to-end

MAC addresses are used for local Layer 2 delivery.

They change when the packet crosses a router.

---

## Mistake 3 — Thinking switches change MAC addresses

Normally, switches forward the existing Ethernet frame.

Routers create a new Layer 2 frame for the next link.

---

## Mistake 4 — Thinking the IP destination changes at every router

The destination IP remains the final destination in this normal routing example.

Routers use the destination IP to decide where to forward the packet.

---

## Mistake 5 — Forgetting ARP

Knowing the next-hop IP is not enough for Ethernet delivery.

The sender needs the corresponding MAC address.

```text
Next-hop IP
    ↓
ARP
    ↓
Next-hop MAC
    ↓
Ethernet Frame
```

---

# 26. Simple Mental Model

Remember these three lines:

```text
IP = Where the packet ultimately needs to go
MAC = Where the frame needs to go on this local link
ARP = Finds MAC from IPv4 address
```

And for a routed packet:

```text
IP stays the same
MAC changes
Frame is rebuilt at every router
```

---

# 27. Final Packet Journey

```text
                 SAME IP PACKET
        ┌──────────────────────────────┐
        │ Src IP: 192.168.1.1          │
        │ Dst IP: 192.168.4.1          │
        └──────────────────────────────┘

PC1                R1              R2              R4              PC4
 │                  │               │               │               │
 │ 1111 → AAAA      │               │               │               │
 ├─────────────────>│               │               │               │
 │                  │ BBBB → CCCC   │               │               │
 │                  ├──────────────>│               │               │
 │                  │               │ DDDD → EEEE   │               │
 │                  │               ├──────────────>│               │
 │                  │               │               │ FFFE → 4444   │
 │                  │               │               ├──────────────>│
 │                  │               │               │               │
```

The packet crosses multiple networks, but the IP packet continues toward the same final destination.

---

# 28. Day 11 Learning Summary

Today I focused on:

- Packet journey across multiple networks
- IPv4 source and destination addresses
- MAC addresses and local Layer 2 delivery
- ARP requests and replies
- Default gateway behavior
- Encapsulation
- De-encapsulation
- Router forwarding
- Routing table lookup
- Switch MAC learning
- Same-network communication
- Different-network communication
- Hop-by-hop MAC changes
- End-to-end IP addressing
- Reading packet flow in a simulation

### Practical status

```text
Day 11:
Theory + Simulation Understanding ✅
Hands-on Lab ❌
```

I did not count this as a hands-on lab day because the session was focused on watching the simulation and understanding the packet journey.

---

# 29. Key Takeaways

1. **IP addresses identify the logical source and destination.**
2. **MAC addresses provide Layer 2 delivery on the current network segment.**
3. **ARP maps an IPv4 address to a MAC address on the local network.**
4. **A host on another network is reached through the default gateway.**
5. **Routers use destination IP addresses and routing tables to forward packets.**
6. **Routers remove the incoming Layer 2 framing and create new Layer 2 framing for the next hop.**
7. **Switches forward Ethernet frames based on MAC addresses.**
8. **MAC addresses change from hop to hop.**
9. **The source and destination IP addresses normally remain unchanged during this routed journey.**
10. **Understanding the difference between Layer 2 and Layer 3 is essential for troubleshooting and CCNA questions.**

---

## Day 11 Status

**Topic:** Packet Journey Across Networks  
**Focus:** ARP, MAC/IP addressing, encapsulation, de-encapsulation, routing and forwarding  
**Activity:** Theory + simulation walkthrough  
**Hands-on lab:** Not performed today  
**Next:** Continue with the next networking topic and apply the concepts in a hands-on lab.

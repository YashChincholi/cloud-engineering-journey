# Day 06 — Ethernet LAN Switching

## Introduction

Day 06 continues the study of **Ethernet LAN Switching** and focuses on how devices communicate with each other within the same local area network (LAN).

This lesson builds on the Ethernet switching theory from Day 05 and connects the concepts to practical packet analysis using **Cisco Packet Tracer**.

### Topics Covered

- Ethernet Frames
- Address Resolution Protocol (ARP)
- ARP Request and ARP Reply
- ARP Tables
- ICMP and Ping
- Ethernet Type Field
- Switch MAC Address Tables
- MAC Address Learning
- Broadcast, Unknown Unicast, and Known Unicast Traffic
- Clearing Dynamic MAC Address Table Entries
- Packet Tracer Simulation Mode
- Introduction to Wireshark packet analysis

---

## 1. Ethernet Frame

An Ethernet frame is the Layer 2 unit used to carry data across an Ethernet network.

### Ethernet Frame Fields

The important Ethernet header fields are:

| Field | Purpose |
|---|---|
| Destination MAC | MAC address of the receiving device |
| Source MAC | MAC address of the sending device |
| Type | Identifies the protocol carried inside the frame |

The **Preamble** and **Start Frame Delimiter (SFD)** are transmitted before the Ethernet header but are generally not considered part of the Ethernet header.

### Ethernet Frame Size

- Minimum Ethernet frame size: **64 bytes**
- Minimum Ethernet payload: **46 bytes**
- If the actual payload is smaller than 46 bytes, **padding** is added.
- Padding is added to the end of the payload to meet the minimum frame size.

### Important Ethernet Type Values

| Type | Protocol |
|---|---|
| `0x0800` | IPv4 |
| `0x0806` | ARP |

---

# 2. ARP — Address Resolution Protocol

**ARP (Address Resolution Protocol)** is used to discover the **MAC address** associated with a known **IPv4 address** on the local network.

A device may know the destination IP address but still need the destination MAC address before sending an Ethernet frame.

### Example

Suppose:

```text
PC1
IP: 192.168.1.2

PC3
IP: 192.168.1.3
```

PC1 wants to ping PC3.

PC1 knows:

```text
Destination IP = 192.168.1.3
```

But PC1 also needs:

```text
Destination MAC = PC3's MAC address
```

ARP is used to discover that MAC address.

---

# 3. ARP Request

An **ARP Request** asks:

> "Who has this IP address?"

The ARP request is sent as a **broadcast** on the local network.

### Destination MAC Address

```text
FFFF.FFFF.FFFF
```

This is the Ethernet broadcast MAC address.

Because the destination MAC is broadcast, switches flood the frame out all appropriate ports except the port on which the frame was received.

### ARP Request Process

```text
PC1
 |
 | ARP Request
 | "Who has 192.168.1.3?"
 | Destination MAC: FFFF.FFFF.FFFF
 v
SW1
 |
 +--------+--------+
 |        |        |
PC2      PC3      PC4
         |
         | "That IP is mine"
```

Only the device whose IP address matches the requested IP responds.

---

# 4. ARP Reply

The destination device sends an **ARP Reply** back to the device that made the request.

Unlike the ARP Request, the ARP Reply is normally **unicast**.

### Example

PC3 receives:

```text
ARP Request:
Who has 192.168.1.3?
```

PC3 responds:

```text
ARP Reply:
192.168.1.3 is at <PC3-MAC>
```

The ARP Reply is sent directly to PC1's MAC address.

---

# 5. ARP Request vs ARP Reply

| Feature | ARP Request | ARP Reply |
|---|---|---|
| Purpose | Find MAC address | Provide MAC address |
| Destination | Broadcast | Unicast |
| Destination MAC | `FFFF.FFFF.FFFF` | Requester's MAC |
| Traffic Type | Broadcast | Unicast |
| Result | Finds destination MAC | Updates ARP table |

---

# 6. ARP Table

An **ARP table** stores mappings between IP addresses and MAC addresses.

Example:

```text
IP Address       MAC Address
192.168.1.3      00AA.BBCC.DDEE
192.168.1.4      0011.2233.4455
```

ARP table entries can be:

- **Dynamic** — learned through ARP communication
- **Static** — manually configured

### Viewing the ARP Table

On a PC:

```bash
arp -a
```

On Cisco IOS:

```cisco
show arp
```

---

# 7. Ping and ICMP

`ping` is a network utility used to test whether another device is reachable.

Ping uses **ICMP (Internet Control Message Protocol)**.

The basic ICMP exchange is:

```text
ICMP Echo Request
        |
        v
Destination
        |
        v
ICMP Echo Reply
```

### Example

```bash
ping 192.168.1.3
```

A successful ping produces ICMP Echo Replies.

### Important Point

Before sending the ICMP Echo Request over Ethernet, the sender needs the destination MAC address.

Therefore, when the MAC address is not already known, ARP occurs first.

---

# 8. ARP + Ping Communication Flow

When PC1 sends its first ping to PC3, the process can look like this:

```text
PC1
 |
 | 1. Check ARP Table
 v
MAC address not found
 |
 | 2. ARP Request
 |    Broadcast
 v
Switch
 |
 | 3. Flood Broadcast
 v
PC3
 |
 | 4. ARP Reply
 |    Unicast
 v
PC1
 |
 | 5. Store IP-to-MAC mapping
 v
ARP Table
 |
 | 6. ICMP Echo Request
 v
PC3
 |
 | 7. ICMP Echo Reply
 v
PC1
```

### Key Idea

The first communication may require:

```text
ARP Request
        ↓
ARP Reply
        ↓
ICMP Echo Request
        ↓
ICMP Echo Reply
```

Once the MAC address is known, future communication can use the existing ARP entry until it expires or is removed.

---

# 9. Packet Tracer Simulation Mode

Cisco Packet Tracer provides a **Simulation Mode** that allows network traffic to be observed step by step.

It can be used to examine:

- ARP Requests
- ARP Replies
- ICMP Echo Requests
- ICMP Echo Replies
- Ethernet frames
- Layer 2 information
- Source and destination MAC addresses
- Protocol information

Simulation Mode is useful because it makes normally invisible network communication easier to understand.

---

# 10. ARP Request Frame Analysis

During the lab, the ARP Request can be inspected at Layer 2.

Important values include:

```text
Destination MAC:
FFFF.FFFF.FFFF

Ethernet Type:
0x0806

Protocol:
ARP
```

The broadcast destination tells the switch that the frame should be flooded through the local network.

---

# 11. Switch MAC Address Learning

A switch maintains a **MAC address table**.

The table allows the switch to determine which interface should be used to forward a frame.

### How a Switch Learns

When a switch receives an Ethernet frame, it examines the:

```text
Source MAC Address
```

The switch then associates that MAC address with the interface where the frame arrived.

Example:

```text
PC1
 |
Fa0/1
 |
SW1
```

If SW1 receives a frame from PC1, it can learn:

```text
PC1 MAC → Fa0/1
```

This is called **dynamic MAC address learning**.

---

# 12. MAC Address Table

A Cisco switch MAC address table contains information such as:

| Field | Meaning |
|---|---|
| VLAN | VLAN associated with the entry |
| MAC Address | Learned Layer 2 address |
| Type | Dynamic or static |
| Ports | Interface associated with the MAC address |

### View the MAC Address Table

```cisco
show mac address-table
```

Example:

```text
Vlan    Mac Address       Type       Ports
----    -----------       --------   -----
1       00AA.BBCC.DDEE    DYNAMIC    Fa0/1
1       0011.2233.4455    DYNAMIC    Fa0/2
```

---

# 13. MAC Address Learning Example

Consider:

```text
PC1 ---- SW1 ---- SW2 ---- PC3
```

When PC1 sends traffic:

1. SW1 receives the frame.
2. SW1 reads PC1's source MAC address.
3. SW1 learns PC1's MAC on the incoming interface.
4. SW1 forwards the frame according to its MAC address table.
5. SW2 can also learn the source MAC when the frame reaches it.

Traffic generation is therefore important for dynamically populating the MAC address table.

---

# 14. Known Unicast Traffic

A **known unicast** frame is a frame whose destination MAC address is already present in the switch's MAC address table.

The switch can forward the frame only through the interface associated with that destination MAC.

```text
PC1
 |
SW1
 |
 |---- PC2
```

If SW1 knows that PC2's MAC address is on a specific port, it forwards the frame only through that port.

This reduces unnecessary traffic.

---

# 15. Unknown Unicast Traffic

An **unknown unicast** occurs when the destination MAC address is not present in the switch's MAC address table.

The switch floods the frame out the appropriate ports, except the port where the frame was received.

```text
             +--- PC2
             |
PC1 --- SW ---+--- PC3
             |
             +--- PC4
```

The switch does not yet know where the destination MAC address is located, so it floods the frame.

---

# 16. Broadcast Traffic

Broadcast frames are sent to all appropriate devices on the local network.

The Ethernet broadcast MAC address is:

```text
FFFF.FFFF.FFFF
```

ARP Requests are an important example of Layer 2 broadcast traffic.

A switch floods a broadcast frame out all appropriate interfaces except the incoming interface.

---

# 17. Broadcast vs Unknown Unicast vs Known Unicast

| Traffic Type | Destination Known? | Switch Behavior |
|---|---:|---|
| Known Unicast | Yes | Forward to specific port |
| Unknown Unicast | No | Flood |
| Broadcast | No | Flood |

### Easy Way to Remember

```text
Known Unicast
     ↓
One specific port

Unknown Unicast
     ↓
Flood

Broadcast
     ↓
Flood
```

---

# 18. Clearing the MAC Address Table

Dynamic MAC addresses can be manually removed from a Cisco switch.

### Clear All Dynamic MAC Addresses

```cisco
clear mac address-table dynamic
```

This removes dynamically learned MAC address entries.

### Clear Dynamic Entries on a Specific Interface

```cisco
clear mac address-table dynamic interface interface-id
```

Example:

```cisco
clear mac address-table dynamic interface fastethernet 0/1
```

The exact command options available can depend on the Cisco IOS/device implementation.

---

# 19. Verifying the MAC Address Table

Before clearing:

```cisco
show mac address-table
```

After clearing:

```cisco
show mac address-table
```

This can be used to verify that dynamic entries have been removed.

In Packet Tracer, the implementation of MAC address-table clearing may have limitations compared with real Cisco IOS/GNS3 environments.

---

# 20. GNS3

**GNS3** is a network emulator used to create and analyze network topologies.

Important points:

- GNS3 can run actual Cisco IOS images.
- Users generally need to provide legally obtained IOS images.
- GNS3 can be used for advanced network experimentation.
- GNS3 can integrate with Wireshark for packet analysis.

Packet Tracer is more beginner-friendly for learning Cisco networking concepts, while GNS3 provides a more realistic emulation environment when appropriately configured.

---

# 21. Wireshark

**Wireshark** is a network protocol analyzer.

It can capture and inspect network packets.

For this topic, Wireshark can be used to analyze:

- ARP Requests
- ARP Replies
- ICMP Echo Requests
- ICMP Echo Replies
- Ethernet headers
- Source MAC addresses
- Destination MAC addresses
- Protocol/type information

This makes Wireshark useful for understanding what is actually happening inside network communication.

---

# 22. Lab — Ethernet LAN Switching

## Lab Objective

The Packet Tracer lab was used to observe how devices communicate inside a LAN and how switches learn MAC addresses.

### Lab Topology

```text
PC1 ---- SW1 ---- SW2 ---- PC3
          |        |
         PC2      PC4
```

The network uses the:

```text
192.168.1.0/24
```

subnet.

---

## Lab Tasks

### 1. Observe the Initial Topology

The topology contains:

- 4 PCs
- 2 switches
- Ethernet connections
- A `192.168.1.0/24` network

---

### 2. Observe PC1 Sending Traffic

When PC1 sends traffic to another PC, Packet Tracer Simulation Mode can be used to observe the packet flow.

When PC1 does not know the destination MAC address, it first performs ARP resolution.

---

### 3. Observe the ARP Request

The ARP Request is broadcast:

```text
Destination MAC:
FFFF.FFFF.FFFF
```

The Ethernet Type is:

```text
0x0806
```

---

### 4. Test Connectivity with Ping

PC1 successfully tested connectivity to:

```text
192.168.1.3
```

and:

```text
192.168.1.4
```

Command:

```bash
ping 192.168.1.3
```

```bash
ping 192.168.1.4
```

The successful replies confirmed network reachability.

---

### 5. Check the MAC Address Table

On SW1 and SW2:

```cisco
show mac address-table
```

This displays dynamically learned MAC addresses and their associated interfaces.

---

### 6. Clear Dynamic MAC Entries

The lab also demonstrated:

```cisco
clear mac address-table dynamic
```

The MAC address table was then checked again to verify the removal of dynamic entries.

---

# 23. Important Lab Observations

### Observation 1 — ARP Happens Before First Communication

When the destination MAC address is unknown:

```text
Unknown MAC
   ↓
ARP Request
   ↓
ARP Reply
   ↓
MAC Address Learned
   ↓
ICMP Communication
```

### Observation 2 — ARP Request Is Broadcast

```text
Destination MAC:
FFFF.FFFF.FFFF
```

### Observation 3 — ARP Reply Is Unicast

The destination MAC is the MAC address of the device that originally sent the ARP Request.

### Observation 4 — Switches Learn from Source MAC Addresses

Switches learn MAC addresses by examining the **source MAC address** of incoming frames.

### Observation 5 — Traffic Populates the MAC Table

Generating traffic such as ping allows switches to dynamically learn MAC addresses.

### Observation 6 — Known Unicast Is Forwarded Selectively

When the destination MAC is known, the switch forwards the frame through the appropriate interface rather than flooding it everywhere.

---

# 24. Useful Commands

## Windows / PC

### Test Connectivity

```bash
ping <destination-ip>
```

Example:

```bash
ping 192.168.1.3
```

### View ARP Table

```bash
arp -a
```

---

## Cisco IOS

### View ARP Table

```cisco
show arp
```

### View MAC Address Table

```cisco
show mac address-table
```

### Clear All Dynamic MAC Entries

```cisco
clear mac address-table dynamic
```

### Clear Dynamic Entries on an Interface

```cisco
clear mac address-table dynamic interface interface-id
```

---

# 25. Quick Revision

## Ethernet Frame

```text
Destination MAC
       ↓
Source MAC
       ↓
Type
       ↓
Payload
       ↓
FCS
```

Minimum Ethernet frame size:

```text
64 bytes
```

Minimum payload:

```text
46 bytes
```

---

## ARP

```text
IP Address
    ↓
ARP
    ↓
MAC Address
```

ARP Request:

```text
Broadcast
```

ARP Reply:

```text
Unicast
```

---

## Ping

```text
ICMP Echo Request
        ↓
   Destination
        ↓
ICMP Echo Reply
```

---

## Switch MAC Learning

```text
Incoming Frame
      ↓
Read Source MAC
      ↓
Learn MAC + Interface
      ↓
Update MAC Address Table
```

---

## Traffic Types

```text
Known Unicast
      ↓
Specific Port

Unknown Unicast
      ↓
Flood

Broadcast
      ↓
Flood
```

---

# 26. Key Exam Points

1. **ARP maps a known IPv4 address to a MAC address on the local network.**
2. **An ARP Request is a broadcast message.**
3. **An ARP Reply is a unicast message.**
4. **The Ethernet broadcast MAC address is `FFFF.FFFF.FFFF`.**
5. **ARP uses Ethernet Type `0x0806`.**
6. **IPv4 uses Ethernet Type `0x0800`.**
7. **The minimum Ethernet frame size is 64 bytes.**
8. **The minimum Ethernet payload size is 46 bytes.**
9. **Padding is added when the payload is smaller than 46 bytes.**
10. **A switch learns MAC addresses from the source MAC address of incoming frames.**
11. **The MAC address table contains VLAN, MAC address, type, and port information.**
12. **`show mac address-table` displays the switch MAC address table.**
13. **Known unicast traffic is forwarded to the specific destination port.**
14. **Unknown unicast traffic is flooded.**
15. **Broadcast traffic is flooded out appropriate ports except the incoming port.**
16. **`clear mac address-table dynamic` removes dynamic MAC entries.**
17. **Ping uses ICMP Echo Request and Echo Reply messages.**
18. **Packet Tracer Simulation Mode helps visualize ARP and ICMP traffic.**
19. **Wireshark can capture and analyze network packets.**
20. **Traffic generation such as ping helps switches populate their MAC address tables.**

---

# 27. Quiz Review

## Quiz 1

**Question:** What happens when a 36-byte payload is sent in an Ethernet frame?

**Answer:**

The Ethernet payload must be at least 46 bytes. Therefore, padding bytes are added to the end of the payload so that the payload reaches 46 bytes.

```text
Actual Payload = 36 bytes
Padding         = 10 bytes
-------------------------
Total Payload   = 46 bytes
```

---

## Quiz 2

**Question:** How are ARP Request and ARP Reply messages sent?

**Answer:**

- ARP Request → Broadcast
- ARP Reply → Unicast

---

## Quiz 3

**Question:** What information is displayed by `show mac address-table`?

**Answer:**

The MAC address table includes:

- VLAN
- MAC Address
- Type
- Port/Interface

Command:

```cisco
show mac address-table
```

---

## Quiz 4

**Question:** How does a switch handle different types of Ethernet traffic?

**Answer:**

- **Known Unicast:** forwarded to the specific destination interface.
- **Unknown Unicast:** flooded out appropriate interfaces except the incoming interface.
- **Broadcast:** flooded out appropriate interfaces except the incoming interface.

---

## Quiz 5

**Question:** How can dynamic MAC addresses be cleared from a specific interface?

**Answer:**

Use:

```cisco
clear mac address-table dynamic interface interface-id
```

Example:

```cisco
clear mac address-table dynamic interface fastethernet 0/1
```

---

# 28. Day 06 Lab Evidence

All screenshots in this day's lab are **my own Packet Tracer work**.

### Screenshots

- `01-topology-and-lab-instructions.png` — Lab topology and instructions
- `02-pc1-outbound-icmp-arp-lookup.png` — PC1 initiating communication and triggering ARP
- `03-arp-request-broadcast-frame-details.png` — ARP broadcast frame details
- `04-ping-192-168-1-3-success.png` — Successful ping to PC3
- `05-ping-192-168-1-4-success.png` — Successful ping to PC4
- `06-sw1-show-mac-address-table.png` — SW1 MAC address table
- `07-sw2-show-and-clear-mac-address-table.png` — SW2 MAC table and clearing dynamic entries
- `08-sw1-clear-mac-address-table-verification.png` — Verification after clearing dynamic entries

---

# 29. Day 06 Lab Video

A screen recording was also created to demonstrate the Packet Tracer simulation:

```text
videos/packet-tracer-pc1-to-pc3-ping-arp-simulation.mp4
```

The recording demonstrates:

- ARP resolution
- Broadcast frame flooding
- Switch-to-switch forwarding
- ICMP packet flow
- PC1 to PC3 communication

---

# 30. Final Takeaway

Day 06 connected Ethernet LAN switching theory with practical packet-level observation.

The main communication flow learned was:

```text
PC wants to communicate
          ↓
Check ARP Table
          ↓
MAC unknown?
          ↓
     ARP Request
      (Broadcast)
          ↓
      ARP Reply
       (Unicast)
          ↓
Destination MAC learned
          ↓
    ICMP Echo Request
          ↓
    ICMP Echo Reply
```

At the switch level:

```text
Frame arrives
     ↓
Learn Source MAC
     ↓
Check Destination MAC
     ↓
Known? ──────────────── Yes ──→ Forward to specific port
  │
  No
  ↓
Flood frame
```

This lab helped reinforce how **ARP, Ethernet frames, MAC address learning, switching, and ICMP** work together during communication inside a LAN.

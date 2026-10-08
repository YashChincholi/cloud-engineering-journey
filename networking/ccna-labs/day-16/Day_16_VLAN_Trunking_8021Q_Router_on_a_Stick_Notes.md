# Day 17 — VLAN Trunking, 802.1Q & Router-on-a-Stick

## Overview

Day 17 continues VLANs by moving from basic access-port configuration to **trunking** and **inter-VLAN routing with Router-on-a-Stick (ROAS)**.

### Topics covered

- Access ports vs trunk ports
- Purpose of trunk ports
- VLAN tagging
- IEEE 802.1Q (dot1q)
- 802.1Q tag fields: TPID, PCP, DEI, VID
- VLAN ranges
- Native VLAN
- Native VLAN mismatch
- Cisco trunk configuration
- Allowed VLANs
- Trunk verification
- Router-on-a-Stick
- Router subinterfaces
- `encapsulation dot1q`
- Inter-VLAN packet flow

---

# 1. Access Port vs Trunk Port

## Access Port

An **access port** belongs to a single VLAN.

Example:

```text
PC ─── Access Port ─── Switch
          VLAN 10
```

The frame does not need a VLAN tag because the switchport already identifies the VLAN.

### Key point

> Access port = one VLAN

---

## Trunk Port

A **trunk port** carries traffic for multiple VLANs over one physical interface.

```text
        VLAN 10 ─┐
        VLAN 20 ─┼── Trunk ──
        VLAN 30 ─┘
```

### Why use trunks?

Without trunks, each VLAN would require a separate physical connection.

With trunks:

```text
Multiple VLANs
      │
      ▼
 One physical link
      │
      ▼
Multiple VLANs
```

This saves switch/router interfaces and makes larger VLAN-based networks practical.

### Key point

> Trunk port = multiple VLANs on one physical link

---

# 2. VLAN Tagging

A switch needs a way to tell the receiving device which VLAN a frame belongs to when multiple VLANs share a trunk.

The solution is **VLAN tagging**.

Frames sent over a trunk are normally tagged with information identifying their VLAN.

Therefore:

- Access port → untagged
- Trunk port → tagged
- Native VLAN → untagged on the trunk

Another way to remember it:

```text
Access port  → Untagged
Trunk port   → Tagged
Native VLAN  → Untagged
```

---

# 3. Trunking Protocols

Two trunking protocols were introduced:

| Protocol | Description | CCNA Importance |
|---|---|---|
| ISL | Cisco proprietary trunking protocol | Know what it is |
| 802.1Q / dot1q | IEEE industry standard | Important |

### 802.1Q

IEEE 802.1Q is the standard VLAN tagging protocol used on Ethernet trunks.

For CCNA, focus primarily on **802.1Q (dot1q)**.

---

# 4. 802.1Q Tag

The 802.1Q tag is inserted into the Ethernet frame.

It is inserted between:

```text
Destination MAC
Source MAC
        ↓
    802.1Q Tag
        ↓
Type/Length
Data
FCS
```

The tag is:

- **4 bytes**
- **32 bits**

The 802.1Q tag contains:

```text
802.1Q Tag
├── TPID — 16 bits
└── TCI  — 16 bits
    ├── PCP — 3 bits
    ├── DEI — 1 bit
    └── VID — 12 bits
```

---

# 5. TPID — Tag Protocol Identifier

**TPID = Tag Protocol Identifier**

- Size: **16 bits / 2 bytes**
- Value: **0x8100**
- Identifies the frame as an 802.1Q-tagged frame

### Remember

```text
TPID → 0x8100 → 802.1Q
```

---

# 6. PCP — Priority Code Point

**PCP = Priority Code Point**

- Size: **3 bits**
- Used for **Class of Service (CoS)**
- Helps prioritize important traffic during congestion

For CCNA, remember the name and purpose rather than the detailed operation.

---

# 7. DEI — Drop Eligible Indicator

**DEI = Drop Eligible Indicator**

- Size: **1 bit**
- Identifies frames that may be dropped during congestion

Again, for CCNA, know the basic purpose.

---

# 8. VID — VLAN Identifier

**VID = VLAN Identifier**

- Size: **12 bits**
- Identifies which VLAN the frame belongs to

This is one of the most important fields of the 802.1Q tag.

### VLAN calculation

A 12-bit field provides:

```text
2^12 = 4096
```

Possible VLAN IDs:

```text
0 ───────────────────── 4095
```

However:

- VLAN 0 → reserved
- VLAN 4095 → reserved

Usable VLAN range:

```text
1 ───────────────────── 4094
```

### Important

> VID identifies the VLAN.

---

# 9. VLAN Ranges

The usable VLAN range is divided into two sections.

| Range | Name |
|---|---|
| 1–1005 | Normal VLANs |
| 1006–4094 | Extended VLANs |

Modern Cisco switches generally support the extended range, although some older devices may not.

---

# 10. Native VLAN

802.1Q supports a **native VLAN**.

### Default

```text
Native VLAN = VLAN 1
```

The native VLAN can be changed on each trunk interface.

### Important behavior

Frames belonging to the native VLAN are sent **without an 802.1Q tag**.

When a switch receives an untagged frame on a trunk, it assumes the frame belongs to the configured native VLAN.

```text
Native VLAN
     ↓
Untagged frame
     ↓
Receiving switch
     ↓
Assumes native VLAN
```

---

# 11. Native VLAN Mismatch

The native VLAN must match on both ends of a trunk.

Example:

```text
SW1                         SW2
Native VLAN 10  ─────────── Native VLAN 30
```

An untagged frame sent by SW1 is interpreted as VLAN 30 by SW2.

This can cause connectivity and network problems.

### Best practice

> Configure the same native VLAN on both ends of the trunk.

---

# 12. Configuring a Trunk

On a Cisco switch, manually configure a trunk with:

```cisco
interface g0/1
 switchport mode trunk
```

On switches that support multiple trunk encapsulation types, you may first need:

```cisco
switchport trunk encapsulation dot1q
switchport mode trunk
```

Modern switches that support only 802.1Q generally only require:

```cisco
switchport mode trunk
```

---

# 13. Verify Trunk Configuration

Use:

```cisco
show interfaces trunk
```

This command can show:

- Trunk interfaces
- Trunk mode
- Encapsulation
- Trunking status
- Native VLAN
- Allowed VLANs
- VLANs active in the management domain
- VLANs forwarding through STP

Example:

```text
Port        Mode       Encapsulation  Status
Gi0/1       on         802.1q         trunking
```

### Important distinction

```cisco
show vlan brief
```

primarily shows VLANs and access-port assignments.

It does **not** show trunk ports as members of every VLAN they carry.

For trunk verification, use:

```cisco
show interfaces trunk
```

---

# 14. Allowed VLANs

By default, all VLANs are allowed on a trunk.

You can restrict the trunk to only the VLANs required.

Example:

```cisco
switchport trunk allowed vlan 10,30
```

This allows only VLANs 10 and 30.

### Why restrict VLANs?

1. Security
2. Reduces unnecessary traffic
3. Prevents unrelated VLAN traffic from crossing the link

---

# 15. Allowed VLAN Command Options

## Set the allowed VLAN list

```cisco
switchport trunk allowed vlan 10,30
```

## Add a VLAN

```cisco
switchport trunk allowed vlan add 20
```

## Remove a VLAN

```cisco
switchport trunk allowed vlan remove 20
```

## Allow all VLANs

```cisco
switchport trunk allowed vlan all
```

This returns the trunk to the default state where all VLANs are allowed.

## Allow all except selected VLANs

```cisco
switchport trunk allowed vlan except 1-5,10
```

## Allow no VLANs

```cisco
switchport trunk allowed vlan none
```

---

# 16. Native VLAN Configuration

Change the native VLAN with:

```cisco
switchport trunk native vlan 1001
```

Example:

```cisco
interface g0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,30
 switchport trunk native vlan 1001
```

Using an unused VLAN as the native VLAN is commonly recommended as a security practice.

### Important

The native VLAN must match on both sides of the trunk.

---

# 17. Example Trunk Design

In the lab topology:

### SW1 → SW2

Required VLANs:

```text
VLAN 10
VLAN 30
```

VLAN 20 does not need to cross this trunk because there are no VLAN 20 hosts on SW1.

Example:

```cisco
interface g0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,30
 switchport trunk native vlan 1001
```

### SW2 → R1

Required VLANs:

```text
VLAN 10
VLAN 20
VLAN 30
```

Example:

```cisco
interface g0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 switchport trunk native vlan 1001
```

---

# 18. Router-on-a-Stick (ROAS)

## Problem

Suppose a router needs to route between:

```text
VLAN 10
VLAN 20
VLAN 30
```

One traditional solution is one physical router interface per VLAN.

That becomes inefficient as the number of VLANs increases.

---

## Solution: Router-on-a-Stick

**Router-on-a-Stick (ROAS)** uses:

- One physical router interface
- One switch trunk
- Multiple router subinterfaces

Example:

```text
                  R1
             G0/0 │
                  │
             ┌────┴────┐
             │  SW2    │
             └─────────┘
                Trunk
          VLAN 10/20/30
```

The router interface is divided logically:

```text
G0/0.10 → VLAN 10
G0/0.20 → VLAN 20
G0/0.30 → VLAN 30
```

These are logical subinterfaces of the same physical interface.

---

# 19. Router Subinterfaces

First enable the physical interface:

```cisco
interface g0/0
 no shutdown
```

The physical interface itself normally does not receive an IP address in this ROAS design.

Create the VLAN 10 subinterface:

```cisco
interface g0/0.10
 encapsulation dot1q 10
 ip address 192.168.1.62 255.255.255.192
```

VLAN 20:

```cisco
interface g0/0.20
 encapsulation dot1q 20
 ip address 192.168.1.126 255.255.255.192
```

VLAN 30:

```cisco
interface g0/0.30
 encapsulation dot1q 30
 ip address 192.168.1.190 255.255.255.192
```

### Subinterface numbering

The subinterface number does not technically have to match the VLAN number.

Example:

```text
G0/0.10 → VLAN 10
```

is recommended because it makes the configuration easier to understand.

---

# 20. `encapsulation dot1q`

Example:

```cisco
encapsulation dot1q 10
```

This tells the router:

> Frames tagged with VLAN 10 belong to this subinterface.

So:

```text
VLAN 10 tag
     ↓
R1 G0/0
     ↓
G0/0.10
```

When traffic leaves the subinterface, the router tags it with the configured VLAN ID.

---

# 21. Router-on-a-Stick Packet Flow

Suppose:

```text
PC4 = VLAN 30
PC7 = VLAN 10
```

PC4 wants to communicate with PC7.

### Step 1 — PC4 → SW1

PC4 sends the frame toward its default gateway.

### Step 2 — SW1 → SW2

The trunk carries VLAN 30 traffic.

### Step 3 — SW2 → R1

SW2 sends the frame through the trunk and tags it as VLAN 30.

### Step 4 — R1 receives VLAN 30

R1 receives the frame on:

```text
G0/0.30
```

because the frame contains the VLAN 30 tag.

### Step 5 — Routing

R1 checks the destination IP and determines that the destination is in the VLAN 10 subnet.

### Step 6 — R1 → SW2

R1 sends the frame out through G0/0 using:

```text
G0/0.10
```

The frame is tagged as VLAN 10.

### Step 7 — SW2 → SW1

The VLAN 10 frame crosses the trunk.

### Step 8 — SW1 → PC7

SW1 forwards the frame to the destination PC.

---

# 22. Complete ROAS Concept

```text
             VLAN 10
                │
PC ── SW1 ── SW2 ═════ R1
                ║       G0/0
             VLAN 20     │
                ║       ├── G0/0.10
             VLAN 30     ├── G0/0.20
                         └── G0/0.30
```

The physical link is one, but logically it carries multiple VLANs.

---

# 23. Important Commands

### Switch trunk

```cisco
interface g0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,30
 switchport trunk native vlan 1001
```

### Verify trunk

```cisco
show interfaces trunk
```

### Configure router subinterface

```cisco
interface g0/0.10
 encapsulation dot1q 10
 ip address <gateway-ip> <mask>
```

### Verify interfaces

```cisco
show ip interface brief
```

### Check routing table

```cisco
show ip route
```

---

# 24. Key Differences to Remember

| Concept | Key Idea |
|---|---|
| Access port | Carries one VLAN |
| Trunk port | Carries multiple VLANs |
| 802.1Q | VLAN tagging standard |
| TPID | Identifies 802.1Q; `0x8100` |
| PCP | Class of Service priority |
| DEI | Drop eligibility |
| VID | Identifies VLAN |
| Native VLAN | Sent untagged |
| Allowed VLANs | VLANs permitted on trunk |
| ROAS | Inter-VLAN routing using one router interface |
| Subinterface | Logical interface on physical router interface |
| `encapsulation dot1q` | Maps VLAN tag to subinterface |

---

# 25. CCNA Quick Memory Sheet

```text
ACCESS  → 1 VLAN
TRUNK   → Multiple VLANs

802.1Q  → VLAN tagging
TPID    → 0x8100
VID     → VLAN ID
VID     → 12 bits
VLANs   → 1–4094 usable

NATIVE VLAN → Untagged
DEFAULT NATIVE VLAN → 1

ROAS → Router-on-a-Stick
ROAS → 1 physical interface + multiple subinterfaces

G0/0.10 → VLAN 10
G0/0.20 → VLAN 20
G0/0.30 → VLAN 30

encapsulation dot1q 10
→ Maps VLAN 10 to the subinterface
```

---

# 26. Quiz Review

## Q1. How do you send VLAN 10 traffic untagged over a trunk?

```cisco
switchport trunk native vlan 10
```

**Answer: D**

Native VLAN traffic is sent untagged.

---

## Q2. How do you return allowed VLANs to the default?

```cisco
switchport trunk allowed vlan all
```

**Answer: B**

All VLANs are allowed by default.

---

## Q3. `switchport mode trunk` is rejected. What can fix it on a switch supporting multiple encapsulations?

```cisco
switchport trunk encapsulation dot1q
```

**Answer: C**

Then configure:

```cisco
switchport mode trunk
```

---

## Q4. Which 802.1Q field identifies the VLAN?

```text
VID
```

**Answer: B**

VID = VLAN Identifier.

---

## Q5. A VLAN is allowed on the trunk but does not appear under "VLANs allowed and active in management domain". Why?

Because the VLAN may not actually exist on the switch.

**Answer: A**

---

# 27. Lab Takeaways

In the Packet Tracer practice:

- Configured access ports for VLAN 10, VLAN 20 and VLAN 30
- Created and verified VLANs
- Configured a trunk between SW1 and SW2
- Restricted the SW1–SW2 trunk to VLANs 10 and 30
- Configured native VLAN 1001
- Configured the SW2–R1 trunk for VLANs 10, 20 and 30
- Configured Router-on-a-Stick on R1
- Created subinterfaces `G0/0.10`, `G0/0.20`, and `G0/0.30`
- Applied `encapsulation dot1q`
- Assigned gateway IP addresses
- Verified trunk operation
- Tested connectivity using ICMP ping
- Observed packet flow between VLANs in Packet Tracer

---

# 28. Final Takeaway

The main idea of Day 17 is:

> **Trunks carry multiple VLANs over one physical link, 802.1Q identifies the VLAN, and Router-on-a-Stick uses subinterfaces to route between those VLANs.**

A simple mental model:

```text
Access Port
    ↓
One VLAN

Trunk Port
    ↓
Many VLANs
    ↓
802.1Q Tags

Router-on-a-Stick
    ↓
One physical router interface
    ↓
Multiple subinterfaces
    ↓
Inter-VLAN Routing
```

## Useful verification commands

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
```

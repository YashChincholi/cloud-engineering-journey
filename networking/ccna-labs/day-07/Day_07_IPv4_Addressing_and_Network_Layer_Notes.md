# Day 07 — IPv4 Addressing & Network Layer Fundamentals

## Introduction

- The **CCNA 200-301** course introduces how traffic is forwarded between different LANs.
- This topic focuses mainly on **Layer 3 (Network Layer)** of the OSI model.
- Layer 3 provides connectivity between end hosts that are on **different networks**.
- It uses **logical addressing**, mainly **IP addresses**, to identify devices and networks.
- Layer 3 is responsible for **path selection** between a source and destination.
- **Routers** primarily operate at Layer 3 and use IP addresses to make forwarding decisions.

> **Day 07 focus:** This day was mainly theory and number-system practice rather than a Packet Tracer lab.

---

## 1. OSI Model — Network Layer Review

### Layer 3: Network Layer

The **Network Layer** is Layer 3 of the OSI model.

Its major responsibilities include:

1. **Logical addressing**
   - Uses IP addresses to identify devices.
   - IPv4 is one example of a Layer 3 addressing system.

2. **Inter-network connectivity**
   - Allows communication between devices located on different networks.
   - Communication outside the local LAN generally requires a router.

3. **Path selection**
   - Determines an appropriate path from the source network to the destination network.
   - Routers use routing information to make forwarding decisions.

### Layer 2 vs Layer 3

| Feature | Layer 2 — Data Link | Layer 3 — Network |
|---|---|---|
| Address | MAC address | IP address |
| Main purpose | Local network delivery | Delivery between networks |
| Example device | Switch | Router |
| Address type | Physical/logical interface identifier | Logical address |
| PDU | Frame | Packet |

### Simple Example

```text
PC A
192.168.1.10
     |
   Switch
     |
   Router
     |
   Switch
     |
PC B
192.168.2.10
```

PC A and PC B are on different IP networks.

- The **switches** provide Layer 2 connectivity within their LANs.
- The **router** provides Layer 3 connectivity between the two networks.
- The IP addresses allow the router to identify the source and destination networks.

---

# 2. IPv4 Addresses

## What is an IPv4 Address?

An **IPv4 address** is a logical address used to identify a device/interface on an IP network.

An IPv4 address contains:

- **32 bits**
- **4 octets**
- **8 bits per octet**

```text
32 bits
   ↓
8 bits + 8 bits + 8 bits + 8 bits
   ↓
Octet 1 . Octet 2 . Octet 3 . Octet 4
```

Example:

```text
192.168.1.10
```

The four decimal numbers are the four octets.

### IPv4 Range

Each octet contains 8 bits.

The possible values of one octet are:

```text
0 — 255
```

Therefore, a valid IPv4 address has four octets, each normally ranging from 0 to 255.

Example:

```text
10.0.0.1
172.16.5.20
192.168.1.100
```

---

# 3. Binary Number System

IPv4 addressing is based on **binary** because computers represent information using bits.

Binary is a **base-2** number system.

Each bit can have one of two values:

```text
0 or 1
```

## Binary Place Values

An 8-bit binary octet has these values:

| Bit | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Value | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |

The maximum value is:

```text
128 + 64 + 32 + 16 + 8 + 4 + 2 + 1 = 255
```

Therefore:

```text
00000000 = 0
11111111 = 255
```

---

# 4. Decimal and Hexadecimal Review

## Decimal

Decimal is a **base-10** number system.

It uses:

```text
0 1 2 3 4 5 6 7 8 9
```

Decimal is the format normally used when writing IPv4 addresses.

Example:

```text
192.168.1.10
```

## Hexadecimal

Hexadecimal is a **base-16** number system.

It uses:

```text
0 1 2 3 4 5 6 7 8 9 A B C D E F
```

Where:

```text
A = 10
B = 11
C = 12
D = 13
E = 14
F = 15
```

Hexadecimal is commonly encountered in networking when working with values such as **MAC addresses** and other binary-related representations.

### Number-System Summary

| Number System | Base | Symbols |
|---|---:|---|
| Binary | 2 | 0–1 |
| Decimal | 10 | 0–9 |
| Hexadecimal | 16 | 0–9, A–F |

---

# 5. Binary to Decimal Conversion

To convert an 8-bit binary number to decimal:

1. Write the binary digits.
2. Write the corresponding bit values.
3. Add the values where the bit is **1**.
4. Ignore the values where the bit is **0**.

## Example 1

Convert:

```text
10001111
```

Bit values:

```text
128 64 32 16 8 4 2 1
 1   0  0  0  1 1 1 1
```

Add the values corresponding to `1`:

```text
128 + 8 + 4 + 2 + 1
= 143
```

Therefore:

```text
10001111 = 143
```

## Example 2

Convert:

```text
01110110
```

```text
128 64 32 16 8 4 2 1
 0   1  1  1  0 1 1 0
```

Add:

```text
64 + 32 + 16 + 4 + 2
= 118
```

Therefore:

```text
01110110 = 118
```

### Important Rule

```text
Binary  →  Decimal
Add the place values where the bit is 1.
```

---

# 6. Decimal to Binary Conversion

To convert a decimal number into binary:

1. Start with the 8-bit place values.
2. Check whether the decimal value is large enough to subtract the current place value.
3. Write `1` if it can be subtracted.
4. Write `0` if it cannot.
5. Continue until all 8 positions are processed.

The place values are:

```text
128 64 32 16 8 4 2 1
```

## Example 1 — 221 to Binary

Start with:

```text
221
```

Subtract:

```text
221 - 128 = 93
93  - 64  = 29
29  - 16  = 13
13  - 8   = 5
5   - 4   = 1
1   - 1   = 0
```

Therefore:

```text
128 64 32 16 8 4 2 1
 1   1  0  1  1 1 0 0
```

So:

```text
221 = 11011100
```

## Example 2 — 127 to Binary

```text
127 = 64 + 32 + 16 + 8 + 4 + 2 + 1
```

Therefore:

```text
128 64 32 16 8 4 2 1
 0   1  1  1  1 1 1 1
```

So:

```text
127 = 01111111
```

### Important Range

With 8 bits:

```text
Minimum = 00000000 = 0
Maximum = 11111111 = 255
```

So one IPv4 octet can represent:

```text
0–255
```

---

# 7. IPv4 Network Portion and Host Portion

An IPv4 address contains two important logical parts:

```text
Network Portion + Host Portion
```

The boundary between them is determined by the **prefix length / subnet mask**.

## Example: /24

Consider:

```text
192.168.1.10/24
```

`/24` means:

```text
First 24 bits  = Network portion
Remaining 8 bits = Host portion
```

Visually:

```text
11111111.11111111.11111111.00000000
<--------- Network -------->< Host >
```

For:

```text
192.168.1.10/24
```

The network portion is:

```text
192.168.1
```

The host portion is represented by:

```text
10
```

### Same Network Example

```text
PC-A: 192.168.1.10/24
PC-B: 192.168.1.20/24
PC-C: 192.168.1.30/24
```

All three have:

```text
Network = 192.168.1.0/24
```

But their host portions are different.

```text
192.168.1.10
192.168.1.20
192.168.1.30
          ↑
   Different hosts
```

> **Key idea:** Devices on the same IPv4 subnet share the same network portion but must have unique usable host addresses.

---

# 8. IPv4 Address Classes

IPv4 was historically divided into **five classes** based on the first octet.

| Class | First Octet | Traditional Default Prefix | Main Purpose |
|---|---:|---:|---|
| A | 0–127 | /8 | Large networks |
| B | 128–191 | /16 | Medium networks |
| C | 192–223 | /24 | Smaller networks |
| D | 224–239 | N/A | Multicast |
| E | 240–255 | N/A | Experimental/Reserved |

> **CCNA note:** Modern networks use **CIDR and prefix lengths** rather than relying on traditional classful addressing. Address classes are still useful for understanding the history and fundamentals of IPv4.

---

# 9. Class A

Traditional Class A addresses use:

```text
First octet: 0–127
Default prefix: /8
Default mask: 255.0.0.0
```

Structure:

```text
Network . Host . Host . Host
```

Example:

```text
10.1.2.3
```

With the traditional `/8` boundary:

```text
10        . 1 . 2 . 3
Network     <--- Host --->
```

Class A provides relatively few network numbers but a very large number of host addresses per network.

---

# 10. Class B

Traditional Class B addresses use:

```text
First octet: 128–191
Default prefix: /16
Default mask: 255.255.0.0
```

Structure:

```text
Network . Network . Host . Host
```

Example:

```text
172.16.10.20
```

With the traditional `/16` boundary:

```text
172.16    . 10 . 20
 Network      < Host >
```

Class B was designed for medium-sized networks.

---

# 11. Class C

Traditional Class C addresses use:

```text
First octet: 192–223
Default prefix: /24
Default mask: 255.255.255.0
```

Structure:

```text
Network . Network . Network . Host
```

Example:

```text
192.168.1.10
```

With the traditional `/24` boundary:

```text
192.168.1    . 10
  Network     Host
```

Class C provides many possible networks with a smaller number of hosts per traditional network.

---

# 12. Class D

Class D addresses:

```text
224–239
```

are used for **multicast**.

Multicast allows traffic to be sent to a group of interested receivers rather than to one specific host or every host on the network.

Example range:

```text
224.0.0.0 – 239.255.255.255
```

---

# 13. Class E

Class E addresses:

```text
240–255
```

are historically reserved for experimental purposes.

They are not used as ordinary unicast host addresses in normal IPv4 networks.

---

# 14. Loopback Addresses

The IPv4 range:

```text
127.0.0.0/8
```

is reserved for **loopback**.

The most commonly used loopback address is:

```text
127.0.0.1
```

It is commonly called:

```text
localhost
```

## Purpose of Loopback

Loopback allows a device to send traffic to itself through its network stack without sending that traffic onto the physical network.

It can be used to:

- Test the local TCP/IP stack.
- Verify that networking software is functioning locally.
- Refer to the local host.

Example:

```text
Computer
   |
   | traffic to 127.0.0.1
   ↓
Local network stack
```

> **Important:** `127.0.0.0/8` is the loopback range. `127.0.0.1` is the most commonly used loopback address.

---

# 15. IPv4 Address Classes — Network and Host Capacity

For traditional classful addressing:

| Class | Network Bits | Host Bits | Traditional Networks | Addresses per Network |
|---|---:|---:|---:|---:|
| A | 8 | 24 | 128 | 16,777,216 |
| B | 16 | 16 | 16,384 | 65,536 |
| C | 24 | 8 | 2,097,152 | 256 |

These values include special network and broadcast addresses in the traditional calculation.

For ordinary host assignment, two addresses in a subnet are normally unavailable:

```text
Network address
Broadcast address
```

Therefore, for a traditional `/24` network:

```text
Total addresses = 256
Usable host addresses = 254
```

> The exact historical classful ranges have reserved portions, so simplified figures such as “128 Class A networks” are useful for basic learning but should not be treated as a modern address-allocation rule.

---

# 16. Prefix Length

A **prefix length** tells us how many bits belong to the network portion.

It is written using slash notation:

```text
/<number>
```

Examples:

```text
/8
/16
/24
```

## /24 Example

```text
192.168.1.10/24
```

Means:

```text
24 network bits
8 host bits
```

Binary representation of the prefix:

```text
11111111.11111111.11111111.00000000
```

---

# 17. Netmasks / Subnet Masks

A subnet mask is another way to represent the network/host boundary.

For every network bit:

```text
1
```

For every host bit:

```text
0
```

## Common Masks

| Prefix | Subnet Mask | Network Bits | Host Bits |
|---|---|---:|---:|
| /8 | 255.0.0.0 | 8 | 24 |
| /16 | 255.255.0.0 | 16 | 16 |
| /24 | 255.255.255.0 | 24 | 8 |

### Prefix and Mask Are Equivalent

For example:

```text
192.168.1.10/24
```

is equivalent to:

```text
IP:   192.168.1.10
Mask: 255.255.255.0
```

Both tell us that the first 24 bits represent the network portion.

---

# 18. Network Address

The **network address** identifies the network itself rather than an individual host.

The host bits of the network address are all:

```text
0
```

## Example

Given:

```text
192.168.1.10/24
```

The network is:

```text
192.168.1.0/24
```

Host portion:

```text
00000000
```

Therefore:

```text
Network address = 192.168.1.0
```

The network address is not normally assigned to an individual host.

### /24 Example

```text
Network:     192.168.1.0
First host:  192.168.1.1
...
Last host:   192.168.1.254
Broadcast:   192.168.1.255
```

---

# 19. Broadcast Address

The **broadcast address** is used to send traffic to all hosts on a particular IPv4 subnet.

The host bits of the broadcast address are all:

```text
1
```

## Example

For:

```text
192.168.1.0/24
```

the broadcast address is:

```text
192.168.1.255
```

Binary host portion:

```text
11111111
```

The broadcast address is not normally assigned to an individual host.

### /24 Address Layout

```text
192.168.1.0      → Network address
192.168.1.1      → First usable host
192.168.1.2      → Usable host
...
192.168.1.254    → Last usable host
192.168.1.255    → Broadcast address
```

---

# 20. Complete IPv4 Example

Consider:

```text
IP Address: 192.168.10.50/24
```

### Step 1 — Identify the prefix

```text
/24
```

means:

```text
24 network bits
8 host bits
```

### Step 2 — Identify the subnet mask

```text
255.255.255.0
```

### Step 3 — Find the network address

Set all host bits to `0`:

```text
192.168.10.0
```

### Step 4 — Find the broadcast address

Set all host bits to `1`:

```text
192.168.10.255
```

### Step 5 — Find usable host range

```text
First usable: 192.168.10.1
Last usable:  192.168.10.254
```

### Summary

| Item | Value |
|---|---|
| IP address | 192.168.10.50 |
| Prefix | /24 |
| Subnet mask | 255.255.255.0 |
| Network address | 192.168.10.0 |
| First usable host | 192.168.10.1 |
| Last usable host | 192.168.10.254 |
| Broadcast address | 192.168.10.255 |
| Total addresses | 256 |
| Usable host addresses | 254 |

---

# 21. Important IPv4 Concepts

## Logical Address

An IP address is a **logical address**.

Unlike a MAC address, it is used to identify a device/interface within an IP network and to determine network reachability.

## Network Portion

Identifies which network the address belongs to.

## Host Portion

Identifies the particular host/interface within that network.

## Prefix Length

Defines how many bits belong to the network portion.

## Subnet Mask

Represents the same network/host boundary in dotted-decimal form.

## Network Address

Identifies the subnet itself.

## Broadcast Address

Identifies all hosts on the subnet for IPv4 broadcast delivery.

---

# 22. Quick Binary Reference

Memorize this table for CCNA subnetting:

| Binary Bit | Decimal Value |
|---|---:|
| 10000000 | 128 |
| 01000000 | 64 |
| 00100000 | 32 |
| 00010000 | 16 |
| 00001000 | 8 |
| 00000100 | 4 |
| 00000010 | 2 |
| 00000001 | 1 |

Full octet:

```text
128 64 32 16 8 4 2 1
```

---

# 23. Useful CCNA Memory Rules

### Rule 1 — IPv4 size

```text
IPv4 = 32 bits
```

### Rule 2 — Number of octets

```text
4 octets
```

### Rule 3 — Bits per octet

```text
8 bits
```

### Rule 4 — Octet range

```text
0–255
```

### Rule 5 — /24

```text
24 network bits
8 host bits
```

### Rule 6 — /24 subnet mask

```text
255.255.255.0
```

### Rule 7 — Network address

```text
Host bits = all 0s
```

### Rule 8 — Broadcast address

```text
Host bits = all 1s
```

### Rule 9 — Usable range

For a normal IPv4 subnet:

```text
Network address + 1
        ↓
     Usable hosts
        ↓
Broadcast address - 1
```

---

# 24. Day 07 Review

- Layer 3 is the **Network Layer** of the OSI model.
- Layer 3 provides logical addressing using **IP addresses**.
- Routers operate primarily at Layer 3 and connect different IP networks.
- IPv4 addresses contain **32 bits**.
- IPv4 addresses are written as **four 8-bit octets** in dotted-decimal notation.
- One IPv4 octet can represent values from **0 to 255**.
- Binary is base 2, decimal is base 10, and hexadecimal is base 16.
- A prefix length identifies how many bits belong to the network portion.
- `/24` means **24 network bits and 8 host bits**.
- A subnet mask represents the same boundary as a prefix length.
- The network address has all host bits set to `0`.
- The broadcast address has all host bits set to `1`.
- Traditional IPv4 classes are A, B, C, D, and E.
- Class D is associated with multicast.
- Class E is historically associated with experimental/reserved use.
- `127.0.0.0/8` is reserved for loopback.
- Modern IPv4 networking uses **CIDR/prefix lengths**, so classful addressing is mainly a foundational concept.

---

# 25. Quick Revision Table

| Topic | Key Point |
|---|---|
| OSI Layer 3 | Network Layer |
| Main Layer 3 address | IP address |
| Main Layer 3 device | Router |
| IPv4 size | 32 bits |
| IPv4 octets | 4 |
| Bits per octet | 8 |
| Octet range | 0–255 |
| Binary base | 2 |
| Decimal base | 10 |
| Hexadecimal base | 16 |
| `/8` | 8 network bits |
| `/16` | 16 network bits |
| `/24` | 24 network bits |
| `/24` mask | 255.255.255.0 |
| Network address | Host bits all 0 |
| Broadcast address | Host bits all 1 |
| Loopback range | 127.0.0.0/8 |
| Common loopback | 127.0.0.1 |
| Class A | 0–127 |
| Class B | 128–191 |
| Class C | 192–223 |
| Class D | 224–239 |
| Class E | 240–255 |

---

# 26. Practice Questions

## Q1. How many bits are in an IPv4 address?

**Answer:** 32 bits.

## Q2. How many octets are in an IPv4 address?

**Answer:** 4 octets.

## Q3. How many bits are in each IPv4 octet?

**Answer:** 8 bits.

## Q4. What is the decimal range of one IPv4 octet?

**Answer:** 0 to 255.

## Q5. What does `/24` mean?

**Answer:** The first 24 bits are the network portion and the remaining 8 bits are the host portion.

## Q6. What is the subnet mask for `/24`?

**Answer:**

```text
255.255.255.0
```

## Q7. What is the network address of `192.168.1.25/24`?

**Answer:**

```text
192.168.1.0
```

## Q8. What is the broadcast address of `192.168.1.25/24`?

**Answer:**

```text
192.168.1.255
```

## Q9. What is `10001111` in decimal?

**Answer:**

```text
143
```

## Q10. What is `01110110` in decimal?

**Answer:**

```text
118
```

## Q11. What is 221 in 8-bit binary?

**Answer:**

```text
11011101
```

> **Correction note:** If your course material states `221 = 11011100`, that is incorrect. `11011100` equals **220**. The correct conversion is `221 = 11011101`.

## Q12. What is 127 in 8-bit binary?

**Answer:**

```text
01111111
```

---

# 27. Key Takeaway

```text
IPv4 Address
     |
     +----------------------+
     |                      |
Network Portion        Host Portion
     |                      |
Identifies network     Identifies host
     |
Defined by prefix/subnet mask
```

The most important concepts from Day 07 are:

```text
IPv4 = 32 bits
      ↓
4 × 8-bit octets
      ↓
Network + Host
      ↓
Prefix/Subnet Mask defines the boundary
      ↓
Network address = host bits 0
Broadcast address = host bits 1
```

These concepts form the foundation for **subnetting, routing, routing tables, and inter-network communication**, which are important parts of the CCNA 200-301 syllabus.

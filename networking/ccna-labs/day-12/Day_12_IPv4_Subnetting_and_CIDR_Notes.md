# Day 12 — IPv4 Subnetting & CIDR Basics

## Overview

Today I learned the fundamentals of **IPv4 subnetting** and **CIDR (Classless Inter-Domain Routing)**.

Subnetting allows a larger IPv4 network to be divided into smaller networks called **subnets**, helping use IP address space more efficiently.

---

## 1. IPv4 Address Classes

Traditional IPv4 addressing used five classes:

| Class | First Octet Range | Default Prefix | Purpose |
|---|---:|---:|---|
| A | 1–126 | /8 | Large networks |
| B | 128–191 | /16 | Medium networks |
| C | 192–223 | /24 | Smaller networks |
| D | 224–239 | N/A | Multicast |
| E | 240–255 | N/A | Experimental |

> Modern networks primarily use **CIDR**, so classful addressing is mainly useful for understanding IPv4 history and fundamentals.

---

## 2. Why Subnetting Is Needed

A fixed classful network can provide far more addresses than an organization actually needs.

For example:

- A /24 network contains **256 total addresses**.
- If a network only needs around 45 host addresses, assigning a larger network can waste address space.
- Subnetting allows the network to be divided into smaller, appropriately sized networks.

### Main benefits

- Efficient use of IPv4 addresses
- Smaller broadcast domains
- Better network organization
- Easier network management
- More flexible IP allocation

---

## 3. CIDR — Classless Inter-Domain Routing

**CIDR** was introduced in 1993 to replace the older classful addressing approach.

CIDR represents a network using:

```text
IP address / prefix length
```

Example:

```text
192.168.1.0/24
```

The `/24` means:

- 24 bits = network portion
- 8 bits = host portion

### Important idea

```text
Prefix length ↑  →  Host bits ↓  →  Fewer addresses
Prefix length ↓  →  Host bits ↑  →  More addresses
```

---

## 4. Usable IPv4 Addresses

For most traditional IPv4 subnets:

```text
Usable addresses = 2^host_bits - 2
```

The two excluded addresses are:

- Network address
- Broadcast address

### Common CIDR sizes

| CIDR | Host Bits | Total Addresses | Usable Hosts |
|---|---:|---:|---:|
| /25 | 7 | 128 | 126 |
| /26 | 6 | 64 | 62 |
| /27 | 5 | 32 | 30 |
| /28 | 4 | 16 | 14 |
| /29 | 3 | 8 | 6 |
| /30 | 2 | 4 | 2 |

### Example — /26

```text
Host bits = 32 - 26 = 6

Total addresses = 2^6 = 64
Usable hosts = 64 - 2 = 62
```

---

## 5. /30 for Point-to-Point Links

A `/30` subnet contains:

```text
4 total addresses
2 usable host addresses
```

Example:

```text
Network:    10.0.0.0
Usable:     10.0.0.1
            10.0.0.2
Broadcast:  10.0.0.3
```

This fits a traditional point-to-point link requiring two endpoint addresses.

---

## 6. /31 for Point-to-Point Links

A `/31` has only **2 total addresses** and **1 host bit**.

Under traditional subnet rules:

```text
2^1 - 2 = 0 usable hosts
```

However, `/31` can be used on point-to-point links under standards such as **RFC 3021**, where the two addresses are treated as endpoint addresses and no network/broadcast address is required.

This makes `/31` more address-efficient than `/30` for supported point-to-point links.

---

## 7. /32 Host Route

A `/32` contains exactly one IPv4 address:

```text
32 network bits
0 host bits
```

It is commonly used to represent a **single host route**, such as:

```text
192.168.1.10/32
```

A `/32` is not normally used as a regular multi-host subnet.

---

## 8. Subnetting Scenario

### Requirement

Network:

```text
192.168.1.0/24
```

Needs to be divided into **4 subnets**.

Each subnet needs approximately **47 addresses**.

### Step 1 — Find the required subnet size

Available choices include:

```text
/30 → 2 usable hosts
/29 → 6 usable hosts
/28 → 14 usable hosts
/27 → 30 usable hosts
/26 → 62 usable hosts
```

We need at least 47 usable host addresses.

Therefore:

```text
/26 → 62 usable hosts
```

is required.

---

## 9. Dividing 192.168.1.0/24 into Four /26 Subnets

A `/24` has 256 total addresses.

A `/26` has 64 addresses.

Therefore:

```text
256 / 64 = 4 subnets
```

The four subnets are:

| Subnet | Network Address | Usable Host Range | Broadcast |
|---|---|---|---|
| 1 | 192.168.1.0/26 | 192.168.1.1–192.168.1.62 | 192.168.1.63 |
| 2 | 192.168.1.64/26 | 192.168.1.65–192.168.1.126 | 192.168.1.127 |
| 3 | 192.168.1.128/26 | 192.168.1.129–192.168.1.190 | 192.168.1.191 |
| 4 | 192.168.1.192/26 | 192.168.1.193–192.168.1.254 | 192.168.1.255 |

### Key Pattern

The subnet increment is:

```text
64
```

So the network addresses are:

```text
0
64
128
192
```

---

## 10. Quick Subnetting Method

When solving a subnetting problem:

### Step 1
Identify the original network.

Example:

```text
192.168.1.0/24
```

### Step 2
Determine how many hosts each subnet needs.

### Step 3
Calculate the required host bits.

```text
Usable hosts = 2^h - 2
```

### Step 4
Choose the smallest subnet that provides enough usable addresses.

### Step 5
Find the subnet block size/increment.

### Step 6
Write:

- Network address
- First usable address
- Last usable address
- Broadcast address

### Step 7
Repeat for the remaining subnets.

---

## 11. Important Formulas

### Host Bits

```text
Host bits = 32 - prefix length
```

### Total Addresses

```text
Total addresses = 2^host_bits
```

### Traditional Usable Hosts

```text
Usable hosts = 2^host_bits - 2
```

### Number of Equal-Size Subnets

If borrowing `n` bits:

```text
Number of subnets = 2^n
```

---

## 12. Key Takeaways

- **Subnetting** divides a network into smaller networks.
- **CIDR** uses prefix lengths such as `/24`, `/26`, and `/30`.
- Increasing the prefix length reduces the number of host addresses.
- `/26` provides **62 usable IPv4 host addresses** under traditional subnet rules.
- `/30` provides **2 usable host addresses**.
- `/31` can efficiently support point-to-point links when supported.
- `/32` represents a single IPv4 address/host route.
- `192.168.1.0/24` can be divided into four `/26` subnets.
- The four `/26` network addresses are:
  - `192.168.1.0`
  - `192.168.1.64`
  - `192.168.1.128`
  - `192.168.1.192`

---

## Day 12 Progress

Today I focused on understanding **why subnetting exists, how CIDR works, how to calculate usable hosts, and how a /24 network can be divided into smaller /26 subnets**.

The next step is to practice subnetting calculations and become faster at identifying:

```text
Network → Host Range → Broadcast → Next Subnet
```

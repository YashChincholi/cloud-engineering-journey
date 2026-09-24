# Day 13 — CCNA Subnetting: Class C, Class B & VLSM

## Overview

Today I continued my **CCNA 200-301 subnetting journey** and practiced subnetting for Class C and Class B networks, along with an introduction to **VLSM (Variable Length Subnet Masking)**.

The main goal was to become faster at calculating:

- Subnet/network addresses
- Broadcast addresses
- Usable host ranges
- Number of subnets
- Hosts per subnet
- Prefix lengths and subnet masks
- VLSM subnet allocation

---

## 1. Subnetting Fundamentals

**Subnetting** means dividing one larger IP network into multiple smaller networks called **subnets**.

When subnetting:

- Bits are borrowed from the host portion.
- Borrowed bits become part of the network/subnet portion.
- Remaining bits are used for hosts.
- More borrowed bits → more subnets.
- More host bits → more addresses per subnet.

### Important Formulas

#### Number of subnets

```text
Number of subnets = 2^borrowed_bits
```

#### Number of addresses per subnet

```text
Addresses per subnet = 2^host_bits
```

#### Usable hosts per subnet

```text
Usable hosts = 2^host_bits - 2
```

The `-2` accounts for:

- Network address
- Broadcast address

---

# 2. Prefix Length and Subnet Mask

CIDR notation tells us how many IPv4 bits belong to the network portion.

Example:

```text
/24
```

means:

```text
11111111.11111111.11111111.00000000
```

Subnet mask:

```text
255.255.255.0
```

Example:

```text
/23 = 255.255.254.0
```

---

# 3. Subnetting Trick — Finding the Increment

A useful shortcut is to calculate the **block size/increment**.

```text
Block size = 256 - subnet mask value in the interesting octet
```

Example:

```text
/27 = 255.255.255.224

256 - 224 = 32
```

So the subnet networks increase by:

```text
0, 32, 64, 96, 128, 160, 192, 224
```

This makes subnet identification much faster.

---

# 4. Day 13 Quiz Review

## Question

Divide:

```text
192.168.1.0/24
```

into **4 subnets**, with at least **45 hosts per subnet**.

### Step 1 — Hosts required

45 usable hosts are required.

```text
2^5 - 2 = 30   ❌
2^6 - 2 = 62   ✅
```

Therefore, 6 host bits are required.

That leaves:

```text
32 - 6 = /26
```

### Result

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

Each subnet provides:

```text
62 usable hosts
```

---

# 5. Practice — Five Equal Subnets

Network:

```text
192.168.255.0/24
```

Requirement:

```text
5 equal-size subnets
```

We need enough borrowed bits for at least 5 subnets.

```text
2^2 = 4   ❌
2^3 = 8   ✅
```

Borrow 3 bits.

Therefore:

```text
/24 + 3 = /27
```

Each `/27` subnet has:

```text
2^5 - 2 = 30 usable hosts
```

The subnet increment is:

```text
256 - 224 = 32
```

Possible `/27` subnets:

```text
192.168.255.0/27
192.168.255.32/27
192.168.255.64/27
192.168.255.96/27
192.168.255.128/27
192.168.255.160/27
192.168.255.192/27
192.168.255.224/27
```

Eight equal subnets are possible, so at least five can be selected.

---

# 6. Identifying the Subnet of a Host

To find which subnet a host belongs to:

1. Identify the prefix length.
2. Determine the subnet mask.
3. Find the block size.
4. Locate the range containing the host IP.
5. The first address in that range is the network address.

## Example 1

Host:

```text
192.168.5.57/27
```

Subnet mask:

```text
255.255.255.224
```

Block size:

```text
256 - 224 = 32
```

Subnet ranges:

```text
0–31
32–63
64–95
...
```

`57` falls inside:

```text
32–63
```

Therefore:

```text
Network:    192.168.5.32/27
Broadcast:  192.168.5.63
Usable:     192.168.5.33 - 192.168.5.62
```

---

## Example 2

Host:

```text
192.168.29.219/29
```

For `/29`:

```text
Subnet mask = 255.255.255.248
Block size = 256 - 248 = 8
```

Subnet ranges near 219:

```text
208–215
216–223
224–231
```

`219` falls in:

```text
216–223
```

Therefore:

```text
Network:    192.168.29.216/29
Broadcast:  192.168.29.223
Usable:     192.168.29.217 - 192.168.29.222
```

---

# 7. Class C Subnetting Reference Table

| Prefix | Subnet Mask | Addresses/Subnet | Usable Hosts |
|---|---|---:|---:|
| /25 | 255.255.255.128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 2 |

### Memory Pattern

Every time one host bit is borrowed:

```text
Subnets ×2
Addresses/subnet ÷2
```

---

# 8. Subnetting Class B Networks

The process is the same for Class B networks.

Example:

```text
172.16.0.0/16
```

Suppose we need:

```text
80 subnets
```

Find the number of borrowed bits:

```text
2^6 = 64   ❌
2^7 = 128  ✅
```

Borrow 7 bits:

```text
/16 + 7 = /23
```

Therefore:

```text
Prefix: /23
Subnet mask: 255.255.254.0
```

Host bits remaining:

```text
32 - 23 = 9
```

Usable hosts:

```text
2^9 - 2 = 510
```

So `/23` provides:

```text
128 possible subnets
510 usable hosts per subnet
```

---

# 9. Choosing the Correct Prefix Length

## Example 1

Network:

```text
172.22.0.0/16
```

Requirement:

```text
500 subnets
```

Find borrowed bits:

```text
2^8 = 256   ❌
2^9 = 512   ✅
```

Therefore:

```text
/16 + 9 = /25
```

Answer:

```text
/25
```

---

## Example 2

Network:

```text
172.18.0.0/16
```

Requirement:

```text
250 equal-size subnets
```

Find borrowed bits:

```text
2^7 = 128  ❌
2^8 = 256  ✅
```

Therefore:

```text
/16 + 8 = /24
```

Answer:

```text
/24
```

---

# 10. Identify the Subnet — Class B

Host:

```text
172.25.217.192/21
```

For `/21`:

```text
Subnet mask = 255.255.248.0
```

Block size in the third octet:

```text
256 - 248 = 8
```

Third-octet ranges include:

```text
208–215
216–223
224–231
```

`217` falls in:

```text
216–223
```

Therefore:

```text
Network:    172.25.216.0/21
Broadcast:  172.25.223.255
```

Usable range:

```text
172.25.216.1 - 172.25.223.254
```

---

# 11. Class B Host Calculation

The same formulas apply to Class B networks.

For every borrowed bit:

```text
Number of subnets doubles
```

For every remaining host bit:

```text
Number of addresses per subnet doubles
```

Usable hosts:

```text
2^host_bits - 2
```

---

# 12. Quiz Questions

## Quiz 1

Network:

```text
172.30.0.0/16
```

Requirement:

```text
100 subnets
At least 500 hosts per subnet
```

Find the appropriate prefix length.

### Solution

For 100 subnets:

```text
2^6 = 64   ❌
2^7 = 128  ✅
```

Borrow 7 bits:

```text
/16 + 7 = /23
```

Host bits:

```text
32 - 23 = 9
```

Usable hosts:

```text
2^9 - 2 = 510
```

Therefore:

```text
Prefix: /23
Subnet mask: 255.255.254.0
Usable hosts/subnet: 510
```

---

# 13. Quiz 2

Host:

```text
172.21.111.201/20
```

For `/20`:

```text
Subnet mask = 255.255.240.0
Block size = 256 - 240 = 16
```

Third-octet ranges near 111:

```text
96–111
112–127
```

`111` belongs to:

```text
96–111
```

Therefore:

```text
Network:    172.21.96.0/20
Broadcast:  172.21.111.255
```

---

# 14. Quiz 3

Find the broadcast address for:

```text
192.168.91.78/26
```

For `/26`:

```text
Mask = 255.255.255.192
Block size = 64
```

Subnets:

```text
0–63
64–127
128–191
192–255
```

`78` belongs to:

```text
64–127
```

Therefore:

```text
Network:    192.168.91.64
Broadcast:  192.168.91.127
```

---

# 15. Quiz 4

Divide:

```text
172.16.0.0/16
```

into:

```text
4 equal subnets
```

Need:

```text
2^2 = 4
```

Borrow 2 bits:

```text
/16 + 2 = /18
```

Subnet mask:

```text
255.255.192.0
```

Block size:

```text
256 - 192 = 64
```

Subnets:

```text
1. 172.16.0.0/18
2. 172.16.64.0/18
3. 172.16.128.0/18
4. 172.16.192.0/18
```

### Second subnet

```text
Network:    172.16.64.0
Broadcast:  172.16.127.255
```

---

# 16. Quiz 5

Network:

```text
172.30.0.0/16
```

Requirement:

```text
1000 hosts per subnet
```

Find the number of host bits required.

```text
2^9 - 2 = 510   ❌
2^10 - 2 = 1022 ✅
```

Therefore:

```text
10 host bits
```

Original network has:

```text
16 host bits
```

Borrow:

```text
16 - 10 = 6 bits
```

Number of subnets:

```text
2^6 = 64
```

Therefore:

```text
64 subnets
1022 usable hosts per subnet
Prefix: /22
```

---

# 17. FLSM vs VLSM

## FLSM — Fixed Length Subnet Mask

All subnets use the same prefix length.

Example:

```text
/26
/26
/26
/26
```

### Characteristics

- Same-size subnets
- Same number of host addresses
- Easier to plan
- Can waste IP addresses when requirements differ

---

## VLSM — Variable Length Subnet Mask

Different subnets can use different prefix lengths.

Example:

```text
/25
/26
/27
/28
/30
```

### Characteristics

- Different-size subnets
- Better IP address utilization
- Useful when departments/LANs need different numbers of hosts
- Requires more planning

---

# 18. VLSM Subnetting Process

When solving a VLSM problem:

### Step 1 — List host requirements

Example:

```text
110 hosts
45 hosts
29 hosts
8 hosts
2 hosts
```

### Step 2 — Sort from largest to smallest

```text
110
45
29
8
2
```

### Step 3 — Select the smallest subnet that can support each requirement

Use:

```text
2^host_bits - 2 >= required hosts
```

### Step 4 — Allocate from the beginning of the available address space

Do not overlap subnets.

### Step 5 — Record

For every subnet, calculate:

- Network address
- Prefix length
- Subnet mask
- First usable address
- Last usable address
- Broadcast address

---

# 19. VLSM Example

Network:

```text
192.168.1.0/24
```

Requirements:

| Network | Hosts Required | Prefix | Addresses | Usable Hosts |
|---|---:|---:|---:|---:|
| Tokyo LAN A | 110 | /25 | 128 | 126 |
| Toronto LAN B | 45 | /26 | 64 | 62 |
| Toronto LAN A | 29 | /27 | 32 | 30 |
| Tokyo LAN B | 8 | /28 | 16 | 14 |
| Point-to-Point | 2 | /30 | 4 | 2 |

### Allocation

#### 1. Tokyo LAN A

```text
Network:    192.168.1.0/25
Usable:     192.168.1.1 - 192.168.1.126
Broadcast:  192.168.1.127
```

#### 2. Toronto LAN B

```text
Network:    192.168.1.128/26
Usable:     192.168.1.129 - 192.168.1.190
Broadcast:  192.168.1.191
```

#### 3. Toronto LAN A

```text
Network:    192.168.1.192/27
Usable:     192.168.1.193 - 192.168.1.222
Broadcast:  192.168.1.223
```

#### 4. Tokyo LAN B

```text
Network:    192.168.1.224/28
Usable:     192.168.1.225 - 192.168.1.238
Broadcast:  192.168.1.239
```

#### 5. Point-to-Point Link

```text
Network:    192.168.1.240/30
Usable:     192.168.1.241 - 192.168.1.242
Broadcast:  192.168.1.243
```

Remaining addresses:

```text
192.168.1.244 - 192.168.1.255
```

These remain available for future allocation.

---

# 20. FLSM vs VLSM — Quick Comparison

| Feature | FLSM | VLSM |
|---|---|---|
| Subnet size | Same | Different |
| Prefix length | Same | Can differ |
| IP efficiency | Lower when requirements vary | Higher |
| Planning | Easier | More planning required |
| Typical use | Equal-size networks | Different host requirements |

---

# 21. Important Subnetting Shortcuts

### Shortcut 1 — Powers of 2

```text
2^1 = 2
2^2 = 4
2^3 = 8
2^4 = 16
2^5 = 32
2^6 = 64
2^7 = 128
2^8 = 256
2^9 = 512
2^10 = 1024
```

### Shortcut 2 — Usable Hosts

```text
Host bits → Usable hosts

1 → 0
2 → 2
3 → 6
4 → 14
5 → 30
6 → 62
7 → 126
8 → 254
9 → 510
10 → 1022
```

### Shortcut 3 — Block Size

```text
Block size = 256 - mask value
```

Examples:

```text
/26 → 255.255.255.192 → 64
/27 → 255.255.255.224 → 32
/28 → 255.255.255.240 → 16
/29 → 255.255.255.248 → 8
/30 → 255.255.255.252 → 4
```

---

# 22. Key Takeaways

- Subnetting divides a network into smaller networks.
- Borrowed bits determine the number of subnets.
- Remaining host bits determine addresses per subnet.
- Usable hosts = `2^host_bits - 2`.
- The block-size method makes subnet identification faster.
- Class B subnetting follows the same logic as Class C.
- Prefix length determines the subnet mask.
- FLSM uses equal-size subnets.
- VLSM allows different subnet sizes.
- In VLSM, allocate the largest subnet first.
- Always calculate network and broadcast addresses carefully.
- Practice is important for becoming fast at CCNA subnetting.

---

# Day 13 Learning Summary

Today I practiced:

- Class C subnetting
- Class B subnetting
- Finding subnet/network addresses
- Finding broadcast addresses
- Calculating usable hosts
- Calculating number of subnets
- Prefix length selection
- Block-size calculations
- FLSM vs VLSM
- VLSM subnet allocation

**Next step:** continue subnetting practice and implement a practical subnetting lab with multiple networks and assigned IP addresses.

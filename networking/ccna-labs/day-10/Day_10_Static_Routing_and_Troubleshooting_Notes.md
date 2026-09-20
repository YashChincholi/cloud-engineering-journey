# Day 10 — Static Routing & Troubleshooting Static Routes

> **CCNA 200-301 | Cisco Packet Tracer | IPv4 Static Routing**

## 1. Overview

Day 10 focused on **static routing** and, more importantly, on troubleshooting routing problems when end-to-end connectivity fails.

Two Packet Tracer labs were completed:

1. **Configuring Static Routes**
2. **Troubleshooting Static Routes**

The first lab focused on building a routed network and configuring static routes manually.

The second lab introduced intentional configuration errors and required a structured troubleshooting process to restore connectivity.

---

# Part 1 — Static Routing

## 2. What is Routing?

Routing is the process of forwarding packets from one network to another network.

A router examines the destination IP address of a packet and uses its **routing table** to determine where the packet should be forwarded.

Example:

```text
PC1
192.168.1.1/24
     |
     | 192.168.1.0/24
     |
    R1
     |
     | 192.168.12.0/24
     |
    R2
     |
     | 192.168.13.0/24
     |
    R3
     |
     | 192.168.3.0/24
     |
PC2
192.168.3.1/24
```

PC1 and PC2 belong to different IP networks, so routers are required for communication between them.

---

# 3. What is a Static Route?

A **static route** is a route manually configured by an administrator.

Cisco IOS syntax:

```cisco
ip route <destination-network> <subnet-mask> <next-hop-ip>
```

Example:

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.12.2
```

This tells R1:

> To reach `192.168.3.0/24`, forward the packet to the next-hop router at `192.168.12.2`.

---

# 4. Static Route Components

A static route normally contains:

| Component | Meaning |
|---|---|
| Destination network | Network we want to reach |
| Subnet mask | Identifies the destination network size |
| Next-hop IP | IP address of the next router |
| Exit interface | Interface through which the packet leaves |

Example:

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.12.2
```

- Destination: `192.168.3.0`
- Mask: `255.255.255.0`
- Next hop: `192.168.12.2`

---

# 5. Network Topology

The Day 10 topology used three routers connected in a chain.

```text
PC1
192.168.1.1/24
Gateway: 192.168.1.254
        |
        |
R1
G0/1: 192.168.1.254/24
G0/0: 192.168.12.1/24
        |
        |
   192.168.12.0/24
        |
        |
R2
G0/0: 192.168.12.2/24
G0/1: 192.168.13.2/24
        |
        |
   192.168.13.0/24
        |
        |
R3
G0/0: 192.168.13.3/24
G0/1: 192.168.3.254/24
        |
        |
PC2
192.168.3.1/24
Gateway: 192.168.3.254
```

---

# 6. IP Addressing Table

| Device | Interface | IP Address | Subnet Mask |
|---|---|---|---|
| PC1 | NIC | `192.168.1.1` | `255.255.255.0` |
| R1 | G0/1 | `192.168.1.254` | `255.255.255.0` |
| R1 | G0/0 | `192.168.12.1` | `255.255.255.0` |
| R2 | G0/0 | `192.168.12.2` | `255.255.255.0` |
| R2 | G0/1 | `192.168.13.2` | `255.255.255.0` |
| R3 | G0/0 | `192.168.13.3` | `255.255.255.0` |
| R3 | G0/1 | `192.168.3.254` | `255.255.255.0` |
| PC2 | NIC | `192.168.3.1` | `255.255.255.0` |

## Default Gateways

| Device | Default Gateway |
|---|---|
| PC1 | `192.168.1.254` |
| PC2 | `192.168.3.254` |

---

# 7. Configuring Router Interfaces

Before static routes can work, the router interfaces need correct IP addresses and must be enabled.

Basic Cisco IOS pattern:

```cisco
enable
configure terminal

interface gigabitEthernet 0/0
ip address <ip-address> <subnet-mask>
no shutdown
exit
```

Example:

```cisco
interface gigabitEthernet 0/0
ip address 192.168.12.1 255.255.255.0
no shutdown
```

---

# 8. Verifying Interfaces

A useful command for checking interface IP addresses and status is:

```cisco
show ip interface brief
```

Example information to check:

```text
Interface              IP-Address      Status
GigabitEthernet0/0      192.168.12.1    up
GigabitEthernet0/1      192.168.1.254   up
```

Important things to check:

- Correct IP address
- Correct subnet mask
- Interface status
- Protocol status

A working interface should normally show:

```text
Status: up
Protocol: up
```

---

# 9. Static Routes Used in the Lab

## R1

R1 needs a route to the `192.168.3.0/24` network.

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.12.2
```

Meaning:

```text
R1
 |
 | 192.168.12.2
 v
R2
 |
 v
192.168.3.0/24
```

---

## R2

R2 needs routes toward both end networks.

Route toward PC1's network:

```cisco
ip route 192.168.1.0 255.255.255.0 G0/0
```

Route toward PC2's network:

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.13.3
```

---

## R3

R3 needs a route back to PC1's network.

```cisco
ip route 192.168.1.0 255.255.255.0 192.168.13.2
```

---

# 10. Verifying the Routing Table

Use:

```cisco
show ip route
```

This displays the router's routing table.

Connected networks normally appear with:

```text
C
```

Local routes appear with:

```text
L
```

Static routes appear with:

```text
S
```

For example:

```text
S    192.168.3.0/24 [1/0] via 192.168.12.2
```

The `S` indicates that the route is static.

---

# 11. Testing Connectivity

From a PC, use:

```text
ping <destination-ip>
```

Example:

```text
ping 192.168.3.1
```

A successful end-to-end test should show replies from the destination.

The important test in this lab was:

```text
PC1 → PC2
192.168.1.1 → 192.168.3.1
```

---

# Part 2 — Troubleshooting Static Routes

# 12. Troubleshooting Scenario

The second lab contained intentional configuration problems.

The initial connectivity test from PC1 to PC2 failed.

The goal was not simply to change configurations randomly, but to identify the cause of the failure using a structured troubleshooting process.

---

# 13. Troubleshooting Workflow

The workflow used was:

```text
Symptom
   ↓
Connectivity Test
   ↓
Interface Verification
   ↓
Routing Table Inspection
   ↓
Identify Configuration Error
   ↓
Apply Fix
   ↓
Verify Fix
   ↓
Retest End-to-End Connectivity
```

This is a useful general troubleshooting pattern for networking and infrastructure work.

---

# 14. Initial Symptom

The first test was an end-to-end ping from PC1 to PC2.

```text
PC1 → PC2
192.168.1.1 → 192.168.3.1
```

The ping initially failed.

This established the symptom:

> **PC1 could not reach PC2 across the routed network.**

---

# 15. Problem 1 — Incorrect Static Route on R1

R1 had an incorrect next-hop address.

The expected next hop was:

```text
192.168.12.2
```

But the incorrect configuration referenced:

```text
192.168.12.3
```

The correct route is:

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.12.2
```

### Why this caused a problem

R1 needs to forward traffic for `192.168.3.0/24` toward R2.

R2's address on the `192.168.12.0/24` link is:

```text
192.168.12.2
```

Therefore, the next hop must point to R2.

---

# 16. Correcting the R1 Route

The incorrect route can be removed with:

```cisco
no ip route 192.168.3.0 255.255.255.0 192.168.12.3
```

Then the correct route can be configured:

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.12.2
```

Verify:

```cisco
show ip route
```

---

# 17. Problem 2 — Incorrect Static Route on R2

R2 contained an incorrect/duplicate route for:

```text
192.168.3.0/24
```

The incorrect route used:

```text
G0/0
```

as the exit interface.

However, the path toward the `192.168.3.0/24` network is through:

```text
G0/1
```

toward R3.

The correct route used:

```cisco
ip route 192.168.3.0 255.255.255.0 192.168.13.3
```

---

# 18. Removing the Incorrect R2 Route

Use the `no` form of the route command to remove an incorrect static route.

Example:

```cisco
no ip route 192.168.3.0 255.255.255.0 G0/0
```

Then verify:

```cisco
show ip route
```

The correct route should point toward R3.

---

# 19. Problem 3 — Incorrect IP Address on R3

R3's G0/0 interface had an incorrect IP address.

Expected:

```text
192.168.13.3
```

Incorrect:

```text
192.168.23.3
```

This created an addressing problem on the connection between R2 and R3.

---

# 20. Correcting R3 Interface Configuration

The interface was corrected to:

```cisco
interface gigabitEthernet 0/0
ip address 192.168.13.3 255.255.255.0
no shutdown
```

Then the interface status was verified using:

```cisco
show ip interface brief
```

---

# 21. R3 Static Route

R3 also needed a route back toward the PC1 network.

```cisco
ip route 192.168.1.0 255.255.255.0 192.168.13.2
```

This provides the return path:

```text
R3
 |
 | 192.168.13.2
 v
R2
 |
 v
R1
 |
 v
192.168.1.0/24
```

---

# 22. Final Verification

After correcting the routing and interface problems, the end-to-end connectivity test was performed again.

```text
PC1 → PC2
192.168.1.1 → 192.168.3.1
```

The final ping succeeded with:

```text
0% packet loss
```

This confirmed that the routing path and return path were working.

---

# 23. End-to-End Path

The successful packet path was:

```text
PC1
192.168.1.1
   |
   v
R1
192.168.1.254
   |
   | 192.168.12.0/24
   v
R2
   |
   | 192.168.13.0/24
   v
R3
192.168.3.254
   |
   v
PC2
192.168.3.1
```

The return traffic follows the reverse routing path.

---

# 24. Useful Cisco Troubleshooting Commands

## Check interface IP addresses and status

```cisco
show ip interface brief
```

Use this when checking:

- IP addresses
- Interface status
- Protocol status

---

## Check the routing table

```cisco
show ip route
```

Use this when checking:

- Connected networks
- Local routes
- Static routes
- Next hops
- Routing decisions

---

## Test connectivity

```cisco
ping <destination-ip>
```

Example:

```cisco
ping 192.168.3.1
```

---

## Check the running configuration

```cisco
show running-config
```

Useful for finding:

- Incorrect IP addresses
- Incorrect static routes
- Missing configuration
- Duplicate configuration

---

## Remove a configuration

Cisco IOS commonly uses:

```cisco
no <command>
```

Example:

```cisco
no ip route 192.168.3.0 255.255.255.0 G0/0
```

---

# 25. Troubleshooting Checklist

When a router cannot reach a remote network, check in this order:

### 1. Test connectivity

```cisco
ping <destination>
```

### 2. Check local interfaces

```cisco
show ip interface brief
```

Ask:

- Is the IP address correct?
- Is the interface up?
- Is the protocol up?

### 3. Check the routing table

```cisco
show ip route
```

Ask:

- Does the destination network exist?
- Is the route static?
- Is the next hop correct?
- Is the exit interface correct?

### 4. Check the configuration

```cisco
show running-config
```

Look for:

- Wrong IP addresses
- Wrong subnet masks
- Wrong next hops
- Wrong exit interfaces
- Missing routes

### 5. Apply the correction

Use the appropriate configuration command.

### 6. Verify

Run the relevant `show` command again.

### 7. Retest

```cisco
ping <destination>
```

---

# 26. Common Static Routing Mistakes

## Wrong next-hop address

Example:

```text
Correct: 192.168.12.2
Wrong:   192.168.12.3
```

Always verify the next-hop address against the topology.

---

## Wrong exit interface

A route can point out of the wrong interface.

Always check which interface actually leads toward the destination network.

---

## Incorrect interface IP

Even a small addressing mistake can break communication between directly connected routers.

Verify with:

```cisco
show ip interface brief
```

---

## Missing return route

It is not enough for traffic to reach the destination.

The destination must also have a route back to the source network.

Think:

```text
Forward path
Source → Destination

Return path
Destination → Source
```

Both paths matter.

---

## Testing only one part of the network

A successful ping between two routers does not automatically prove that PC1 can reach PC2.

Test progressively:

```text
PC → Local Router
        ↓
Router → Router
        ↓
Router → Remote LAN
        ↓
PC → Remote PC
```

---

# 27. Key Concepts Learned

### Static Routing

Routes can be manually configured using:

```cisco
ip route
```

### Routing Table

Routers use the routing table to determine where packets should go.

```cisco
show ip route
```

### Next Hop

The next-hop address identifies the router that should receive the packet next.

### Exit Interface

The exit interface identifies where traffic leaves the current router.

### End-to-End Connectivity

A complete communication path requires:

```text
Correct addressing
+
Correct interfaces
+
Forward route
+
Return route
```

### Troubleshooting

Instead of changing random configurations:

```text
Observe
→ Test
→ Inspect
→ Identify
→ Fix
→ Verify
→ Retest
```

---

# 28. Lab Files

## Configuring Static Routes

Packet Tracer lab:

```text
networking/ccna-labs/day-10/Configuring Static Routes/
```

## Troubleshooting Static Routes

Packet Tracer troubleshooting lab:

```text
networking/ccna-labs/day-10/Troubleshooting Static Routes/
```

The troubleshooting lab contains screenshots showing:

- Initial failed ping tests
- Initial routing-table inspection
- Route configuration errors
- Interface/address correction
- Route correction
- Final connectivity verification

---

# 29. Day 10 Summary

Day 10 moved from simply configuring a network to **finding and fixing network failures**.

### Configured

- Router interfaces
- IPv4 addresses
- Default gateways
- Static routes

### Verified

- Interface status
- Routing tables
- End-to-end connectivity

### Troubleshot

- Incorrect static next hop
- Incorrect exit interface
- Incorrect router interface IP
- Missing/corrected routing information

### Main workflow

```text
Configure
   ↓
Verify
   ↓
Test
   ↓
Troubleshoot
   ↓
Fix
   ↓
Verify
   ↓
Retest
```

The most important lesson from this lab was that troubleshooting should be **structured and evidence-driven**, rather than based on random configuration changes.

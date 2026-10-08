# Day 18 — VLANs and Inter-VLAN Routing (Part 3): Multilayer Switching with SVIs

## Overview

This lab builds on VLAN trunking and Router-on-a-Stick (ROAS) from the previous lab.

The main goal was to replace **Router-on-a-Stick inter-VLAN routing** with **Switch Virtual Interfaces (SVIs)** on a **Layer 3 / multilayer switch**.

### Main flow

```text
Router-on-a-Stick (ROAS)
        ↓
Remove router subinterfaces
        ↓
Create Layer 3 point-to-point link
        ↓
Enable IP routing on multilayer switch
        ↓
Configure SVIs for VLAN 10/20/30
        ↓
Inter-VLAN routing happens on SW2
        ↓
Default route sends external traffic to R1
```

---

## Lab Objectives

- Replace the existing ROAS configuration between R1 and SW2.
- Create a Layer 3 point-to-point connection between R1 and SW2.
- Convert SW2's router-facing interface from Layer 2 to Layer 3.
- Enable IP routing on the multilayer switch.
- Configure a default route from SW2 toward R1.
- Configure SVIs for VLANs 10, 20, and 30.
- Use the SVIs as default gateways for hosts.
- Verify inter-VLAN connectivity.
- Verify Internet/external connectivity.
- Observe the packet path in Packet Tracer Simulation Mode.
- Review a troubleshooting scenario involving incorrect VLAN assignment.

---

# 1. Network Addressing

| Network / VLAN | Subnet | Default Gateway / SVI |
|---|---|---|
| VLAN 10 | `10.0.0.0/26` | `10.0.0.62` |
| VLAN 20 | `10.0.0.64/26` | `10.0.0.126` |
| VLAN 30 | `10.0.0.128/26` | `10.0.0.190` |
| R1 ↔ SW2 | `10.0.0.192/30` | R1: `10.0.0.194`, SW2: `10.0.0.193` |

### /30 point-to-point link

```text
10.0.0.192/30

Network       10.0.0.192
SW2           10.0.0.193
R1            10.0.0.194
Broadcast     10.0.0.195
```

---

# 2. Lab Topology

```text
                         Internet
                            |
                            |
                           R1
                      G0/0: 10.0.0.194
                            |
                     L3 /30 point-to-point
                            |
                    G1/0/2: 10.0.0.193
                           SW2
                    Multilayer Switch
                       /          \
                      /            \
             SVI VLANs              Trunk
       VLAN10: 10.0.0.62              |
       VLAN20: 10.0.0.126            SW1
       VLAN30: 10.0.0.190          /  |  \
                                  PCs / VLANs
```

SW2 performs inter-VLAN routing locally using its SVIs.

---

# 3. Step 1 — Remove Router-on-a-Stick from R1

Previously, R1 used subinterfaces such as:

```text
G0/0.10
G0/0.20
G0/0.30
```

These subinterfaces were used for ROAS.

Remove them:

```cisco
enable
configure terminal

no interface g0/0.10
no interface g0/0.20
no interface g0/0.30
```

Verify:

```cisco
do show ip interface brief
```

The old ROAS subinterfaces should no longer be present in Packet Tracer.

### Configure the physical R1 interface

```cisco
interface g0/0
ip address 10.0.0.194 255.255.255.252
no shutdown
```

R1 now uses its physical interface as one side of the Layer 3 point-to-point link.

---

# 4. Step 2 — Convert SW2's Interface to Layer 3

The interface connected to R1 is:

```text
SW2 G1/0/2
```

First clear the previous configuration:

```cisco
enable
configure terminal

default interface g1/0/2
```

Then enter the interface:

```cisco
interface g1/0/2
```

A normal switchport operates at Layer 2, so an IP address cannot be configured directly.

Convert it into a routed Layer 3 interface:

```cisco
no switchport
```

Now configure the IP address:

```cisco
ip address 10.0.0.193 255.255.255.252
no shutdown
```

### Key concept

```text
switchport
    ↓
Layer 2 interface

no switchport
    ↓
Layer 3 routed interface
    ↓
Can have an IP address
```

---

# 5. Step 3 — Enable IP Routing on SW2

A multilayer switch can perform both Layer 2 switching and Layer 3 routing.

Enable Layer 3 routing globally:

```cisco
ip routing
```

Verify:

```cisco
do show ip route
```

The connected route for the `/30` network should appear.

> Packet Tracer may not display the local route exactly as real Cisco IOS/GNS3 would. This can be a simulator-specific difference.

---

# 6. Step 4 — Configure the Default Route on SW2

SW2 needs a path for destinations that are not in its local routing table.

Configure R1 as the next hop:

```cisco
ip route 0.0.0.0 0.0.0.0 10.0.0.194
```

Verify:

```cisco
do show ip route
```

Expected concept:

```text
S* 0.0.0.0/0 via 10.0.0.194
```

### Meaning

```text
Unknown destination
       ↓
SW2 default route
       ↓
10.0.0.194
       ↓
R1
       ↓
External network / Internet
```

---

# 7. Step 5 — Configure Switch Virtual Interfaces (SVIs)

An SVI is a virtual Layer 3 interface associated with a VLAN.

For this lab, the SVI becomes the **default gateway** for hosts inside each VLAN.

## VLAN 10

```cisco
interface vlan 10
ip address 10.0.0.62 255.255.255.192
no shutdown
```

## VLAN 20

```cisco
interface vlan 20
ip address 10.0.0.126 255.255.255.192
no shutdown
```

## VLAN 30

```cisco
interface vlan 30
ip address 10.0.0.190 255.255.255.192
no shutdown
```

### Gateway design

```text
VLAN 10 → 10.0.0.62
VLAN 20 → 10.0.0.126
VLAN 30 → 10.0.0.190
```

The lab uses the **last usable IP address** of each subnet as the SVI/default gateway.

---

# 8. Verify the SVIs

First confirm that the VLANs exist:

```cisco
show vlan brief
```

Then check interface status:

```cisco
show ip interface brief
```

Expected result:

```text
Vlan10    10.0.0.62     up    up
Vlan20    10.0.0.126    up    up
Vlan30    10.0.0.190    up    up
```

### Important SVI concept

An SVI will not normally become operational just because the IP address was configured. The associated VLAN must exist and have an active Layer 2 path according to the switch's operational conditions.

---

# 9. Important Verification Commands

## Check IP interfaces

```cisco
show ip interface brief
```

Useful for checking:

- Interface IP addresses
- Administrative status
- Line protocol status
- SVI status

## Check the routing table

```cisco
show ip route
```

Useful for checking:

- Connected routes
- Local routes
- Static routes
- Default route

## Check VLANs

```cisco
show vlan brief
```

Useful for checking:

- VLAN existence
- Access-port membership

## Check trunk ports

```cisco
show interfaces trunk
```

Useful for checking:

- Trunk interfaces
- Native VLAN
- Allowed VLANs
- Active VLANs

---

# 10. Inter-VLAN Connectivity Test

The lab tests connectivity from:

```text
PC7
IP: 10.0.0.3
VLAN: 10
```

to:

```text
PC3
IP: 10.0.0.129
VLAN: 30
```

Test:

```text
ping 10.0.0.129
```

The first ping may fail or time out because ARP resolution needs to happen before the devices can forward the traffic normally.

A second ping should succeed after the required MAC address information has been learned.

---

# 11. Packet Flow — PC7 to PC3

The important point is that **R1 does not route this inter-VLAN traffic**.

```text
PC7 (VLAN 10)
   |
   | ICMP Echo Request
   ↓
SW2 SVI VLAN 10
10.0.0.62
   |
   | Layer 3 routing inside SW2
   ↓
SW2 SVI VLAN 30
10.0.0.190
   |
   | VLAN 30 over trunk
   ↓
SW1
   |
   | Access port
   ↓
PC3 (VLAN 30)
10.0.0.129
```

The reply follows the reverse path:

```text
PC3
 ↓
SW1
 ↓
SW2
 ↓
VLAN 10 SVI
 ↓
PC7
```

### Key takeaway

```text
ROAS:
Host → Switch → Router → Switch → Host

SVI routing:
Host → Multilayer Switch → Switch → Host
```

For local inter-VLAN traffic, the multilayer switch can route the packet itself.

---

# 12. Internet Connectivity Test

After verifying inter-VLAN routing, test the default route:

```text
ping 1.1.1.1
```

Expected path:

```text
PC7
 ↓
SW2 SVI VLAN 10
 ↓
SW2 routing table
 ↓
Default route
 ↓
SW2 G1/0/2
10.0.0.193
 ↓
R1 G0/0
10.0.0.194
 ↓
Internet
 ↓
1.1.1.1
```

This proves that:

1. The host can reach its VLAN gateway.
2. SW2 can route traffic.
3. SW2 has a working default route.
4. R1 can forward the traffic externally.

---

# 13. Simulation Mode Observation

Packet Tracer Simulation Mode was used to observe the inter-VLAN packet path.

For the PC7 → PC3 test:

```text
PC7
 ↓
SW2
 ↓
SW1
 ↓
PC3
```

The packet does **not** travel through R1.

This visually confirms that SW2 is performing the inter-VLAN routing.

---

# 14. ROAS vs SVI Routing

| Feature | Router-on-a-Stick | SVI on Layer 3 Switch |
|---|---|---|
| Routing device | Router | Multilayer switch |
| VLAN gateways | Router subinterfaces | SVIs |
| Router physical interface | One trunk interface | Layer 3 routed link to switch |
| Inter-VLAN routing | Router | Layer 3 switch |
| Example | `G0/0.10` | `interface vlan 10` |
| VLAN traffic path | Host → Switch → Router → Switch | Host → L3 Switch |
| Typical benefit | Simple/small networks | Faster and scalable campus switching |

---

# 15. Troubleshooting Lab Preview

The video also previews a NetSim troubleshooting scenario involving broken inter-VLAN connectivity.

The reported problem:

> PC3 cannot ping other devices, including devices in its own VLAN.

## Step 1 — Check the PC configuration

PC3 had:

- Correct IP address
- Correct default gateway
- Incorrect subnet mask

It was configured as `/24` instead of the expected `/25`.

After correcting the subnet mask, connectivity still failed.

### Lesson

Do not stop troubleshooting after finding one incorrect configuration.

A network can have **multiple independent problems**.

---

# 16. Possible Layer 2 Causes of Inter-VLAN Problems

Two important possibilities identified during troubleshooting:

### Cause 1 — Wrong VLAN assignment

A host can have an IP address belonging to VLAN 10 while its switch port is actually assigned to another VLAN.

### Cause 2 — Incorrect trunk configuration

A trunk may have problems such as:

- Missing `switchport mode trunk`
- Required VLAN not allowed on the trunk
- Native VLAN mismatch

Useful command:

```cisco
show interfaces trunk
```

---

# 17. Troubleshooting the Wrong VLAN Assignment

On Switch2, the port connected to PC3 was:

```text
FastEthernet0/3
```

The documentation said PC3 should be in:

```text
VLAN 10
```

But the switch showed:

```text
FastEthernet0/3 → VLAN 12
```

This was the main reason PC3 could not communicate correctly.

## Check the port mode

```cisco
show interfaces f0/3 switchport
```

The port was operating as an access port.

## Correct the VLAN

```cisco
configure terminal
interface f0/3
switchport mode access
switchport access vlan 10
```

Verify:

```cisco
do show vlan brief
```

Expected:

```text
VLAN 10
  FastEthernet0/3
```

---

# 18. Troubleshooting Results

After correcting PC3's VLAN assignment:

```text
PC3 → Default Gateway     SUCCESS
PC3 → PC1 (same VLAN)    SUCCESS
PC3 → PC2/PC4            STILL FAILED
```

This demonstrates an important troubleshooting principle:

> Fixing one problem does not necessarily mean the entire network is fixed.

The preview continued by checking PC2 in VLAN 12.

PC2 could:

```text
PC2 → PC4       SUCCESS
```

but could not:

```text
PC2 → Default Gateway       FAILED
```

This indicated that another issue remained, which would be investigated in the next troubleshooting task.

---

# 19. Useful Troubleshooting Commands

### PC configuration

```text
ipconfig /all
```

### VLAN membership

```cisco
show vlan brief
```

### Trunk configuration

```cisco
show interfaces trunk
```

### Detailed switchport information

```cisco
show interfaces f0/3 switchport
```

### Interface status

```cisco
show ip interface brief
```

### Routing table

```cisco
show ip route
```

### Basic connectivity

```text
ping <destination-ip>
```

---

# 20. Key Concepts Learned

## 1. Layer 3 switch

A multilayer switch can perform both:

```text
Layer 2 switching
+
Layer 3 routing
```

## 2. Routed port

```cisco
no switchport
```

converts a switch interface into a Layer 3 routed interface.

## 3. SVI

```cisco
interface vlan 10
```

creates a virtual Layer 3 interface for VLAN 10.

The SVI can act as the default gateway for hosts in that VLAN.

## 4. `ip routing`

```cisco
ip routing
```

enables Layer 3 routing on the multilayer switch.

## 5. Default route

```cisco
ip route 0.0.0.0 0.0.0.0 10.0.0.194
```

sends unknown destinations toward R1.

## 6. Inter-VLAN routing

Traffic between VLANs can be routed directly by a multilayer switch instead of being sent to an external router.

---

# 21. Final Lab Flow

```text
Existing ROAS
    ↓
Remove R1 subinterfaces
    ↓
Configure R1 G0/0 = 10.0.0.194/30
    ↓
Reset SW2 G1/0/2
    ↓
no switchport
    ↓
Configure SW2 G1/0/2 = 10.0.0.193/30
    ↓
ip routing
    ↓
Configure default route → R1
    ↓
Configure VLAN 10/20/30 SVIs
    ↓
Verify SVI status
    ↓
PC7 → PC3 ping
    ↓
Inter-VLAN routing works through SW2
    ↓
PC7 → 1.1.1.1 ping
    ↓
External connectivity works
```

---

# 22. Evidence / Screenshots

Recommended screenshots for this lab:

1. `01_lab_topology_and_requirements.png`
2. `02_R1_removing_ROAS_subinterfaces.png`
3. `03_R1_configuring_L3_interface_G0-0.png`
4. `04_SW2_defaulting_interface_G1-0-2.png`
5. `05_SW2_verifying_cleared_G1-0-2_config.png`
6. `06_SW2_converting_G1-0-2_to_routed_port.png`
7. `07_SW2_assigning_IP_and_enabling_ip_routing.png`
8. `08_SW2_configuring_default_static_route.png`
9. `09_SW2_configuring_VLAN_SVIs.png`
10. `10_SW2_verifying_SVI_interface_status.png`
11. `11_PC7_inter_vlan_ping_to_PC3_verification.png`
12. `12_PC7_internet_ping_to_1-1-1-1_verification.png`

Video evidence:

```text
videos/13_simulation_inter_vlan_packet_flow.mp4
```

---

# 23. Key Takeaways

- **ROAS** uses router subinterfaces for inter-VLAN routing.
- **SVIs** allow a multilayer switch to act as the default gateway for VLANs.
- `no switchport` converts a Layer 2 switchport into a routed Layer 3 interface.
- `ip routing` enables routing on a multilayer switch.
- The SVI can use the last usable IP address of the VLAN subnet as the gateway.
- Local inter-VLAN traffic can stay on the multilayer switch instead of going through R1.
- A default route on SW2 sends external traffic toward R1.
- ARP can cause the first ping to time out before subsequent pings succeed.
- Troubleshooting should verify the **host, VLAN assignment, access port, trunk, and Layer 3 gateway** rather than assuming there is only one problem.

---

## Lab Files

```text
day-18/
├── images/
│   └── my-work/
├── videos/
│   └── 13_simulation_inter_vlan_packet_flow.mp4
├── Day 18 Lab - Multilayer Switching.pkt
└── Day_18_VLANs_and_InterVLAN_Routing_Part_3_Multilayer_Switching_Notes.md
```

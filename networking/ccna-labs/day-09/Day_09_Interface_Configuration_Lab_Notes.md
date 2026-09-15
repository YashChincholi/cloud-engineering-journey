\
# Day 09 — Cisco Interface Configuration Lab

## Overview

This lab focuses on practical Cisco interface configuration using Cisco Packet Tracer.

The lab uses:

- 1 router: **R1**
- 2 switches: **SW1** and **SW2**
- 4 PCs: **PC1–PC4**
- LAN: **172.16.0.0/16**
- Packet Tracer file: `Day 09 Lab - Interface Configuration.pkt`

The main objective is to configure device interfaces, assign IPv4 addresses, configure speed and duplex, add interface descriptions, disable unused interfaces, save the configurations, and verify the results.

---

## 1. Lab Objectives

By completing this lab, I practiced:

1. Configuring hostnames
2. Checking interface status
3. Assigning an IPv4 address to a router interface
4. Configuring interface speed
5. Configuring duplex
6. Adding interface descriptions
7. Enabling interfaces with `no shutdown`
8. Configuring multiple interfaces with `interface range`
9. Disabling unused interfaces with `shutdown`
10. Configuring static IPv4 addresses on PCs
11. Verifying interface configuration
12. Saving running configuration to startup configuration
13. Verifying saved configuration

---

## 2. Lab Topology

```text
                         R1
                    172.16.255.254/16
                         |
                         |
                        SW1
                      /    \
                    PC1    PC2
                         |
                         |
                        SW2
                      /    \
                    PC3    PC4
```

> The exact physical port mapping should be verified from the Packet Tracer topology and the lab instructions.

---

## 3. IPv4 Addressing

The lab uses the network:

```text
172.16.0.0/16
```

Subnet mask:

```text
255.255.0.0
```

The router interface used as the LAN gateway is:

```text
172.16.255.254/16
```

### PC addressing used in the lab

| Device | IPv4 Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| R1 G0/0 | `172.16.255.254` | `255.255.0.0` | — |
| PC1 | `172.16.0.1` | `255.255.0.0` | `172.16.255.254` |
| PC2 | `172.16.0.2` | `255.255.0.0` | `172.16.255.254` |
| PC3 | `172.16.0.3` | `255.255.0.0` | `172.16.255.254` |
| PC4 | `172.16.0.4` | `255.255.0.0` | `172.16.255.254` |

---

## 4. Cisco IOS Modes Used

### User EXEC mode

```text
R1>
```

### Privileged EXEC mode

```text
R1#
```

Enter it with:

```text
enable
```

### Global configuration mode

```text
R1(config)#
```

Enter it with:

```text
configure terminal
```

### Interface configuration mode

```text
R1(config-if)#
```

Enter it with:

```text
interface GigabitEthernet0/0
```

---

# 5. R1 Configuration

## 5.1 Configure hostname

```cisco
enable
configure terminal
hostname R1
```

---

## 5.2 Configure G0/0

The main LAN-facing interface was configured with:

```cisco
interface GigabitEthernet0/0
ip address 172.16.255.254 255.255.0.0
speed 1000
duplex full
description ## to SW1 ##
no shutdown
```

### What each command does

| Command | Purpose |
|---|---|
| `interface GigabitEthernet0/0` | Selects the interface |
| `ip address ...` | Assigns IPv4 address and subnet mask |
| `speed 1000` | Sets interface speed to 1000 Mbps |
| `duplex full` | Sets full-duplex operation |
| `description ...` | Documents the interface connection |
| `no shutdown` | Enables the interface |

---

## 5.3 Configure unused R1 interfaces

Unused GigabitEthernet interfaces were documented and disabled using an interface range.

Example:

```cisco
interface range GigabitEthernet0/1 - 2
description ## not in use ##
shutdown
```

This applies the same configuration to the selected interfaces.

---

## 5.4 Verify R1 interfaces

```cisco
show ip interface brief
```

Also inspect the running configuration:

```cisco
show running-config
```

The important things to verify are:

- Correct IP address
- Correct interface
- Interface enabled
- Description present
- Unused interfaces shut down

---

## 5.5 Save R1 configuration

```cisco
copy running-config startup-config
```

Then verify:

```cisco
show startup-config
```

---

# 6. Configure PC IPv4 Addresses

Each PC was configured with a static IPv4 address.

### PC1

```text
IP Address:      172.16.0.1
Subnet Mask:     255.255.0.0
Default Gateway: 172.16.255.254
```

### PC2

```text
IP Address:      172.16.0.2
Subnet Mask:     255.255.0.0
Default Gateway: 172.16.255.254
```

### PC3

```text
IP Address:      172.16.0.3
Subnet Mask:     255.255.0.0
Default Gateway: 172.16.255.254
```

### PC4

```text
IP Address:      172.16.0.4
Subnet Mask:     255.255.0.0
Default Gateway: 172.16.255.254
```

---

# 7. SW1 Configuration

## 7.1 Check initial interface status

```cisco
show interfaces status
```

This provides useful information about:

- Port
- Description/name
- Status
- VLAN
- Duplex
- Speed
- Interface type

---

## 7.2 Configure connected interfaces

The connected GigabitEthernet interfaces were configured with the required speed and duplex settings.

Example:

```cisco
interface GigabitEthernet0/1
speed 1000
duplex full
description ## to R1 ##
```

The second connected GigabitEthernet interface was configured similarly for the SW1–SW2 connection.

---

## 7.3 Add descriptions to PC-facing ports

The PC-facing FastEthernet ports were documented with descriptions.

Example:

```cisco
interface FastEthernet0/1
description ## to PC1 ##
```

and:

```cisco
interface FastEthernet0/2
description ## to PC2 ##
```

Descriptions do not change packet forwarding; they make the configuration easier to understand and troubleshoot.

---

## 7.4 Shut down unused ports

Unused ports were disabled using an interface range.

Example:

```cisco
interface range FastEthernet0/3 - 24
shutdown
```

This is more efficient than configuring every unused interface individually.

---

## 7.5 Verify SW1

```cisco
show interfaces status
```

Check the FastEthernet ports:

- Connected PC ports should show their expected status.
- Unused ports should show as disabled.

Check GigabitEthernet ports separately:

```cisco
show interfaces status
```

Verify:

- Connection status
- Description
- Duplex
- Speed

Also inspect:

```cisco
show running-config
```

---

## 7.6 Save SW1

```cisco
write memory
```

Then verify:

```cisco
show startup-config
```

---

# 8. SW2 Configuration

## 8.1 Check initial interface status

```cisco
show interfaces status
```

---

## 8.2 Configure the active GigabitEthernet interface

The active GigabitEthernet interface was configured with the required speed, duplex, and description.

Example:

```cisco
interface GigabitEthernet0/1
speed 1000
duplex full
description ## to SW1 ##
```

---

## 8.3 Configure PC-facing ports

Example:

```cisco
interface FastEthernet0/1
description ## to PC3 ##
```

```cisco
interface FastEthernet0/2
description ## to PC4 ##
```

---

## 8.4 Disable unused ports

The unused interfaces were disabled using an interface range.

Example:

```cisco
interface range GigabitEthernet0/2
shutdown
```

and the unused FastEthernet ports:

```cisco
interface range FastEthernet0/3 - 24
shutdown
```

---

## 8.5 Verify SW2

```cisco
show interfaces status
```

Then:

```cisco
show running-config
```

Verify the active ports, descriptions, speed/duplex settings, and shutdown interfaces.

---

## 8.6 Save SW2

```cisco
write memory
```

or the lab's demonstrated `write` command.

Then verify the startup configuration:

```cisco
show startup-config
```

---

# 9. Important Verification Commands

## `show ip interface brief`

Provides a quick view of:

```text
Interface
IP Address
Status
Protocol
```

Example:

```cisco
show ip interface brief
```

---

## `show interfaces status`

Useful for switch interfaces.

It shows information such as:

```text
Port
Name
Status
VLAN
Duplex
Speed
Type
```

---

## `show interfaces`

Provides detailed interface information and counters.

Useful when troubleshooting:

- CRC errors
- Runts
- Giants
- Input errors
- Output errors
- Collisions

---

## `show running-config`

Displays the configuration currently active in RAM.

```cisco
show running-config
```

---

## `show startup-config`

Displays the saved configuration that can be loaded after a reboot.

```cisco
show startup-config
```

---

# 10. Running Configuration vs Startup Configuration

A key concept practiced in this lab:

```text
Running Configuration
        |
        | copy running-config startup-config
        ↓
Startup Configuration
```

### Running configuration

The configuration currently being used by the device.

### Startup configuration

The saved configuration used when the device starts/reloads.

Therefore, after making important configuration changes, save them.

---

# 11. Speed and Duplex

This lab intentionally practices manual speed and duplex configuration.

Example:

```cisco
speed 1000
duplex full
```

### Speed

```text
1000 Mbps = 1 Gbps
```

### Duplex

```text
Full duplex
```

allows simultaneous transmission and reception.

---

## Important real-world note

The lab demonstrates manual speed/duplex configuration, but this should not be interpreted as a universal rule that speed and duplex must always be hardcoded.

Cisco's current documentation explains that compatible Ethernet devices can use autonegotiation and recommends keeping autonegotiation enabled for compliant devices in the relevant scenarios. If speed/duplex are manually configured, the settings should match on both ends of the link. citeturn0search0turn0search6

For this lab, the important learning objective is understanding what the `speed` and `duplex` commands do and how mismatches can affect a link.

---

# 12. Duplex Mismatch

A duplex mismatch can occur when one side uses:

```text
Full duplex
```

while the other uses:

```text
Half duplex
```

Possible symptoms include:

- Poor performance
- Collisions
- CRC/FCS errors
- Runts
- Retransmissions

Cisco documents duplex mismatch as a common cause of Ethernet performance problems. citeturn0search0

A safe configuration principle is:

```text
Both ends autonegotiate
        OR
Both ends use matching manual settings
```

---

# 13. Interface Descriptions

Descriptions were added to make the topology understandable from the CLI.

Example:

```cisco
description ## to R1 ##
```

or:

```cisco
description ## to PC1 ##
```

A good description answers:

> "What is connected to this interface?"

Cisco's interface documentation supports using descriptions and verifying them from the CLI. citeturn0search4

---

# 14. Interface Range

Instead of configuring interfaces individually:

```cisco
interface FastEthernet0/3
shutdown

interface FastEthernet0/4
shutdown

interface FastEthernet0/5
shutdown
```

we can select a range:

```cisco
interface range FastEthernet0/3 - 24
shutdown
```

This applies the command to all selected interfaces.

Cisco documents `interface range` specifically for applying the same configuration to multiple interfaces. citeturn0search3turn0search4

---

# 15. Troubleshooting Flow Used in the Lab

When an interface does not work:

```text
1. Check physical/topology connection
          ↓
2. show ip interface brief
          ↓
3. Check Status + Protocol
          ↓
4. Check speed/duplex
          ↓
5. Check interface description/config
          ↓
6. Check running configuration
          ↓
7. Test connectivity with ping
```

For switch ports:

```text
show interfaces status
```

is especially useful.

For deeper troubleshooting:

```text
show interfaces
```

can reveal interface counters and errors.

---

# 16. What I Practiced in Day 09

### Router

- Hostname configuration
- IPv4 interface configuration
- Speed configuration
- Duplex configuration
- Interface description
- `no shutdown`
- Unused interface shutdown
- Running configuration verification
- Startup configuration verification
- Saving configuration

### Switches

- Interface status inspection
- Interface descriptions
- Speed and duplex configuration
- PC-facing port configuration
- `interface range`
- Shutdown of unused interfaces
- Running configuration verification
- Startup configuration verification
- Saving configuration

### PCs

- Static IPv4 addressing
- Subnet mask
- Default gateway
- Connectivity testing

---

# 17. Day 09 Verification Checklist

```text
[✓] R1 configured
[✓] R1 G0/0 assigned 172.16.255.254/16
[✓] R1 G0/0 enabled
[✓] R1 interface descriptions configured
[✓] Unused R1 interfaces disabled
[✓] PC1 configured
[✓] PC2 configured
[✓] PC3 configured
[✓] PC4 configured
[✓] SW1 interfaces configured
[✓] SW1 unused ports disabled
[✓] SW2 interfaces configured
[✓] SW2 unused ports disabled
[✓] Interface status verified
[✓] Running configuration verified
[✓] Startup configuration verified
[✓] Configurations saved
[✓] Connectivity tested
```

---

## 18. Key Takeaways

### 1. Interfaces need to be configured and verified

Don't just enter commands. Always verify the resulting state.

### 2. `no shutdown` enables an interface

```cisco
no shutdown
```

### 3. `shutdown` disables an interface

```cisco
shutdown
```

### 4. `interface range` saves time

It allows the same configuration to be applied to multiple interfaces. citeturn0search3

### 5. Descriptions improve documentation

```cisco
description ## to SW1 ##
```

### 6. Save important configuration

```cisco
copy running-config startup-config
```

### 7. Verify after configuring

Useful commands:

```cisco
show ip interface brief
show interfaces status
show interfaces
show running-config
show startup-config
```

---

## 19. Day 09 Lab Files

```text
day-09/
├── Day 09 Lab - Interface Configuration.pkt
├── Day_09_Interface_Configuration_Lab_Notes.md
└── images/
    ├── my-work/
    │   ├── Packet_Tracer_Topology_and_Lab_Instructions.png
    │   ├── R1_G0-0_IP_Speed_Duplex_Description_and_Enable.png
    │   ├── R1_Interface_Range_Description_and_Running_Config_View.png
    │   ├── R1_Running_Config_Interfaces_View.png
    │   ├── R1_Copy_Running_Config_To_Startup_Config_and_Verification.png
    │   ├── PC1_Static_IP_Configuration.png
    │   ├── PC2_Static_IP_Configuration.png
    │   ├── PC3_Static_IP_Configuration.png
    │   ├── PC4_Static_IP_Configuration.png
    │   ├── SW1_Initial_Interface_Status.png
    │   ├── SW1_Interface_Descriptions_Speed_Duplex_Shutdown.png
    │   ├── SW1_Post_Config_Interface_Status_FastEthernet.png
    │   ├── SW1_Post_Config_Interface_Status_Gigabit.png
    │   ├── SW1_Save_Config_Write_Memory_and_Startup_Config.png
    │   ├── SW1_Running_Config_Interfaces_View.png
    │   ├── SW2_Initial_Interface_Status.png
    │   ├── SW2_Interface_Descriptions_Speed_Duplex_Shutdown.png
    │   ├── SW2_Post_Config_Interface_Status_FastEthernet.png
    │   └── SW2_Save_Config_Write_Memory_and_Startup_Config.png
    └── reference/
        └── Reference_Topology_and_IP_Addressing_Guide.png
```

---

## 20. Lab Completion

Day 09 connected several previous CCNA concepts into one practical exercise:

```text
IPv4 Addressing
      +
Ethernet
      +
LAN Switching
      +
Cisco IOS
      +
Interface Configuration
      +
Verification
      ↓
Practical Interface Configuration Lab
```

The main lesson is not just memorizing commands. The goal is to develop the habit of:

```text
Configure → Verify → Troubleshoot → Save
```

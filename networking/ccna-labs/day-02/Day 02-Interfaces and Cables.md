# Day 02 — Network Interfaces, Ethernet & Cables

## Overview

Day 2 focused on network interfaces, Ethernet standards, copper and fiber-optic cabling, cable types, pin roles, and selecting the appropriate cable for different network connections.

The concepts were then applied in Cisco Packet Tracer by building a network topology containing routers, switches, PCs, and a server.

---

## 1. Network Interfaces and Ports

Network devices use physical interfaces and ports to connect to other devices.

### RJ-45

* RJ-45 is the standard physical connector used for wired Ethernet connections over copper cabling.
* An RJ-45 connector has **8 pins**.
* UTP Ethernet cables contain **4 twisted pairs**, for a total of **8 wires**.
* Network interfaces must follow compatible standards so that connected devices can communicate.

---

## 2. Ethernet

Ethernet is a collection of networking standards and protocols used for communication over wired networks.

Data is transmitted as bits:

* `0`
* `1`

Network speed is measured in **bits per second (bps)**.

### Common Units

| Unit         |             Equivalent |
| ------------ | ---------------------: |
| Kilobit (Kb) |             1,000 bits |
| Megabit (Mb) |         1,000,000 bits |
| Gigabit (Gb) |     1,000,000,000 bits |
| Terabit (Tb) | 1,000,000,000,000 bits |

> **Note:** 1 byte = 8 bits. Network speeds are normally expressed in bits per second, while storage capacity is commonly expressed in bytes.

---

## 3. Ethernet Standards — Copper

The IEEE 802.3 family defines Ethernet standards, including Ethernet over copper twisted-pair cabling.

|    Speed | Common Name         | IEEE Standard | Informal Name | Maximum Length |
| -------: | ------------------- | ------------- | ------------- | -------------: |
|  10 Mbps | Ethernet            | 802.3         | 10BASE-T      |          100 m |
| 100 Mbps | Fast Ethernet       | 802.3u        | 100BASE-T     |          100 m |
|   1 Gbps | Gigabit Ethernet    | 802.3ab       | 1000BASE-T    |          100 m |
|  10 Gbps | 10 Gigabit Ethernet | 802.3an       | 10GBASE-T     |          100 m |

### Ethernet Naming

In names such as `1000BASE-T`:

* **1000** = 1000 Mbps
* **BASE** = baseband signaling
* **T** = twisted-pair cabling

For these copper Ethernet standards, the maximum cable length is **100 meters**.

---

## 4. UTP Cabling

**UTP** stands for **Unshielded Twisted Pair**.

A UTP cable contains:

* 4 twisted pairs
* 8 wires
* RJ-45 connector

The wires are twisted together to help reduce **Electromagnetic Interference (EMI)**.

### Pairs Used by Ethernet Standards

| Standard   | Pairs Used | Wires Used |
| ---------- | ---------: | ---------: |
| 10BASE-T   |    2 pairs |    4 wires |
| 100BASE-T  |    2 pairs |    4 wires |
| 1000BASE-T |    4 pairs |    8 wires |
| 10GBASE-T  |    4 pairs |    8 wires |

### Important Difference

**10BASE-T and 100BASE-T**

* Use 2 pairs
* Use 4 wires
* Pins `1,2` and `3,6`

**1000BASE-T and 10GBASE-T**

* Use all 4 pairs
* Use all 8 wires
* Support bidirectional transmission across the pairs

---

## 5. Transmit and Receive Pins

For 10/100BASE-T Ethernet, different device types have different transmit and receive pin assignments.

| Device Type | Transmit (Tx) Pins | Receive (Rx) Pins |
| ----------- | ------------------ | ----------------- |
| PC / Router | 1 and 2            | 3 and 6           |
| Switch      | 3 and 6            | 1 and 2           |

This difference is important when determining how the transmit and receive pairs should be connected.

### Full-Duplex Transmission

Full-duplex communication allows devices to transmit and receive data simultaneously.

---

## 6. Straight-Through Cable

A straight-through cable uses the same pin numbers on both ends.

### Pin Mapping

```text
1 → 1
2 → 2
3 → 3
6 → 6
```

Traditionally, straight-through cables are used to connect **different device types**.

### Examples

```text
PC → Switch
Router → Switch
```

---

## 7. Crossover Cable

A crossover cable swaps the transmit and receive pairs.

### Pin Mapping

```text
1 → 3
2 → 6
3 → 1
6 → 2
```

Traditionally, crossover cables are used to connect **similar device types**.

### Examples

```text
PC → PC
Switch → Switch
Router → Router
```

### Auto MDI-X

Modern network devices may support **Auto MDI-X**.

Auto MDI-X allows a device to automatically detect and compensate for transmit/receive pin differences.

For this lab, Auto MDI-X was assumed to be **disabled or unsupported**, so the appropriate cable type had to be selected manually.

---

## 8. Fiber-Optic Cabling

Fiber-optic cables transmit data using **light pulses through glass fiber** rather than electrical signals.

Fiber connections use separate strands for transmit and receive.

### Main Fiber Components

| Layer        | Description                                      |
| ------------ | ------------------------------------------------ |
| Core         | Glass fiber that carries the light               |
| Cladding     | Surrounds the core and reflects light internally |
| Buffer       | Protective layer around the fiber                |
| Outer Jacket | External protective covering                     |

---

## 9. Multimode vs Single-Mode Fiber

### Multimode Fiber

* Has a wider core.
* Supports multiple modes of light.
* Generally used for shorter distances.
* Lower cost compared with single-mode fiber.
* Suitable for short- to medium-distance network connections.

### Single-Mode Fiber

* Has a narrower core.
* Carries a single mode of light.
* Designed for longer distances.
* Uses laser-based transmission.
* Suitable for kilometer-scale connections.

### General Comparison

| Feature     | Multimode             | Single-Mode   |
| ----------- | --------------------- | ------------- |
| Core        | Wider                 | Narrower      |
| Light       | Multiple modes        | Single mode   |
| Distance    | Shorter               | Longer        |
| Cost        | Lower                 | Higher        |
| Typical Use | Short/medium distance | Long distance |

---

## 10. Fiber-Optic Ethernet Standards

| Standard    |   Speed | Fiber Type              |   Maximum Distance |
| ----------- | ------: | ----------------------- | -----------------: |
| 1000BASE-LX |  1 Gbps | Multimode / Single-Mode | 550 m MM / 5 km SM |
| 10GBASE-SR  | 10 Gbps | Multimode               |              400 m |
| 10GBASE-LR  | 10 Gbps | Single-Mode             |              10 km |
| 10GBASE-ER  | 10 Gbps | Single-Mode             |              30 km |

---

# 11. Cable Selection

The appropriate cable depends on the devices being connected and, for longer links, the required distance.

### End Device → Switch

Use a **straight-through cable**.

Examples:

```text
PC1 → SW3
PC2 → SW4
PC3 → SW7
SRV1 → SW8
```

### Switch → Switch

Use a **crossover cable** when Auto MDI-X is unavailable.

Examples:

```text
SW3 → SW1
SW1 → SW2
SW4 → SW2
SW7 → SW5
SW5 → SW6
SW8 → SW6
```

### Switch → Router

Use a **straight-through cable**.

Examples:

```text
SW1 → R2
SW2 → R2
SW5 → R4
SW6 → R4
```

### Router → Router

For copper connections, use a **crossover cable** when Auto MDI-X is unavailable.

The distance must also be considered when selecting the physical medium.

| Connection | Distance | Cable / Medium    |
| ---------- | -------: | ----------------- |
| R2 → R1    |     50 m | Copper crossover  |
| R1 → R3    |     3 km | Single-mode fiber |
| R3 → R4    |    250 m | Multimode fiber   |

---

# 12. Packet Tracer Lab

## Topology

The lab topology contains:

* 4 routers
* 8 switches
* 3 PCs
* 1 server

The devices were connected according to the required labels and distance requirements.

![Day 2 Network Topology](images/day-02-network-topology.png)

---

## 13. Connection Summary

### R1 → R2

```text
Distance: 50 meters
Medium: Copper
Cable: Crossover
```

The distance is within the 100-meter copper Ethernet limit.

### R1 → R3

```text
Distance: 3 kilometers
Medium: Fiber
Fiber Type: Single-Mode
```

Single-mode fiber was selected because the connection requires a kilometer-scale distance.

### R3 → R4

```text
Distance: 250 meters
Medium: Fiber
Fiber Type: Multimode
```

Multimode fiber was selected because the distance exceeds the practical 100-meter copper limit while remaining within the multimode range used in this lab.

---

# 14. Troubleshooting Experience

During the lab, I encountered an issue with the **R1-to-R3 fiber connection**.

The interfaces could report an `up/up` state from the CLI while the Packet Tracer visual link remained red.

I investigated several possible causes, including:

* Fiber module type
* Router slot selection
* Interface mapping
* Cable selection
* Speed and duplex settings
* Packet Tracer behavior with modular empty routers

This was useful because it showed that troubleshooting requires checking both the configuration and the physical/interface layer.

---

# 15. Key Takeaways

* Ethernet uses standardized physical and communication specifications.
* RJ-45 connectors contain 8 pins.
* UTP cables contain 4 twisted pairs and 8 wires.
* 10BASE-T and 100BASE-T use 2 pairs for Ethernet communication.
* 1000BASE-T and 10GBASE-T use all 4 pairs.
* PC/router and switch interfaces have different Tx/Rx pin assignments for 10/100BASE-T.
* Straight-through cables traditionally connect different device types.
* Crossover cables traditionally connect similar device types.
* Auto MDI-X can automatically compensate for cable pin differences on supported devices.
* Fiber-optic cables use light instead of electrical signals.
* Multimode fiber is generally used for shorter distances.
* Single-mode fiber is designed for longer distances.
* Cable selection depends on device type, pin roles, Ethernet standard, and distance.
* Packet Tracer can exhibit simulation-specific behavior that requires additional troubleshooting.

---

# 16. Learning Approach

For this stage, my focus is on first building the network **accurately** and understanding the physical connections between devices.

I will revisit these concepts later as I progress through CCNA and dive deeper into:

* Ethernet operation
* Physical-layer behavior
* Interface operation
* Standards
* Cable characteristics
* Troubleshooting
* How the underlying technologies actually work

The goal is to first develop a strong practical foundation and then progressively understand the deeper theory behind it.

---

## Lab File

Cisco Packet Tracer lab:

```text
../day-02/Day 02 Lab - Connecting Devices.pkt
```

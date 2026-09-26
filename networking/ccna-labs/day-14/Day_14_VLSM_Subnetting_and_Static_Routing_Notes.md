# Day 14 — VLSM Subnetting & Static Routing Lab

## Overview

Day 14 focused on applying **VLSM (Variable Length Subnet Masking)** in Cisco Packet Tracer and configuring the routers and end devices for end-to-end connectivity.

The lab used the `192.168.5.0/24` network and divided it into subnets of different sizes based on the host requirements.

## Lab Requirements

| Network | Hosts Required | Prefix | Network Address | Broadcast Address |
|---|---:|---:|---|---|
| LAN2 | 64 | `/25` | `192.168.5.0` | `192.168.5.127` |
| LAN1 | 45 | `/26` | `192.168.5.128` | `192.168.5.191` |
| LAN3 | 14 | `/28` | `192.168.5.192` | `192.168.5.207` |
| LAN4 | 9 | `/28` | `192.168.5.208` | `192.168.5.223` |
| R1 ↔ R2 | Point-to-Point | `/30` | `192.168.5.224` | `192.168.5.227` |

The subnets were allocated from the largest requirement to the smallest requirement.

## Addressing

### LAN2 — `/25`

- Network: `192.168.5.0/25`
- PC2: `192.168.5.1`
- R1 G0/1: `192.168.5.126`
- Mask: `255.255.255.128`

### LAN1 — `/26`

- Network: `192.168.5.128/26`
- PC1: `192.168.5.129`
- R1 G0/0: `192.168.5.190`
- Mask: `255.255.255.192`

### LAN3 — `/28`

- Network: `192.168.5.192/28`
- PC3: `192.168.5.193`
- R2 G0/0: `192.168.5.206`
- Mask: `255.255.255.240`

### LAN4 — `/28`

- Network: `192.168.5.208/28`
- PC4: `192.168.5.209`
- R2 G0/1: `192.168.5.222`
- Mask: `255.255.255.240`

### R1 ↔ R2 Point-to-Point — `/30`

- Network: `192.168.5.224/30`
- R1 G0/0/0: `192.168.5.225`
- R2 G0/0/0: `192.168.5.226`
- Mask: `255.255.255.252`

## Router Configuration

### R1

Configured:

- `G0/1` → `192.168.5.126/25`
- `G0/0` → `192.168.5.190/26`
- `G0/0/0` → `192.168.5.225/30`

Static routes:

```text
ip route 192.168.5.192 255.255.255.240 192.168.5.226
ip route 192.168.5.208 255.255.255.240 192.168.5.226
```

### R2

Configured:

- `G0/0` → `192.168.5.206/28`
- `G0/1` → `192.168.5.222/28`
- `G0/0/0` → `192.168.5.226/30`

Static routes:

```text
ip route 192.168.5.128 255.255.255.192 192.168.5.225
ip route 192.168.5.0 255.255.255.128 192.168.5.225
```

## Verification

Used:

```text
show ip interface
show ip route
```

The routing tables showed the connected/local networks and the configured static routes.

End-to-end connectivity was then tested between hosts.

## Key Learnings

- VLSM allows different subnet sizes to be used within the same `/24` network.
- Subnets should be allocated according to host requirements.
- The largest subnet was assigned first, followed by smaller requirements.
- The first usable address was assigned to the PC and the last usable address to the router in this lab.
- A `/30` subnet was used for the point-to-point R1–R2 link.
- Static routes were required so LANs behind different routers could reach each other.
- `show ip route` can be used to verify connected and static routes.

## Lab Structure

```text
day-14/
├── images/
│   └── my-work/
│       ├── network_topology_subnetting_assignment.png
│       ├── PC1_interface_ip_config.png
│       ├── PC2_gateway_settings.png
│       ├── PC2_interface_ip_config.png
│       ├── R1_g0-0_interface_config.png
│       ├── R1_g0-0-0_wan_interface_config.png
│       ├── R1_g0-1_interface_config.png
│       ├── R1_static_routes_routing_table.png
│       ├── R2_g0-0_interface_config.png
│       ├── R2_g0-0-0_wan_interface_config.png
│       ├── R2_g0-1_interface_config.png
│       └── R2_static_routes_routing_table.png
│
└── Day 14 Lab - VLSM.pkt
```

## Summary

Day 14 was about moving from subnetting theory into a complete practical implementation.

I calculated the VLSM subnets, configured router and PC addressing, created the point-to-point link, added static routes, and verified routing-table information and connectivity.

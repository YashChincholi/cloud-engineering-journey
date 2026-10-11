# Day 19 Lab --- Spanning Tree Protocol (STP)

## Overview

This lab uses Cisco Packet Tracer to analyze a four-switch topology and
identify the STP root bridge, port roles, and forwarding/blocking
behavior. CLI output is used to verify the port-role predictions.

**Lab file:** `Day 19 Lab - Analyzing STP.pkt`\
**Screenshots:** `images/my-work/`

## Objectives

-   Identify the root bridge by comparing bridge IDs.
-   Determine root, designated, and non-designated/alternate ports.
-   Understand how STP prevents Layer 2 switching loops.
-   Verify the topology with `show spanning-tree` commands.

## Key Concepts

### Why STP is needed

Redundant Layer 2 links improve resilience, but loops can cause:

-   **Broadcast storms:** Broadcast and unknown-unicast frames can
    circulate repeatedly because Ethernet frames do not have an IP-style
    TTL field.
-   **MAC address flapping:** A switch may learn the same source MAC
    address on different ports as looping frames arrive.
-   **Network congestion:** Looping traffic can overwhelm the LAN and
    prevent normal traffic from passing.

STP creates a loop-free logical topology by allowing some ports to
forward traffic and placing redundant paths into a blocking state. If an
active path fails, STP can reconverge and allow a backup path to
forward.

### Root bridge election

The switch with the **lowest Bridge ID** becomes the root bridge. The
Bridge ID is based on bridge priority, the extended system ID (VLAN ID),
and the switch MAC address.

1.  Compare bridge priority first.
2.  If priorities tie, compare MAC addresses.
3.  The lower Bridge ID wins.

In this lab, **SW3 is the root bridge**. The lab instructions show SW3
with priority `24577`, lower than the other switches' priorities.

All ports on the root bridge are designated ports and are in a
forwarding state.

### STP port roles

  -----------------------------------------------------------------------
  Port role               Purpose                 Typical state in this
                                                  lab
  ----------------------- ----------------------- -----------------------
  **Root Port (Root)**    The port on a non-root  Forwarding
                          switch that provides    
                          its best path toward    
                          the root bridge. Each   
                          non-root switch has one 
                          root port per           
                          spanning-tree instance. 

  **Designated Port       The forwarding port     Forwarding
  (Designated)**          selected for a network  
                          segment/collision       
                          domain. The root        
                          bridge's ports are all  
                          designated.             

  **Non-Designated /      A redundant port that   Blocking
  Alternate**             is not selected to      
                          forward, preventing a   
                          Layer 2 loop. Cisco     
                          output may label it     
                          `Altn`.                 
  -----------------------------------------------------------------------

### Root port selection

For each non-root switch, STP selects one root port using these criteria
in order:

1.  Lowest total root path cost.
2.  If tied, the neighbor switch with the lowest Bridge ID.
3.  If still tied, the lowest port ID on the **neighbor** switch.

Common STP interface costs covered in the course:

  Link speed                  STP cost
  ------------------------- ----------
  10 Mbps Ethernet                 100
  100 Mbps Fast Ethernet            19
  1 Gbps Gigabit Ethernet            4
  10 Gbps Ethernet                   2

### Designated and blocking port selection

For each remaining shared Layer 2 segment, one port is designated. The
switch with the lower root cost wins; if tied, the lower Bridge ID wins.
The other side becomes non-designated/alternate and blocks traffic.

## Lab Topology and Results

The topology contains four switches: SW1, SW2, SW3, and SW4.

### Root bridge

**SW3 --- Root Bridge**

-   SW3 has the lowest Bridge ID in the lab.
-   All STP-participating ports on SW3 are designated and forwarding.
-   Since SW3 is the root bridge, it does not have a root port.

### Verified port roles

  Switch   Interface                      STP role                     State
  -------- ------------------------------ ---------------------------- ------------
  SW3      STP-participating interfaces   Designated                   Forwarding
  SW1      Fa0/4                          Root                         Forwarding
  SW1      Fa0/1, Fa0/2, Fa0/3            Alternate / non-designated   Blocking
  SW2      Gi0/1                          Root                         Forwarding
  SW2      Fa0/1, Fa0/2                   Designated                   Forwarding
  SW2      Fa0/3                          Alternate / non-designated   Blocking
  SW4      Gi0/2                          Root                         Forwarding
  SW4      Gi0/1                          Designated                   Forwarding

Use the labeled topology screenshot and CLI screenshots as the visual
record of these results.

## Verification Commands

Run these commands from privileged EXEC mode (`Switch#`).

### Show STP roles and states

``` text
show spanning-tree
```

Shows the root ID, local bridge ID, interface role, state, interface
cost, and port ID for the selected VLAN.

### Show a particular VLAN

``` text
show spanning-tree vlan 1
```

Useful when a switch has multiple VLANs and you want to inspect the STP
instance for VLAN 1.

### Show STP summary

``` text
show spanning-tree summary
```

Summarizes STP mode and the number of interfaces in each STP state.

### Show detailed STP information

``` text
show spanning-tree detail
```

Provides additional information, including root-path details. In this
lab, the detailed output can help explain the total root path cost when
it is not obvious from the interface cost alone.

**Important:** The `Cost` column in the regular `show spanning-tree`
interface table is the cost of that local interface. It is not
necessarily the total root path cost. Use the detailed output when you
need the total root path cost.

## Screenshot Guide

Place screenshots in `images/my-work/` using these descriptive
filenames.

  ----------------------------------------------------------------------------------
  Filename                                       What it documents
  ---------------------------------------------- -----------------------------------
  `01_STP_Lab_Topology_and_Questions.png`        Four-switch topology, bridge
                                                 priorities/MAC addresses, and
                                                 worksheet questions.

  `02_STP_Topology_Port_Roles_Labeled.png`       Topology annotated with root,
                                                 designated, and alternate/blocked
                                                 roles.

  `03_SW3_Spanning_Tree_Root_Verification.png`   `show spanning-tree` output
                                                 confirming SW3 is the root bridge
                                                 and its ports are
                                                 designated/forwarding.

  `04_SW3_Spanning_Tree_Summary.png`             `show spanning-tree summary`
                                                 output, including STP mode and
                                                 state counts.

  `05_SW3_Spanning_Tree_Detail.png`              `show spanning-tree detail` output
                                                 and additional protocol
                                                 information.

  `06_SW1_Spanning_Tree_Port_Roles.png`          SW1's root port Fa0/4 and
                                                 alternate/blocking ports
                                                 Fa0/1--Fa0/3.

  `07_SW2_Spanning_Tree_Port_Roles.png`          SW2's root port Gi0/1, designated
                                                 Fa0/1--Fa0/2, and
                                                 alternate/blocking Fa0/3.

  `08_SW4_Spanning_Tree_Port_Roles.png`          SW4's root port Gi0/2 and
                                                 designated port Gi0/1.
  ----------------------------------------------------------------------------------

## Lab Checklist

-   [x] Identify SW3 as the root bridge.
-   [x] Mark all root-bridge ports as designated and forwarding.
-   [x] Identify one root port on each non-root switch.
-   [x] Identify designated and alternate/blocking ports on the
    remaining links.
-   [x] Verify the predicted roles with `show spanning-tree`.
-   [x] Review summary and detailed output.

## Quick Revision

-   **Lowest Bridge ID wins** the root bridge election.
-   The root bridge has **all designated ports** and no root port.
-   Each non-root switch chooses **one root port** for its best path to
    the root.
-   Each Layer 2 segment has one designated port.
-   Alternate/non-designated ports block to prevent loops.
-   STP prevents broadcast storms and MAC address flapping while
    preserving redundant paths.
-   Use `show spanning-tree`, `show spanning-tree summary`, and
    `show spanning-tree detail` to verify STP operation.

## Repository Location

Save this file alongside the Packet Tracer lab:

``` text
networking/
└── ccna-labs/
    └── day-19/
        └── Spanning Tree Protocol/
            ├── Day_19_STP_Lab_Notes.md
            ├── Day 19 Lab - Analyzing STP.pkt
            └── images/
                └── my-work/
```

# Day 19 -- DTP, VTP, Trunk Links, and VLAN Configuration

## Lab Overview

**Topic:** Cisco Dynamic Trunking Protocol (DTP) and VLAN Trunking
Protocol (VTP)\
**Lab environment:** Cisco Packet Tracer\
**Switches:** SW1, SW2, SW3\
**VLANs used:** VLAN 10, VLAN 20, VLAN 30, VLAN 40\
**Main goals:**

1.  Configure inter-switch links as static trunks and disable DTP
    negotiation.
2.  Configure SW1 as the VTP server and propagate VLANs 10, 20, and 30.
3.  Configure SW2 in VTP transparent mode and create VLAN 40 locally.
4.  Configure SW3 as a VTP client and verify that it cannot create VLANs
    locally.
5.  Configure host-facing ports as static access ports and assign them
    to the correct VLANs.

> **Note:** DTP and VTP are Cisco-proprietary protocols. Their basic
> functions are useful to understand for networking practice, even
> though they are not listed in the current CCNA 200-301 exam topics.

------------------------------------------------------------------------

## 1. Key Concepts

### DTP --- Dynamic Trunking Protocol

DTP allows Cisco switch interfaces to negotiate whether a link should
operate as an access port or a trunk port.

-   **Access port:** Carries traffic for one VLAN.
-   **Trunk port:** Carries traffic for multiple VLANs between network
    devices.
-   **Dynamic auto:** Passively waits for the connected interface to
    initiate trunk negotiation.
-   **Dynamic desirable:** Actively attempts to form a trunk.
-   **Static trunk:** Configured manually with `switchport mode trunk`.
-   **DTP hardening:** Use `switchport nonegotiate` on manually
    configured trunk links to stop DTP negotiation frames.

For this lab, inter-switch links are configured as static trunks, and
DTP negotiation is disabled.

### VTP --- VLAN Trunking Protocol

VTP can distribute VLAN database changes between switches in the same
VTP domain over trunk links.

  -----------------------------------------------------------------------
  VTP mode                            Main behavior
  ----------------------------------- -----------------------------------
  **Server**                          Can create, modify, and delete
                                      VLANs; advertises VLAN database
                                      changes.

  **Client**                          Synchronizes its VLAN database and
                                      cannot create VLANs locally.

  **Transparent**                     Maintains a local VLAN database;
                                      local VLAN changes are not
                                      advertised as its own database. It
                                      can forward VTP advertisements when
                                      applicable.
  -----------------------------------------------------------------------

Important points:

-   Switches must use the same VTP domain to synchronize in this lab.
-   VTP synchronizes the **VLAN database**, not the switchport-to-VLAN
    assignments.
-   Host-facing access ports must still be configured separately on each
    switch.
-   A VTP configuration revision number is used to identify VLAN
    database updates.
-   Changing a switch to VTP transparent mode resets its revision number
    to `0` in this lab.
-   A switch in VTP client mode cannot create VLANs manually.

------------------------------------------------------------------------

## 2. Lab Topology and Port Plan

### Inter-switch trunk links

  Switch   Interfaces to configure as trunks
  -------- -----------------------------------
  SW1      G0/1
  SW2      G0/1, G0/2
  SW3      G0/1

### Host-facing access ports

  Switch   Interface(s)     VLAN Purpose
  -------- -------------- ------ ----------------------
  SW1      Fa0/1--Fa0/2       10 End-host connections
  SW1      Fa0/3              20 End-host connection
  SW2      Fa0/1--Fa0/2       40 End-host connections
  SW3      Fa0/1              10 End-host connection
  SW3      Fa0/2--Fa0/3       30 End-host connections
  SW3      Fa0/4              20 End-host connection

------------------------------------------------------------------------

## 3. Step 1 --- Configure Trunks and Disable DTP

Before changing an interface, inspect its current switchport status.

### Verify SW1 G0/1

``` text
enable
configure terminal
do show interfaces g0/1 switchport
```

The initial output showed dynamic auto as the administrative mode,
static access as the operational mode, and trunk negotiation enabled.

Configure the interface:

``` text
interface g0/1
switchport mode trunk
switchport nonegotiate
end
show interfaces g0/1 switchport
```

**Expected verification:**

-   Administrative mode: trunk
-   Operational mode: trunk
-   Negotiation of Trunking: Off

### Configure SW2 G0/1 and G0/2

``` text
enable
configure terminal
interface range g0/1 - 2
switchport mode trunk
switchport nonegotiate
end
show interfaces g0/1 switchport
show interfaces g0/2 switchport
```

**Expected verification:** Both interfaces operate as trunks, and DTP
negotiation is disabled.

### Configure SW3 G0/1

``` text
enable
configure terminal
interface g0/1
switchport mode trunk
switchport nonegotiate
end
show interfaces g0/1 switchport
```

**Expected verification:** G0/1 operates as a trunk and trunk
negotiation is off.

### Why disable DTP?

Static trunk configuration makes the intended link mode explicit.
Disabling DTP prevents the interface from sending DTP negotiation
frames. This is part of the lab's switchport-hardening practice.

------------------------------------------------------------------------

## 4. Step 2 --- Configure SW1 as the VTP Server

Check the initial VTP status:

``` text
enable
configure terminal
do show vtp status
```

Set the VTP domain and create the required VLANs:

``` text
vtp domain CCNA
vlan 10
exit
vlan 20
exit
vlan 30
exit
end
show vtp status
show vlan brief
```

**Observed lab result:**

-   VTP domain: `CCNA`
-   VLANs 10, 20, and 30 were created on SW1.
-   The VTP configuration revision number increased to `3`, one
    increment for each VLAN created in this lab.
-   The switch showed 8 VLANs in total, including the existing default
    VLANs.

### Verify VTP synchronization on SW2

``` text
show vtp status
show vlan brief
```

SW2 joined the `CCNA` VTP domain and received VLANs 10, 20, and 30 from
SW1. Its revision number matched the server's revision number (`3`).

### Verify VTP synchronization on SW3

``` text
show vtp status
show vlan brief
```

SW3 also received VLANs 10, 20, and 30 through VTP.

**Learning point:** When the switches were in compatible VTP modes and
connected through trunk links, the VLAN database changes made on SW1
propagated to SW2 and SW3 without creating those VLANs individually on
each switch.

------------------------------------------------------------------------

## 5. Step 3 --- Configure SW2 as VTP Transparent and Create VLAN 40

On SW2:

``` text
enable
configure terminal
vtp mode transparent
vlan 40
end
show vtp status
show vlan brief
```

**Observed lab result:**

-   SW2 changed to VTP transparent mode.
-   VLAN 40 was added to SW2's local VLAN database.
-   The VTP revision number became `0`.
-   SW2 retained the VLANs it had previously learned.

### Verify SW1 and SW3

On SW1 and SW3, run:

``` text
show vlan brief
```

VLAN 40 did not appear in their VLAN databases.

**Why?** VLAN 40 was created locally on SW2 while it was in transparent
mode. SW2 did not advertise this local VLAN change as a VTP database
update. Transparent mode can still forward applicable VTP advertisements
between other switches; it simply does not synchronize its own VLAN
database like a VTP client.

------------------------------------------------------------------------

## 6. Step 4 --- Configure SW3 as a VTP Client

On SW3:

``` text
enable
configure terminal
vtp mode client
end
show vtp status
```

Then try to create VLAN 50:

``` text
configure terminal
vlan 50
```

**Observed result:** The switch rejected the VLAN creation attempt
because VTP clients cannot configure VLANs locally.

To add a VLAN that should propagate through VTP, create it on the VTP
server (SW1) and allow the client to synchronize the VLAN database.

------------------------------------------------------------------------

## 7. Step 5 --- Configure Host-Facing Access Ports

Host-facing ports should be explicitly configured as access ports and
assigned to their intended VLANs.

### SW3 --- Fa0/1 in VLAN 10

First inspect the interface:

``` text
enable
configure terminal
do show interfaces fa0/1 switchport
```

The initial output showed that trunk negotiation was on. Configure the
access port:

``` text
interface fa0/1
switchport mode access
switchport access vlan 10
end
show interfaces fa0/1 switchport
```

**Expected verification:** The port is a static access port assigned to
VLAN 10, and trunk negotiation is off.

### SW3 --- Fa0/2 and Fa0/3 in VLAN 30; Fa0/4 in VLAN 20

``` text
configure terminal
interface range fa0/2 - 3
switchport mode access
switchport access vlan 30
exit
interface fa0/4
switchport mode access
switchport access vlan 20
end
show vlan brief
```

### SW2 --- Fa0/1 and Fa0/2 in VLAN 40

``` text
configure terminal
interface range fa0/1 - 2
switchport mode access
switchport access vlan 40
end
show vlan brief
```

### SW1 --- Fa0/1 and Fa0/2 in VLAN 10; Fa0/3 in VLAN 20

``` text
configure terminal
interface range fa0/1 - 2
switchport mode access
switchport access vlan 10
exit
interface fa0/3
switchport mode access
switchport access vlan 20
end
show vlan brief
```

------------------------------------------------------------------------

## 8. Verification Checklist

Use this checklist after completing the configuration.

-   [ ] SW1 G0/1 is a trunk and DTP negotiation is off.
-   [ ] SW2 G0/1 and G0/2 are trunks and DTP negotiation is off.
-   [ ] SW3 G0/1 is a trunk and DTP negotiation is off.
-   [ ] SW1 uses VTP domain `CCNA` and has VLANs 10, 20, and 30.
-   [ ] SW2 and SW3 synchronized VLANs 10, 20, and 30 from SW1 before
    their VTP modes were changed.
-   [ ] SW2 is in transparent mode and has locally created VLAN 40.
-   [ ] VLAN 40 was not propagated as a local VLAN update to SW1 or SW3.
-   [ ] SW3 is in client mode and rejects local VLAN creation.
-   [ ] Host-facing interfaces are static access ports assigned to the
    correct VLANs.
-   [ ] `show vlan brief`, `show vtp status`, and
    `show interfaces <interface> switchport` show the expected
    configuration.

### Useful verification commands

``` text
show interfaces trunk
show interfaces g0/1 switchport
show vlan brief
show vtp status
```

------------------------------------------------------------------------

## 9. Lab Screenshot Index

Save the screenshots in this lab folder's `images/my-work/` directory
and rename them using the descriptive filenames below.

  ----------------------------------------------------------------------------------------------------
                            \# Suggested filename                                What it documents
  ---------------------------- ------------------------------------------------- ---------------------
                            01 `01_Lab_Topology_and_Instructions.png`            Complete topology,
                                                                                 VLANs, switches,
                                                                                 connected PCs, and
                                                                                 lab tasks.

                            02 `02_SW1_Initial_Interface_G0-1_Status.png`        SW1 G0/1 initial
                                                                                 dynamic auto/access
                                                                                 status and DTP
                                                                                 negotiation.

                            03 `03_SW1_Trunk_Mode_Configured.png`                SW1 G0/1 configured
                                                                                 as a trunk.

                            04 `04_SW1_DTP_Disabled_Nonegotiate.png`             DTP negotiation
                                                                                 disabled on SW1 G0/1.

                            05 `05_SW2_Trunk_and_DTP_Disabled.png`               SW2 G0/1--G0/2
                                                                                 configured as trunks
                                                                                 with DTP disabled.

                            06 `06_SW2_Interface_G0-2_Verification.png`          SW2 G0/2 trunk and
                                                                                 DTP status
                                                                                 verification.

                            07 `07_SW3_Trunk_and_DTP_Disabled.png`               SW3 G0/1 configured
                                                                                 as a trunk with DTP
                                                                                 disabled.

                            08 `08_SW1_VTP_Domain_Setup.png`                     SW1 VTP status and
                                                                                 `CCNA` domain setup.

                            09 `09_SW1_VLANs_Created_VTP_Revision_3.png`         VLANs 10, 20, and 30
                                                                                 created; revision
                                                                                 number 3.

                            10 `10_SW2_VTP_Synchronization_Status.png`           SW2 VTP
                                                                                 synchronization
                                                                                 status.

                            11 `11_SW2_VLAN_Database_Verification.png`           SW2 `show vlan brief`
                                                                                 confirms VLANs 10,
                                                                                 20, and 30.

                            12 `12_SW3_VLAN_Database_Verification.png`           SW3 VTP status and
                                                                                 VLAN database
                                                                                 verification.

                            13 `13_SW2_Transparent_Mode_and_VLAN40.png`          SW2 transparent mode
                                                                                 and local VLAN 40
                                                                                 creation.

                            14 `14_SW3_VTP_Server_Status_Check.png`              Check that VLAN 40
                                                                                 did not appear on
                                                                                 SW3.

                            15 `15_SW1_VLAN_Database_Unaffected_by_VLAN40.png`   Check that VLAN 40
                                                                                 did not appear on
                                                                                 SW1.

                            16 `16_SW3_VTP_Client_Mode_VLAN50_Error.png`         SW3 client mode and
                                                                                 rejected VLAN 50
                                                                                 creation.

                            17 `17_SW3_Access_Port_DTP_On_Check.png`             SW3 Fa0/1 before
                                                                                 configuration, with
                                                                                 DTP negotiation on.

                            18 `18_SW3_Access_Port_Hardened_Nonegotiate.png`     SW3 Fa0/1 configured
                                                                                 as access in VLAN 10;
                                                                                 DTP negotiation off.

                            19 `19_SW3_Host_Ports_Access_Configuration.png`      SW3 host-facing
                                                                                 access-port and VLAN
                                                                                 assignments.

                            20 `20_SW2_Host_Ports_Access_Configuration.png`      SW2 Fa0/1--Fa0/2
                                                                                 configured as access
                                                                                 ports in VLAN 40.

                            21 `21_SW1_Host_Ports_Access_Configuration.png`      SW1 host-facing
                                                                                 access-port and VLAN
                                                                                 assignments.
  ----------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 10. What I Learned

-   How to configure static trunk links between Cisco switches.
-   How to disable DTP negotiation using `switchport nonegotiate`.
-   How a VTP server distributes VLAN database updates to other
    switches.
-   The differences between VTP server, client, and transparent modes.
-   Why VLANs created locally in transparent mode are not propagated as
    the switch's own VTP database updates.
-   Why VTP clients cannot create VLANs locally.
-   How to assign host-facing switchports to specific VLANs as static
    access ports.
-   Why trunk configuration and access-port VLAN assignment must still
    be configured on the relevant interfaces even when VTP is used.

## Quick Revision

-   **DTP:** Negotiates access/trunk behavior between Cisco switches.
-   **`switchport mode trunk`:** Manually sets an interface to trunk
    mode.
-   **`switchport nonegotiate`:** Stops DTP negotiation frames on the
    interface.
-   **VTP server:** Creates and advertises VLAN database changes.
-   **VTP client:** Synchronizes the VLAN database; cannot create VLANs
    locally.
-   **VTP transparent:** Keeps a local VLAN database and does not
    advertise its own local VLAN changes.
-   **`show vlan brief`:** Verifies VLANs and port assignments.
-   **`show vtp status`:** Verifies VTP domain, mode, and revision
    information.
-   **`show interfaces <interface> switchport`:** Verifies switchport
    mode and DTP negotiation status.

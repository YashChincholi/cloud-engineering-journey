# Day 08 --- IPv4 Router Interface Configuration & Connectivity Lab

> **CCNA 200-301 --- Networking Journey**\
> **Day 08 focus:** Configuring IPv4 addresses on a Cisco router's
> GigabitEthernet interfaces, enabling interfaces, verifying the
> configuration, saving the configuration, configuring end devices, and
> testing connectivity with `ping`.

------------------------------------------------------------------------

## 1. Introduction

Day 07 introduced IPv4 addressing, the Network Layer, network/host
portions, subnet masks, and prefix lengths.

Day 08 puts those concepts into practice with a **Cisco Packet Tracer
router configuration lab**.

The lab uses one router (`R1`), three switches, and three PCs. Each PC
belongs to a different IPv4 network, and the router provides Layer 3
connectivity between those networks.

Cisco's standard interface configuration workflow is to enter privileged
EXEC mode, enter global configuration mode, select the interface,
configure its IP address, enable it with `no shutdown`, and then verify
the result with `show ip interface brief`.
citeturn0search1turn0search13

------------------------------------------------------------------------

## 2. Lab Topology

``` text
                         15.0.0.0/8
              PC1 ---------------- SW1
           15.0.0.1                  |
                                      |
                              G0/0 15.255.255.254
                                      |
                                      |
                                    R1
                                   /  \
                                  /    \
                  G0/1 182.98.255.254  G0/2 201.191.20.254
                              |                 |
                             SW2               SW3
                              |                 |
                          PC2 182.98.0.1    PC3 201.191.20.1
```

### Networks used

  -------------------------------------------------------------------------------------
  Network          Prefix         Subnet Mask       Router Interface   End Device
  ---------------- -------------- ----------------- ------------------ ----------------
  `15.0.0.0`       `/8`           `255.0.0.0`       G0/0 ---           PC1 ---
                                                    `15.255.255.254`   `15.0.0.1`

  `182.98.0.0`     `/16`          `255.255.0.0`     G0/1 ---           PC2 ---
                                                    `182.98.255.254`   `182.98.0.1`

  `201.191.20.0`   `/24`          `255.255.255.0`   G0/2 ---           PC3 ---
                                                    `201.191.20.254`   `201.191.20.1`
  -------------------------------------------------------------------------------------

> **Important:** The router interface address acts as the default
> gateway for the corresponding LAN.

------------------------------------------------------------------------

# 3. Lab Objectives

The lab tasks are:

1.  Configure R1's hostname.
2.  Use a `show` command to inspect R1's interfaces.
3.  Configure IPv4 addresses on R1's interfaces.
4.  Add interface descriptions.
5.  Enable the interfaces with `no shutdown`.
6.  Verify R1's interfaces.
7.  View the running configuration.
8.  Save the configuration.
9.  Configure IPv4 addresses on PC1, PC2, and PC3.
10. Test connectivity from PC1 to PC2 and PC3.

------------------------------------------------------------------------

# 4. IPv4 Addressing Plan

## R1 Interface Addressing

  ----------------------------------------------------------------------------------------------
  Interface              Connected Network   IP Address         Subnet Mask       Description
  ---------------------- ------------------- ------------------ ----------------- --------------
  `GigabitEthernet0/0`   `15.0.0.0/8`        `15.255.255.254`   `255.0.0.0`       `to SW1`

  `GigabitEthernet0/1`   `182.98.0.0/16`     `182.98.255.254`   `255.255.0.0`     `to SW2`

  `GigabitEthernet0/2`   `201.191.20.0/24`   `201.191.20.254`   `255.255.255.0`   `to SW3`
  ----------------------------------------------------------------------------------------------

## PC Addressing

  Device   IPv4 Address     Subnet Mask       Default Gateway
  -------- ---------------- ----------------- ------------------
  PC1      `15.0.0.1`       `255.0.0.0`       `15.255.255.254`
  PC2      `182.98.0.1`     `255.255.0.0`     `182.98.255.254`
  PC3      `201.191.20.1`   `255.255.255.0`   `201.191.20.254`

------------------------------------------------------------------------

# 5. Step 1 --- Enter Privileged EXEC Mode

Open the CLI of R1.

``` text
Router> enable
Router#
```

### What happened?

-   `Router>` is **User EXEC mode**.
-   `enable` moves the router into **Privileged EXEC mode**.
-   `Router#` indicates Privileged EXEC mode.

------------------------------------------------------------------------

# 6. Step 2 --- Enter Global Configuration Mode

``` text
Router# configure terminal
Router(config)#
```

Short form:

``` text
Router# conf t
Router(config)#
```

### What happened?

The router entered **global configuration mode**, where device-wide
configuration can be changed.

------------------------------------------------------------------------

# 7. Step 3 --- Check the Interfaces

Before configuring the interfaces, check their current state:

``` text
Router# show ip interface brief
```

A screenshot from the lab initially showed:

``` text
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     unassigned      YES unset  administratively down down
GigabitEthernet0/1     unassigned      YES unset  administratively down down
GigabitEthernet0/2     unassigned      YES unset  administratively down down
Vlan1                  unassigned      YES unset  administratively down down
```

### Why use this command?

`show ip interface brief` provides a quick summary of:

-   Interface name
-   IP address
-   Configuration method
-   Interface status
-   Line protocol status

Cisco documents this command as a way to display a brief summary of
interface IP information and status. citeturn0search0turn0search2

------------------------------------------------------------------------

# 8. Important CLI Mistake --- `show` Inside Configuration Mode

In the lab, the following command was attempted:

``` text
Router(config)# show ip interfaces
```

This produced an invalid-command message.

The same happened with:

``` text
Router(config)# show ip interface
```

### Why?

The router was still in:

``` text
Router(config)#
```

configuration mode.

A useful solution is to use `do` before an EXEC-mode `show` command:

``` text
Router(config)# do show ip interface brief
```

This allows the command to be executed without leaving configuration
mode.

### Alternative

Return to privileged EXEC mode:

``` text
Router(config)# end
Router#
```

Then run:

``` text
Router# show ip interface brief
```

------------------------------------------------------------------------

# 9. Step 4 --- Configure GigabitEthernet0/0

G0/0 connects R1 to SW1 and the `15.0.0.0/8` network.

``` text
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 15.255.255.254 255.0.0.0
Router(config-if)# description ## to SW1 ##
Router(config-if)# no shutdown
```

### Expected result

After `no shutdown`, the router reports messages similar to:

``` text
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0,
changed state to up
```

### Meaning

The interface has changed from administratively disabled to enabled.

Cisco's documented interface workflow includes selecting the interface,
assigning an IP address, using `no shutdown` to enable it, and verifying
it afterward. citeturn0search1turn0search13

------------------------------------------------------------------------

# 10. Step 5 --- Configure GigabitEthernet0/1

G0/1 connects R1 to SW2 and the `182.98.0.0/16` network.

``` text
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip address 182.98.255.254 255.255.0.0
Router(config-if)# description ## to SW2 ##
Router(config-if)# no shutdown
```

Expected status:

``` text
GigabitEthernet0/1
changed state to up
```

------------------------------------------------------------------------

# 11. Step 6 --- Configure GigabitEthernet0/2

G0/2 connects R1 to SW3 and the `201.191.20.0/24` network.

``` text
Router(config)# interface gigabitEthernet 0/2
Router(config-if)# ip address 201.191.20.254 255.255.255.0
Router(config-if)# description ## to SW3 ##
Router(config-if)# no shutdown
```

Expected status:

``` text
GigabitEthernet0/2
changed state to up
```

------------------------------------------------------------------------

# 12. Complete R1 Interface Configuration

The three interface configurations can be summarized as:

``` text
interface GigabitEthernet0/0
 description ## to SW1 ##
 ip address 15.255.255.254 255.0.0.0
 no shutdown

interface GigabitEthernet0/1
 description ## to SW2 ##
 ip address 182.98.255.254 255.255.0.0
 no shutdown

interface GigabitEthernet0/2
 description ## to SW3 ##
 ip address 201.191.20.254 255.255.255.0
 no shutdown
```

> Packet Tracer may also display `duplex auto` and `speed auto` in the
> running configuration. Those are normal interface settings.

------------------------------------------------------------------------

# 13. Why `no shutdown` Is Important

Cisco router interfaces can be administratively disabled.

When an interface is disabled, `show ip interface brief` can show:

``` text
administratively down
```

The command:

``` text
no shutdown
```

enables the interface.

A correctly connected and enabled interface should normally reach:

``` text
Status    up
Protocol  up
```

Cisco documentation specifically identifies `no shutdown` as the command
used to enable an interface. citeturn0search1turn0search13

------------------------------------------------------------------------

# 14. Step 7 --- Verify R1 Interfaces

After configuring all three interfaces:

``` text
Router# show ip interface brief
```

The completed lab showed:

``` text
Interface              IP-Address        OK? Method Status Protocol
GigabitEthernet0/0     15.255.255.254    YES manual up     up
GigabitEthernet0/1     182.98.255.254    YES manual up     up
GigabitEthernet0/2     201.191.20.254    YES manual up     up
Vlan1                  unassigned        YES unset  administratively down down
```

### What we verify

  Interface   Expected IP        Status   Protocol
  ----------- ------------------ -------- ----------
  G0/0        `15.255.255.254`   `up`     `up`
  G0/1        `182.98.255.254`   `up`     `up`
  G0/2        `201.191.20.254`   `up`     `up`

This is one of the most useful commands for quickly checking router
interface state. citeturn0search0

------------------------------------------------------------------------

# 15. Understanding `up/up`

When you see:

``` text
Status    up
Protocol  up
```

the interface is operational and its line protocol is operational.

### Simple interpretation

``` text
up / up
   ↓
Interface is enabled
   +
Physical/link and protocol conditions are working
```

If you instead see:

``` text
administratively down / down
```

the interface has normally been disabled with `shutdown`.

If you see:

``` text
down / down
```

investigate the physical/link connection, interface state, cabling, or
connected device.

------------------------------------------------------------------------

# 16. Step 8 --- View the Running Configuration

Use:

``` text
Router# show running-config
```

or:

``` text
Router# show run
```

The lab verified the configured interfaces in the running configuration.

Example:

``` text
interface GigabitEthernet0/0
 description ## to SW1 ##
 ip address 15.255.255.254 255.0.0.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 description ## to SW2 ##
 ip address 182.98.255.254 255.255.0.0
 duplex auto
 speed auto
!
interface GigabitEthernet0/2
 description ## to SW3 ##
 ip address 201.191.20.254 255.255.255.0
 duplex auto
 speed auto
```

### Why check the running configuration?

It confirms that:

-   The correct IP addresses were entered.
-   The correct subnet masks were entered.
-   Interface descriptions are present.
-   The interfaces were configured as expected.

------------------------------------------------------------------------

# 17. Running Configuration vs Startup Configuration

This is important for CCNA.

### Running Configuration

The **running-config** is the configuration currently active in RAM.

Command:

``` text
show running-config
```

Short form:

``` text
show run
```

### Startup Configuration

The **startup-config** is the saved configuration used when the device
boots.

Command:

``` text
show startup-config
```

### Simple memory trick

``` text
running-config
      ↓
currently running

startup-config
      ↓
saved configuration for startup
```

------------------------------------------------------------------------

# 18. Step 9 --- Save the Configuration

The lab used:

``` text
Router# write
Building configuration...
[OK]
```

This saves the current configuration.

Another common command is:

``` text
Router# copy running-config startup-config
```

You may also see:

``` text
Router# write memory
```

### Why save?

Without saving the configuration, configuration changes in
running-config can be lost after a reload or power cycle.

### Memory trick

``` text
configure
   ↓
running-config
   ↓
save
   ↓
startup-config
```

------------------------------------------------------------------------

# 19. Step 10 --- Configure PC1

PC1 was configured with:

``` text
IPv4 Address: 15.0.0.1
Subnet Mask: 255.0.0.0
Default Gateway: 15.255.255.254
```

### Packet Tracer steps

1.  Open PC1.
2.  Select **Desktop**.
3.  Open **IP Configuration**.
4.  Select **Static**.
5.  Enter the IPv4 address.
6.  Enter the subnet mask.
7.  Enter the default gateway.

### PC1 configuration

  Setting           Value
  ----------------- ------------------
  IPv4 Address      `15.0.0.1`
  Subnet Mask       `255.0.0.0`
  Default Gateway   `15.255.255.254`

------------------------------------------------------------------------

# 20. Step 11 --- Configure PC2

PC2 was configured with:

``` text
IPv4 Address: 182.98.0.1
Subnet Mask: 255.255.0.0
Default Gateway: 182.98.255.254
```

### PC2 configuration

  Setting           Value
  ----------------- ------------------
  IPv4 Address      `182.98.0.1`
  Subnet Mask       `255.255.0.0`
  Default Gateway   `182.98.255.254`

------------------------------------------------------------------------

# 21. Step 12 --- Configure PC3

PC3 was configured with:

``` text
IPv4 Address: 201.191.20.1
Subnet Mask: 255.255.255.0
Default Gateway: 201.191.20.254
```

### PC3 configuration

  Setting           Value
  ----------------- ------------------
  IPv4 Address      `201.191.20.1`
  Subnet Mask       `255.255.255.0`
  Default Gateway   `201.191.20.254`

------------------------------------------------------------------------

# 22. Why Do PCs Need a Default Gateway?

PCs can communicate directly with devices on their local subnet.

But when the destination is on another IP network, the PC needs a device
that can forward the packet toward another network.

That device is the **default gateway**.

In this topology:

``` text
PC1
15.0.0.1
   |
   | default gateway
   ↓
R1 G0/0
15.255.255.254
```

For PC2:

``` text
PC2
182.98.0.1
   |
   ↓
R1 G0/1
182.98.255.254
```

For PC3:

``` text
PC3
201.191.20.1
   |
   ↓
R1 G0/2
201.191.20.254
```

------------------------------------------------------------------------

# 23. Step 13 --- Test Connectivity with Ping

From PC1:

``` text
ping 182.98.0.1
```

and:

``` text
ping 201.191.20.1
```

The lab screenshot shows successful replies from both destinations after
an initial timeout.

Example:

``` text
Pinging 182.98.0.1 with 32 bytes of data:

Request timed out.
Reply from 182.98.0.1: bytes=32 time=10ms TTL=127
Reply from 182.98.0.1: bytes=32 time=13ms TTL=127
Reply from 182.98.0.1: bytes=32 time<1ms TTL=127
```

and:

``` text
Pinging 201.191.20.1 with 32 bytes of data:

Request timed out.
Reply from 201.191.20.1: bytes=32 time<1ms TTL=127
Reply from 201.191.20.1: bytes=32 time<1ms TTL=127
Reply from 201.191.20.1: bytes=32 time<1ms TTL=127
```

The first timeout can occur while the devices resolve Layer 2
information such as ARP before the subsequent ICMP packets succeed.

------------------------------------------------------------------------

# 24. How PC1 Reaches PC2

PC1:

``` text
15.0.0.1/8
```

PC2:

``` text
182.98.0.1/16
```

These are different networks.

The packet therefore needs to use PC1's default gateway:

``` text
15.255.255.254
```

The logical flow is:

``` text
PC1
15.0.0.1
   |
   v
SW1
   |
   v
R1 G0/0
15.255.255.254
   |
   | Layer 3 forwarding
   |
R1 G0/1
182.98.255.254
   |
   v
SW2
   |
   v
PC2
182.98.0.1
```

This demonstrates the Layer 3 role introduced in Day 07.

------------------------------------------------------------------------

# 25. How PC1 Reaches PC3

PC1:

``` text
15.0.0.1/8
```

PC3:

``` text
201.191.20.1/24
```

Again, the destination is on a different network.

Flow:

``` text
PC1
15.0.0.1
   |
   v
SW1
   |
   v
R1 G0/0
15.255.255.254
   |
   | Layer 3 forwarding
   |
R1 G0/2
201.191.20.254
   |
   v
SW3
   |
   v
PC3
201.191.20.1
```

------------------------------------------------------------------------

# 26. Why No Static Routes Were Required in This Lab

R1 has a directly connected interface in each of the three networks:

``` text
15.0.0.0/8
182.98.0.0/16
201.191.20.0/24
```

Because each network is directly connected to one of R1's active
interfaces, R1 can identify those networks as connected routes.

The important idea for this lab is:

``` text
One router
   |
   +---- 15.0.0.0/8
   |
   +---- 182.98.0.0/16
   |
   +---- 201.191.20.0/24
```

The router is acting as the Layer 3 connection point between the three
LANs.

------------------------------------------------------------------------

# 27. Prefix Length Review

The three networks use different prefix lengths.

## Network 1

``` text
15.0.0.0/8
```

Mask:

``` text
255.0.0.0
```

Network bits:

``` text
8
```

Host bits:

``` text
24
```

------------------------------------------------------------------------

## Network 2

``` text
182.98.0.0/16
```

Mask:

``` text
255.255.0.0
```

Network bits:

``` text
16
```

Host bits:

``` text
16
```

------------------------------------------------------------------------

## Network 3

``` text
201.191.20.0/24
```

Mask:

``` text
255.255.255.0
```

Network bits:

``` text
24
```

Host bits:

``` text
8
```

### Comparison

  Network            Prefix Mask                Network Bits   Host Bits
  ---------------- -------- ----------------- -------------- -----------
  `15.0.0.0`           `/8` `255.0.0.0`                    8          24
  `182.98.0.0`        `/16` `255.255.0.0`                 16          16
  `201.191.20.0`      `/24` `255.255.255.0`               24           8

------------------------------------------------------------------------

# 28. Key CCNA Concept --- Different Networks Need Layer 3

Compare:

``` text
PC1 = 15.0.0.1/8
PC2 = 182.98.0.1/16
```

The network portions are different:

``` text
15.0.0.0
182.98.0.0
```

Therefore they are on different IPv4 networks.

A router provides the Layer 3 forwarding function between them.

### Simple rule

``` text
Same subnet
   ↓
Direct/local communication

Different subnet
   ↓
Use a router/default gateway
```

------------------------------------------------------------------------

# 29. Important Commands Learned

  --------------------------------------------------------------------------
  Command                                Purpose
  -------------------------------------- -----------------------------------
  `enable`                               Enter privileged EXEC mode

  `configure terminal`                   Enter global configuration mode

  `interface gigabitEthernet 0/0`        Select an interface

  `ip address IP MASK`                   Assign an IPv4 address

  `description TEXT`                     Add an interface description

  `no shutdown`                          Enable an interface

  `show ip interface brief`              Quickly verify interface IP/status

  `do show ip interface brief`           Run the show command from config
                                         mode

  `show running-config`                  Display active configuration

  `show startup-config`                  Display saved startup configuration

  `write`                                Save the configuration

  `copy running-config startup-config`   Save running config to startup
                                         config

  `end`                                  Return to privileged EXEC mode

  `ping IP`                              Test IP connectivity
  --------------------------------------------------------------------------

Cisco's official configuration procedure uses the same core sequence of
`enable`, `configure terminal`, selecting the interface, assigning the
IP address, `no shutdown`, and verification with
`show ip interface brief`. citeturn0search1turn0search10

------------------------------------------------------------------------

# 30. Common Mistakes and Troubleshooting

## Mistake 1 --- Forgetting `no shutdown`

Symptom:

``` text
administratively down
```

Fix:

``` text
Router(config)# interface g0/0
Router(config-if)# no shutdown
```

------------------------------------------------------------------------

## Mistake 2 --- Wrong subnet mask

Example:

``` text
15.0.0.1
255.255.255.0
```

would place the PC in a different subnet interpretation from the lab's
intended `/8` network.

Correct PC1 configuration:

``` text
15.0.0.1
255.0.0.0
```

------------------------------------------------------------------------

## Mistake 3 --- Wrong default gateway

PC1 must use:

``` text
15.255.255.254
```

PC2 must use:

``` text
182.98.255.254
```

PC3 must use:

``` text
201.191.20.254
```

The gateway must be an IP address on the same subnet as the PC.

------------------------------------------------------------------------

## Mistake 4 --- Running `show` from the wrong mode

If you are here:

``` text
Router(config)#
```

use:

``` text
do show ip interface brief
```

or:

``` text
end
show ip interface brief
```

------------------------------------------------------------------------

## Mistake 5 --- Not saving the configuration

A correct running configuration is not enough if the device is reloaded
before saving.

Save it with:

``` text
copy running-config startup-config
```

or:

``` text
write
```

------------------------------------------------------------------------

## Mistake 6 --- Testing only one interface

Always verify all three:

``` text
show ip interface brief
```

Expected:

``` text
G0/0  15.255.255.254    up    up
G0/1  182.98.255.254    up    up
G0/2  201.191.20.254    up    up
```

------------------------------------------------------------------------

# 31. Verification Checklist

Before considering the lab complete, check:

-   [x] R1 interfaces identified.
-   [x] G0/0 configured with `15.255.255.254/8`.
-   [x] G0/1 configured with `182.98.255.254/16`.
-   [x] G0/2 configured with `201.191.20.254/24`.
-   [x] Interface descriptions added.
-   [x] All three interfaces enabled with `no shutdown`.
-   [x] `show ip interface brief` shows G0/0, G0/1, and G0/2 as `up/up`.
-   [x] Running configuration checked.
-   [x] Configuration saved with `write`.
-   [x] PC1 configured as `15.0.0.1/8`.
-   [x] PC2 configured as `182.98.0.1/16`.
-   [x] PC3 configured as `201.191.20.1/24`.
-   [x] PC1 can ping PC2.
-   [x] PC1 can ping PC3.

------------------------------------------------------------------------

# 32. What This Lab Demonstrates

This lab connects the theory from Day 07 with actual router
configuration.

### Day 07

``` text
IPv4
 ↓
Network + Host portions
 ↓
Subnet mask / prefix
 ↓
Different networks
```

### Day 08

``` text
IPv4 addressing
 ↓
Configure router interfaces
 ↓
Enable interfaces
 ↓
Configure default gateways
 ↓
Route between directly connected networks
 ↓
Ping across networks
```

------------------------------------------------------------------------

# 33. Day 08 Key Takeaways

1.  A router interface can be configured with an IPv4 address and subnet
    mask.
2.  `no shutdown` enables a router interface.
3.  `show ip interface brief` is a quick way to verify interface IP
    addresses and status.
4.  An interface showing `up/up` is operational at both the interface
    and line-protocol levels.
5.  An interface showing `administratively down` is disabled.
6.  Interface descriptions make configurations easier to understand and
    troubleshoot.
7.  A PC needs a correct default gateway to communicate with devices on
    different IP networks.
8.  R1 connects three different IPv4 networks in this lab.
9.  The router interface on each LAN acts as that LAN's default gateway.
10. `show running-config` displays the active configuration.
11. `write` saves the configuration.
12. `ping` can be used to verify end-to-end IP connectivity.
13. Different IPv4 networks require Layer 3 forwarding to communicate.
14. The subnet mask/prefix length determines the network boundary.

------------------------------------------------------------------------

# 34. Quick Revision Table

  Topic                       Remember
  --------------------------- ------------------------------------------------
  Layer used for IP routing   Layer 3 --- Network Layer
  Router                      Layer 3 device
  IPv4 address                32 bits
  `/8` mask                   `255.0.0.0`
  `/16` mask                  `255.255.0.0`
  `/24` mask                  `255.255.255.0`
  Enable interface            `no shutdown`
  Interface verification      `show ip interface brief`
  Active configuration        `show running-config`
  Saved configuration         `show startup-config`
  Save configuration          `write` / `copy running-config startup-config`
  Connectivity test           `ping`
  PC1 gateway                 `15.255.255.254`
  PC2 gateway                 `182.98.255.254`
  PC3 gateway                 `201.191.20.254`

------------------------------------------------------------------------

# 35. Practice Questions

## Q1. What command quickly displays the IP address and status of router interfaces?

**Answer:**

``` text
show ip interface brief
```

------------------------------------------------------------------------

## Q2. What command enables a router interface?

**Answer:**

``` text
no shutdown
```

------------------------------------------------------------------------

## Q3. What is the IP address of R1 G0/0 in this lab?

**Answer:**

``` text
15.255.255.254
```

------------------------------------------------------------------------

## Q4. What is the subnet mask of the G0/1 network?

**Answer:**

``` text
255.255.0.0
```

------------------------------------------------------------------------

## Q5. What is the default gateway of PC3?

**Answer:**

``` text
201.191.20.254
```

------------------------------------------------------------------------

## Q6. Why does PC1 need a default gateway to reach PC2?

**Answer:**

PC1 and PC2 are on different IPv4 networks, so PC1 sends traffic for the
remote network to its default gateway, R1 G0/0.

------------------------------------------------------------------------

## Q7. What does `up/up` mean in `show ip interface brief`?

**Answer:**

The interface is operational and the line protocol is operational.

------------------------------------------------------------------------

## Q8. What does `administratively down` usually indicate?

**Answer:**

The interface has been administratively disabled, commonly because
`shutdown` is configured.

------------------------------------------------------------------------

## Q9. What command saves the running configuration?

**Answer:**

``` text
copy running-config startup-config
```

or:

``` text
write
```

------------------------------------------------------------------------

## Q10. What command can be used from configuration mode to run a show command?

**Answer:**

``` text
do show ip interface brief
```

------------------------------------------------------------------------

# 36. Final Lab Command Sequence

For quick revision, the core router configuration is:

``` text
Router> enable
Router# configure terminal

Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 15.255.255.254 255.0.0.0
Router(config-if)# description ## to SW1 ##
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip address 182.98.255.254 255.255.0.0
Router(config-if)# description ## to SW2 ##
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface gigabitEthernet 0/2
Router(config-if)# ip address 201.191.20.254 255.255.255.0
Router(config-if)# description ## to SW3 ##
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# end

Router# show ip interface brief
Router# show running-config
Router# write
```

Then configure the PCs:

``` text
PC1
IP:      15.0.0.1
Mask:    255.0.0.0
Gateway: 15.255.255.254
```

``` text
PC2
IP:      182.98.0.1
Mask:    255.255.0.0
Gateway: 182.98.255.254
```

``` text
PC3
IP:      201.191.20.1
Mask:    255.255.255.0
Gateway: 201.191.20.254
```

Test:

``` text
PC1> ping 182.98.0.1
PC1> ping 201.191.20.1
```

------------------------------------------------------------------------

# 37. Lab Evidence

The screenshots below are the **my-work** evidence captured during the
Packet Tracer lab.

### Topology and Instructions

![Network topology and lab
instructions](images/my-work/Network_Topology_and_Task_Instructions.png)

### R1 Initial Interface Check and G0/0 Configuration

![R1 initial interface check and G0/0 IP
configuration](images/my-work/R1_Initial_Interface_Check_and_G0-0_IP_Config.png)

### R1 G0/0 Enabled

![R1 G0/0 enabled with no
shutdown](images/my-work/R1_G0-0_Interface_Enable_No_Shutdown.png)

### R1 G0/1 Configuration

![R1 G0/1 IP configuration, description and
enable](images/my-work/R1_G0-1_IP_Config_Description_and_Enable.png)

### R1 G0/2 Configuration

![R1 G0/2 IP configuration, description and
enable](images/my-work/R1_G0-2_IP_Config_Description_and_Enable.png)

### R1 Running Configuration

![R1 running configuration interface
section](images/my-work/R1_Running_Config_Interfaces_View.png)

### R1 Saved Configuration

![R1 configuration saved with
write](images/my-work/R1_Save_Configuration_Write.png)

### R1 Final Interface Verification

![R1 show ip interface brief
verification](images/my-work/R1_Show_IP_Interface_Brief_Verification.png)

### PC1 IP Configuration

![PC1 IP address
configuration](images/my-work/PC1_IP_Address_Configuration.png)

### PC2 IP Configuration

![PC2 IP address
configuration](images/my-work/PC2_IP_Address_Configuration.png)

### PC3 IP Configuration

![PC3 IP address
configuration](images/my-work/PC3_IP_Address_Configuration.png)

### PC1 Connectivity Test

![PC1 ping connectivity test to PC2 and
PC3](images/my-work/PC1_Ping_Connectivity_Test_to_PC2_and_PC3.png)

------------------------------------------------------------------------

# 38. Suggested Repository Structure

Place the Day 08 lab in:

``` text
networking/
└── ccna-labs/
    └── day-08/
        ├── images/
        │   └── my-work/
        │       ├── Network_Topology_and_Task_Instructions.png
        │       ├── PC1_IP_Address_Configuration.png
        │       ├── PC1_Ping_Connectivity_Test_to_PC2_and_PC3.png
        │       ├── PC2_IP_Address_Configuration.png
        │       ├── PC3_IP_Address_Configuration.png
        │       ├── R1_G0-0_Interface_Enable_No_Shutdown.png
        │       ├── R1_G0-1_IP_Config_Description_and_Enable.png
        │       ├── R1_G0-2_IP_Config_Description_and_Enable.png
        │       ├── R1_Initial_Interface_Check_and_G0-0_IP_Config.png
        │       ├── R1_Running_Config_Interfaces_View.png
        │       ├── R1_Save_Configuration_Write.png
        │       └── R1_Show_IP_Interface_Brief_Verification.png
        │
        └── Day_08_IPv4_Router_Interface_Configuration_and_Connectivity_Lab.md
```

------------------------------------------------------------------------

# 39. Day 08 Completion

``` text
Day 07
IPv4 fundamentals
       ↓
Day 08
Router interface configuration
       ↓
IP addresses + subnet masks
       ↓
no shutdown
       ↓
Interface verification
       ↓
PC default gateways
       ↓
Ping across different networks
       ↓
Layer 3 connectivity
```

> **Day 08 takeaway:** I moved from understanding IPv4 addressing to
> configuring IPv4 addresses on a Cisco router and verifying real
> connectivity between three different networks in Packet Tracer.

------------------------------------------------------------------------

## References

-   Cisco --- Initial router interface configuration and verification:
    https://www.cisco.com/c/en/us/td/docs/routers/access/4400/hardware/installation/guide4400-4300/C4400_isr/initconfig.html
-   Cisco --- Configuring IPv4 addresses:
    https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_ipv4/configuration/xe-16-11/ipv4-xe-16-11-book/configuring_ipv4_addresses.pdf
-   Cisco --- `show ip interface brief` reference:
    https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr/command/ipaddr-cr-book/ipaddr-r1.html

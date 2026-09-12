# Day 5 --- Ethernet LAN Switching & MAC Address Learning

> **CCNA Study Notes \| Day 5**
>
> **Topic:** Fundamentals of Ethernet LAN Switching, Ethernet Frames,
> MAC Addresses, and MAC Address Learning

------------------------------------------------------------------------

## 1. What I Learned Today

Today I focused on the **theory behind Ethernet LAN switching** and how
switches use **MAC addresses** to decide where Ethernet frames should be
sent.

The main concepts covered were:

-   OSI Layer 1 and Layer 2
-   Ethernet LANs
-   Ethernet frame structure
-   MAC addresses
-   Hexadecimal notation
-   OUI and BIA
-   Switch MAC address tables
-   Dynamic MAC address learning
-   Known unicast forwarding
-   Unknown unicast flooding
-   MAC address table aging
-   Frame forwarding across multiple switches

The most important idea from today:

> **A switch learns the source MAC address of incoming frames and uses
> its MAC address table to make forwarding decisions.**

------------------------------------------------------------------------

# 2. Ethernet and the OSI Model

Ethernet primarily operates at:

-   **Layer 1 --- Physical**
-   **Layer 2 --- Data Link**

## Layer 1 --- Physical Layer

The Physical Layer deals with the actual transmission of bits across a
physical medium.

Examples include:

-   Copper cables
-   Fiber-optic cables
-   Electrical/optical signals
-   Connectors
-   Voltage/signal characteristics
-   Physical transmission distance

For example, traditional Ethernet over twisted-pair copper commonly uses
a maximum cable length of approximately **100 meters** for a standard
Ethernet link.

### Layer 1 Question

Layer 1 is essentially concerned with:

> **How are bits physically transmitted?**

------------------------------------------------------------------------

## Layer 2 --- Data Link Layer

The Data Link Layer handles communication between devices on the same
local network segment and organizes data into **frames**.

Important Layer 2 concepts include:

-   Ethernet frames
-   MAC addresses
-   Switches
-   Frame forwarding
-   Error detection using FCS

### Layer 2 Question

Layer 2 is essentially concerned with:

> **Which device on the local network should receive this frame?**

------------------------------------------------------------------------

# 3. What Is a LAN?

**LAN = Local Area Network**

A LAN is a network connecting devices within a relatively limited area,
such as:

-   Home
-   Office
-   School
-   Small building
-   Campus environment

Ethernet switches are commonly used to connect devices within a LAN.

### Important Point

> **Adding switches does not automatically create separate LANs.**

For example:

``` text
PC1 â”€â”€ SW1 â”€â”€ SW2 â”€â”€ PC3
        â”‚       â”‚
       PC2     PC4
```

These devices can still belong to the same LAN/VLAN.

A **router interface** is what separates Layer 3 networks.

------------------------------------------------------------------------

# 4. Ethernet Frame

When data is transmitted using Ethernet, Layer 2 adds an Ethernet header
and trailer around the Layer 3 packet.

Conceptually:

``` text
Ethernet Frame
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚ Ethernet      â”‚ Packet        â”‚ Ethernet    â”‚
â”‚ Header        â”‚               â”‚ Trailer     â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

The Ethernet header contains important addressing information,
including:

-   Destination MAC address
-   Source MAC address
-   Type/Length field

The trailer contains:

-   FCS

------------------------------------------------------------------------

# 5. Ethernet Frame Fields

A simplified Ethernet frame contains the following fields:

  ------------------------------------------------------------------------
  Field                                         Size Purpose
  --------------------- ---------------------------- ---------------------
  Preamble                                   7 bytes Synchronizes the
                                                     receiver

  SFD                                         1 byte Indicates the start
                                                     of the frame

  Destination MAC                            6 bytes Identifies the
                                                     intended receiver

  Source MAC                                 6 bytes Identifies the sender

  Type/Length                                2 bytes Identifies
                                                     upper-layer protocol
                                                     or payload length

  Payload                                   Variable Encapsulated Layer 3
                                                     data

  FCS                                        4 bytes Error detection
  ------------------------------------------------------------------------

### Header + Trailer

The fields shown in the lesson give:

``` text
Preamble       = 7 bytes
SFD            = 1 byte
Destination    = 6 bytes
Source         = 6 bytes
Type           = 2 bytes
FCS            = 4 bytes
--------------------------------
Total          = 26 bytes
```

So:

> **Ethernet header + trailer = 26 bytes**

The payload/packet is carried between the header and trailer.

------------------------------------------------------------------------

# 6. Preamble

The **Preamble** is 7 bytes long.

Its purpose is to help the receiving device synchronize its clock with
the incoming Ethernet signal.

It consists of a repeating pattern of bits.

### Remember

> **Preamble = synchronization**

------------------------------------------------------------------------

# 7. Start Frame Delimiter (SFD)

The **SFD (Start Frame Delimiter)** is 1 byte.

It follows the preamble and indicates that the actual Ethernet frame is
beginning.

### Remember

> **SFD = Start Frame Delimiter = frame is about to begin**

------------------------------------------------------------------------

# 8. Destination MAC Address

The Destination MAC Address field is:

> **6 bytes = 48 bits**

It identifies the intended Layer 2 destination.

Example:

``` text
AAAA.AA00.0003
```

A switch examines the destination MAC address to determine how the frame
should be forwarded.

------------------------------------------------------------------------

# 9. Source MAC Address

The Source MAC Address field is also:

> **6 bytes = 48 bits**

It identifies the device that transmitted the frame.

Example:

``` text
AAAA.AA00.0001
```

### Very Important Switching Concept

When a switch receives a frame, it learns from the:

> **SOURCE MAC address**

Not the destination MAC address.

The switch records:

``` text
Source MAC  â†’  Incoming Interface
```

For example:

``` text
AAAA.AA00.0001 â†’ Fa0/1
```

This is called **dynamic MAC address learning**.

------------------------------------------------------------------------

# 10. Type / Length Field

The Ethernet field after the source MAC address is commonly called the
**Type/Length field**.

In Ethernet II, the field identifies the upper-layer protocol.

Examples:

  Value      Meaning
  ---------- ---------
  `0x0800`   IPv4
  `0x86DD`   IPv6

The field is 2 bytes (16 bits).

### Important Idea

The value can distinguish between:

-   An EtherType identifying the encapsulated protocol
-   A payload length in Ethernet formats where the field represents
    length

For CCNA study, remember the common Ethernet II examples:

``` text
0x0800  â†’ IPv4
0x86DD  â†’ IPv6
```

------------------------------------------------------------------------

# 11. Frame Check Sequence (FCS)

The **FCS (Frame Check Sequence)** is located in the Ethernet trailer.

Size:

> **4 bytes**

The sender calculates a value using a **CRC (Cyclic Redundancy Check)**
algorithm.

The receiver performs its own check to determine whether the frame was
corrupted during transmission.

### Important Point

FCS is used for:

> **Error detection**

It does not correct the corrupted frame.

If a frame fails the integrity check, the receiving device can discard
it.

------------------------------------------------------------------------

# 12. MAC Addresses

**MAC = Media Access Control**

An Ethernet MAC address is:

> **48 bits = 6 bytes**

A MAC address is normally written as 12 hexadecimal digits.

Examples:

``` text
AAAA.AA00.0001
AAAA.AA00.0002
AAAA.AA00.0003
AAAA.AA00.0004
```

Other common notation:

``` text
AA:AA:AA:00:00:01
```

------------------------------------------------------------------------

# 13. MAC Address Structure

A MAC address is 48 bits:

``` text
48 bits
â”Œâ”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”¬â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”
â”‚       First 24 bits   â”‚       Last 24 bits    â”‚
â”‚          OUI          â”‚ Device-specific part  â”‚
â””â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”´â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”˜
```

## OUI

**OUI = Organizationally Unique Identifier**

The first **24 bits (3 bytes)** identify the organization/manufacturer
associated with the MAC address allocation.

The remaining portion identifies the individual interface/address within
that allocation.

### Remember

> **OUI = first 24 bits / first 3 bytes**

------------------------------------------------------------------------

# 14. Burned-In Address (BIA)

A MAC address assigned to a network interface by the manufacturer is
commonly referred to as a:

> **BIA --- Burned-In Address**

MAC addresses are intended to be globally unique, although locally
administered addresses and virtualization can introduce exceptions to
the simple "manufacturer-burned-in and globally unique" model.

------------------------------------------------------------------------

# 15. Hexadecimal

MAC addresses are written using **hexadecimal** because hexadecimal
provides a compact way to represent binary values.

Hexadecimal uses 16 symbols:

``` text
0 1 2 3 4 5 6 7 8 9 A B C D E F
```

The letters represent:

``` text
A = 10
B = 11
C = 12
D = 13
E = 14
F = 15
```

------------------------------------------------------------------------

# 16. Hexadecimal and Binary

One hexadecimal digit represents:

> **4 bits**

For example:

``` text
Hex:       A
Decimal:   10
Binary:    1010
```

Another example:

``` text
Hex:       F
Decimal:   15
Binary:    1111
```

Therefore:

``` text
2 hexadecimal digits = 8 bits = 1 byte
```

This is why a 48-bit MAC address can be represented using:

``` text
48 Ã· 4 = 12 hexadecimal digits
```

------------------------------------------------------------------------

# 17. Hexadecimal Examples

### Example 1

``` text
Hex 10 = Decimal 16
```

Because:

``` text
1 Ã— 16 + 0 Ã— 1
= 16
```

### Example 2

``` text
Hex 1A = Decimal 26
```

Because:

``` text
1 Ã— 16 + A
= 16 + 10
= 26
```

### Example 3

``` text
Hex 17 = Decimal 23
```

Because:

``` text
1 Ã— 16 + 7
= 16 + 7
= 23
```

------------------------------------------------------------------------

# 18. Switch MAC Address Table

An Ethernet switch maintains a **MAC address table**.

It may also be called:

-   MAC address table
-   CAM table
-   MAC/CAM table

The table maps MAC addresses to switch interfaces.

Example:

  MAC Address   Interface
  ------------- -----------
  `.0001`       Fa0/1
  `.0002`       Fa0/2
  `.0003`       Fa0/3

The switch uses this table to decide where to send frames.

------------------------------------------------------------------------

# 19. Dynamic MAC Address Learning

This is one of the most important concepts from today's lesson.

Suppose PC1 sends a frame into SW1 through `Fa0/1`.

The frame contains:

``` text
Source MAC      = AAAA.AA00.0001
Destination MAC = AAAA.AA00.0003
```

SW1 receives the frame on `Fa0/1`.

The switch looks at the **source MAC** and learns:

``` text
AAAA.AA00.0001 â†’ Fa0/1
```

The switch has now learned where PC1 is located.

### Learning Rule

> **Source MAC + incoming interface = MAC table entry**

------------------------------------------------------------------------

# 20. What Does the Switch Do With the Destination MAC?

After learning the source MAC, the switch checks the destination MAC
against its MAC address table.

There are two important cases:

1.  Destination MAC is known
2.  Destination MAC is unknown

------------------------------------------------------------------------

# 21. Known Unicast

A **known unicast** occurs when the destination MAC address is already
present in the switch's MAC address table.

Example:

``` text
Destination MAC = AAAA.AA00.0003
```

And the table contains:

``` text
AAAA.AA00.0003 â†’ Fa0/3
```

The switch forwards the frame only toward:

``` text
Fa0/3
```

It does not need to send the frame out every other interface.

### Result

Efficient forwarding.

``` text
PC1 â”€â”€ SW1 â”€â”€â”€â”€â”€â”€â”€â”€â”€ PC3
        â”‚
        â””â”€â”€ frame sent toward PC3
```

------------------------------------------------------------------------

# 22. Unknown Unicast

An **unknown unicast** occurs when the destination MAC address is not
present in the switch's MAC address table.

Example:

``` text
Destination MAC = AAAA.AA00.0003
```

But SW1 does not yet know where `.0003` is located.

The switch then:

> **Floods the frame out all appropriate ports except the port on which
> the frame arrived.**

For example:

``` text
Frame arrives â†’ Fa0/1

Flood out:
Fa0/2
Fa0/3
Fa0/4
...
```

The incoming interface is not used for the flood.

------------------------------------------------------------------------

# 23. Why Does the Switch Flood?

The switch cannot directly forward the frame if it does not know where
the destination MAC address is located.

So it sends the frame out the other relevant interfaces.

This gives the destination device an opportunity to receive the frame.

Other devices that receive the frame but are not the intended
destination will discard it.

------------------------------------------------------------------------

# 24. Learning Through the Return Frame

An important detail:

If PC1 sends to PC3 and SW1 does not initially know PC3's MAC address,
the frame may be flooded.

When PC3 responds, the response frame has:

``` text
Source MAC = PC3's MAC
```

The switch receives that response and learns the location of PC3.

For example:

``` text
PC3 MAC â†’ Fa0/3
```

Now future frames destined for PC3 can be forwarded as known unicasts
instead of being flooded.

------------------------------------------------------------------------

# 25. Multi-Switch MAC Learning

MAC learning works across multiple interconnected switches.

Example:

``` text
PC1 â”€â”€ SW1 â”€â”€â”€â”€â”€â”€â”€â”€â”€ SW2 â”€â”€ PC3
```

Suppose PC1 sends a frame to PC3.

Initially, SW1 may know:

``` text
PC1 MAC â†’ Fa0/1
```

But it may not yet know PC3's MAC.

After the frame travels through the network, switches can learn MAC
addresses based on the source MAC addresses they receive.

SW1 can learn:

``` text
PC3 MAC â†’ interface toward SW2
```

SW2 can learn:

``` text
PC1 MAC â†’ interface toward SW1
```

This allows the switches to build a map of where devices are located.

------------------------------------------------------------------------

# 26. Example From Today's Topology

Consider:

``` text
PC1                  PC3
MAC .0001            MAC .0003
  â”‚                     â”‚
 Fa0/1                Fa0/1
  â”‚                     â”‚
 SW1 â”€â”€â”€â”€â”€ Fa0/3 â”€â”€â”€â”€ SW2
  â”‚                     â”‚
 Fa0/2                Fa0/2
  â”‚                     â”‚
 PC2                  PC4
MAC .0002            MAC .0004
```

If PC1 sends a frame to PC3:

``` text
Source      = .0001
Destination = .0003
```

SW1 already knows:

``` text
.0001 â†’ Fa0/1
```

So SW1 confirms the source location.

If `.0003` is unknown, SW1 treats the destination as an unknown unicast
and floods the frame out the appropriate interfaces other than Fa0/1.

When the frame reaches SW2, SW2 also learns the source MAC `.0001` on
the interface toward SW1.

If SW2 does not yet know `.0003`, it floods toward its other interfaces.

Once PC3 replies, the switches learn where `.0003` is located.

------------------------------------------------------------------------

# 27. MAC Address Table Aging

Dynamic MAC addresses are not kept forever.

Cisco switches commonly use an aging timer of:

> **300 seconds = 5 minutes**

If a dynamically learned MAC address is not seen for the aging period,
the entry can be removed.

This prevents the MAC address table from retaining stale information
indefinitely.

### Example

Initially:

``` text
AAAA.AA00.0003 â†’ Fa0/3
```

After sufficient inactivity:

``` text
Entry ages out
```

When traffic from that device appears again, the switch can relearn the
MAC address.

------------------------------------------------------------------------

# 28. Known vs Unknown Unicast

  -----------------------------------------------------------------------
  Feature                 Known Unicast           Unknown Unicast
  ----------------------- ----------------------- -----------------------
  Destination in MAC      Yes                     No
  table?                                          

  Switch knows            Yes                     No
  destination interface?                          

  Action                  Forward to specific     Flood out appropriate
                          interface               other interfaces

  Efficiency              High                    Lower

  MAC learning            Source MAC is learned   Source MAC is still
                                                  learned
  -----------------------------------------------------------------------

### Key Rule

> **The switch always learns from the source MAC, regardless of whether
> the destination is known.**

------------------------------------------------------------------------

# 29. The Switching Process --- Step by Step

When a switch receives an Ethernet frame:

### Step 1 --- Receive the frame

The frame enters through an interface.

``` text
Frame â†’ Switch
```

### Step 2 --- Learn the source MAC

The switch checks the source MAC.

``` text
Source MAC â†’ Incoming Interface
```

It adds or updates the MAC table.

### Step 3 --- Check the destination MAC

The switch searches its MAC address table.

### Step 4 --- Make a forwarding decision

If destination is known:

``` text
Forward out the destination interface
```

If destination is unknown:

``` text
Flood out appropriate interfaces
```

### Step 5 --- Destination receives the frame

The intended device accepts the frame.

Other devices discard frames that are not addressed to them.

------------------------------------------------------------------------

# 30. The Most Important Mental Model

Think of a switch as building a map:

``` text
MAC Address            Where?
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€         â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
.0001                  Fa0/1
.0002                  Fa0/2
.0003                  Fa0/3
.0004                  Fa0/4
```

The switch does **not** initially know every device's location.

It learns dynamically by inspecting the **source MAC address of incoming
frames**.

Then it uses that learned information to forward future frames
efficiently.

------------------------------------------------------------------------

# 31. Common Exam Traps

## Trap 1 --- Switch learns from destination MAC

âŒ Incorrect

The switch learns from the:

âœ… **Source MAC address**

------------------------------------------------------------------------

## Trap 2 --- Unknown unicast is discarded

âŒ Incorrect

An unknown
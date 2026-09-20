# INC-001 — Static Routing Connectivity Failure

## Incident Information

| Field       | Details                            |
| ----------- | ---------------------------------- |
| Incident ID | INC-001                            |
| Category    | Networking                         |
| Environment | Cisco Packet Tracer                |
| Type        | Troubleshooting Lab                |
| Status      | Resolved                           |
| Impact      | PC1 could not communicate with PC2 |

---

## 1. Summary

PC1 was unable to communicate with PC2 across a multi-router network.

The issue was investigated by testing connectivity, inspecting router interfaces and routing tables, identifying incorrect routing and interface configuration, applying fixes, and verifying end-to-end connectivity.

---

## 2. Symptom

The initial connectivity test from PC1 to PC2 failed.

```text
PC1 → PC2
Connectivity: FAILED
```

---

## 3. Investigation

The troubleshooting process followed a structured approach:

```text
Connectivity Test
        ↓
Check Router Interfaces
        ↓
Inspect Routing Tables
        ↓
Identify Incorrect Configuration
        ↓
Apply Fix
        ↓
Verify
        ↓
Retest Connectivity
```

### R1 Investigation

The routing table on R1 was inspected to verify the route toward the remote network.

An incorrect static route/next-hop configuration was identified.

### R2 Investigation

The routing configuration on R2 was inspected.

An incorrect static route using the wrong exit interface was identified.

### R3 Investigation

The interface and routing configuration on R3 were inspected.

An incorrect interface IP configuration was identified, along with the required routing configuration.

---

## 4. Root Cause

The connectivity failure was caused by multiple configuration errors across the routing path.

The affected configuration included:

* Incorrect static route information on R1
* Incorrect static route configuration on R2
* Incorrect interface IP configuration on R3
* Missing/corrective routing configuration required on R3

---

## 5. Remediation

The identified configuration issues were corrected.

The fixes included:

1. Correcting the static route on R1.
2. Removing the incorrect route configuration on R2.
3. Correcting the affected interface IP address on R3.
4. Adding/verifying the required static route on R3.
5. Rechecking the routing configuration after the changes.

---

## 6. Verification

After applying the corrections, end-to-end connectivity was tested again.

```text
PC1 → PC2
Connectivity: SUCCESS
Packet Loss: 0%
```

The final test confirmed that the routing path was functioning correctly.

---

## 7. Troubleshooting Workflow

The investigation followed this workflow:

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
Verify Configuration
   ↓
Retest Connectivity
```

---

## 8. Evidence

The original troubleshooting lab contains the supporting evidence and Packet Tracer file:

`networking/ccna-labs/day-10/Troubleshooting Static Routes/`

Key evidence includes:

* Initial failed ping
* R1 routing table inspection
* R1 route correction
* R2 route error discovery
* R2 route cleanup
* R3 interface and route inspection
* R3 IP correction and route addition
* Final end-to-end connectivity verification

---

## 9. Lessons Learned

This troubleshooting exercise reinforced the importance of:

* Testing connectivity before making configuration changes
* Checking interfaces before assuming the problem is routing
* Inspecting routing tables when remote networks are unreachable
* Verifying next-hop and exit-interface information
* Making one logical correction at a time
* Verifying configuration after applying a fix
* Performing an end-to-end connectivity test after remediation

---

## 10. Next Steps

Future incident reports will expand beyond static routing into areas such as:

* VLAN connectivity
* Inter-VLAN routing
* DHCP
* DNS
* Linux services
* Network troubleshooting
* AWS infrastructure
* Security configuration
* Application connectivity

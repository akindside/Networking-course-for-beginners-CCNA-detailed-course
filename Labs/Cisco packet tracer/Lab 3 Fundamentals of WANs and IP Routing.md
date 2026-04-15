## 1. Lab Overview and Learning Objectives

In the landscape of modern enterprise networking, mastering Wide-Area Network (WAN) technologies and IP routing is not just a requirement—it is the foundation of the internetwork itself. While Local Area Networks (LANs) provide connectivity within a single building, WANs bridge the vast distances between remote geographic sites. This lab is designed to take the theoretical frameworks of Chapter 3 and transform them into a functional, simulated environment. By constructing this topology in Cisco Packet Tracer, you will transition from observing "packets" as abstract concepts to watching them traverse multiple hops across diverse link types.

**Learning Objectives** By the end of this lab, you will be able to:

1. **Contrast Serial and Ethernet WAN links:** Identify the physical and logical differences between legacy Serial Leased Lines and modern Ethernet WAN services (E-Line).
2. **Visualize IP Encapsulation and De-encapsulation:** Observe the 4-step internal routing process, specifically how routers verify data integrity via the Frame Check Sequence (FCS) before forwarding.
3. **Verify End-to-End Connectivity:** Configure and utilize "helper protocols" like DNS and ARP to resolve names and hardware addresses, ensuring a complete path from source to destination.

To begin, we will gather our hardware and establish the physical "Points of Presence" (PoPs) that define our simulated world.

--------------------------------------------------------------------------------

## 2. Phase I: Constructing the Network Topology

In a production environment, hardware selection is driven by the specific link types provided by a Service Provider. We will use three routers to represent a Branch Office, a Service Provider PoP, and a Head Office. To mirror the real world, we must use specific cables: Serial cables for leased lines (requiring Serial interfaces) and Ethernet cables for modern WAN services.

### Device Inventory & Physical Layer Specs

|   |   |   |   |
|---|---|---|---|
|Device Type|Role|Physical Interface|Connection Type|
|**Router (R1)**|Branch Gateway|Serial 0/0/0 & GigabitEthernet 0/0|Serial Leased Line to R2|
|**Router (R2)**|Service Provider PoP|Serial 0/0/0 & GigabitEthernet 0/0|Serial to R1; Ethernet WAN to R3|
|**Router (R3)**|Head Office Gateway|GigabitEthernet 0/0 & 0/1|Ethernet WAN to R2|
|**PC1 & PC2**|Endpoints|FastEthernet 0|Standard LAN Connection|
|**DNS Server**|Name Resolution|FastEthernet 0|Head Office LAN (Subnet 4)|

### Packet Tracer Setup Instructions

1. **Deploy Hardware:** Drag three **2911 Routers** and two **Generic PCs** into the workspace. Add one **Server** for DNS services.
2. **Install Serial Modules:** Click R1 and R2. In the "Physical" tab, power the router **OFF**, drag an **HWIC-2T** module into an empty slot, and power it back **ON**.
3. **The Serial Link (R1 to R2):** Select the **Serial DCE** cable (red lightning bolt). Connect **Serial 0/0/0** on R1 to **Serial 0/0/0** on R2.
4. **The Ethernet WAN (R2 to R3):** Select the **Copper Straight-Through** cable. Connect **GigabitEthernet 0/0** on R2 to **GigabitEthernet 0/0** on R3. This represents an **E-Line Service**.
5. **Connecting the LANs:**
    - Connect PC1 to R1's **GigabitEthernet 0/1**.
    - Connect PC2 and the DNS Server to R3's **GigabitEthernet 0/1** via a switch (or directly if using multiple router ports).

With the physical layer established, the link lights may remain red. This is expected; we must now implement the logical addressing to bring the interfaces "Up."

--------------------------------------------------------------------------------

## 3. Phase II: IP Addressing and Subnetting Implementation

IP addresses are grouped into **subnets**, functioning much like a Postal ZIP Code system. This allows routers to forward traffic toward a "neighborhood" (the subnet) rather than needing a unique route for every single device on Earth.

### The Golden Rule of Subnetting

As a senior architect, you must internalize this rule: **Two IP addresses not separated by a router MUST be in the same subnet.** Conversely, any two interfaces separated by a router MUST be in different subnets. If PC1 and R1's local interface were in different subnets, PC1 would never send the packet to the router for delivery.

### Logical Addressing Schema & Interface Mapping

| Segment          | Subnet         | R1 Interface/IP     | R2 Interface/IP     | R3 Interface/IP   | Host IP           |
| ---------------- | -------------- | ------------------- | ------------------- | ----------------- | ----------------- |
| **Branch LAN**   | 150.150.1.0/24 | G0/1: 150.150.1.1   | -                   | -                 | PC1: 150.150.1.10 |
| **Serial Link**  | 150.150.2.0/24 | S0/0/0: 150.150.2.1 | S0/0/0: 150.150.2.2 | -                 | -                 |
| **Ethernet WAN** | 150.150.3.0/24 | -                   | G0/0: 150.150.3.1   | G0/0: 150.150.3.2 | -                 |
| **Head Office**  | 150.150.4.0/24 | -                   | -                   | G0/1: 150.150.4.1 | PC2: 150.150.4.10 |
| **DNS Server**   | 150.150.4.0/24 | -                   | -                   | -                 | DNS: 150.150.4.2  |

### Configuration Steps (CLI Best Practices)

On each router, navigate to the **CLI** tab and follow this professional sequence (example for R1 Serial):

```
Router> enable
Router# configure terminal
Router(config)# interface Serial0/0/0
Router(config-if)# ip address 150.150.2.1 255.255.255.0
Router(config-if)# encapsulation hdlc
Router(config-if)# no shutdown
Router(config-if)# exit
```

_Note: We use_ _**HDLC**_ _as our default data-link protocol for the serial link to ensure consistency across the WAN._

--------------------------------------------------------------------------------

## 4. Phase III: The Routing Journey (Encapsulation and De-encapsulation)

A router acts as a **Path Selector**. While the 20-byte IP header remains identical from the moment PC1 sends it until PC2 receives it (preserving the source and destination IPs), the Layer 2 "container" (the frame) is discarded and rebuilt at every single hop.

### The "Packet Walk" Walkthrough: PC1 to PC2

|   |   |   |   |
|---|---|---|---|
|Hop|Layer 2 Header Action|Layer 3 Header Action|Integrity Check|
|**PC1 → R1**|**Add Ethernet:** Dest MAC is R1's G0/1.|Source: 150.150.1.10; Dest: 150.150.4.10|PC1 calculates FCS.|
|**R1 (Internal)**|**Discard Ethernet:** Header stripped after check.|**Router matches 150.150.4.0** in Routing Table.|**Step 1:** R1 verifies FCS. If error, discard.|
|**R1 → R2**|**Add HDLC:** Encapsulates IP in an HDLC frame.|**Unchanged:** IP header persists.|R1 calculates new FCS.|
|**R2 → R3**|**Add Ethernet:** Dest MAC is R3's G0/0.|**Unchanged:** IP header persists.|R2 verifies FCS, then strips HDLC.|
|**R3 → PC2**|**Add Ethernet:** Dest MAC is PC2's MAC.|**Unchanged:** Packet reaches final destination.|R3 verifies FCS, then rebuilds for LAN.|

**The "So What?" of Encapsulation:** Layer 2 headers are transient; they exist only to move the packet across one specific wire. Layer 3 headers are persistent; they provide the end-to-end "Postal Address" that guides the packet through the entire internetwork.

--------------------------------------------------------------------------------

## 5. Phase IV: Helper Protocols and Verification (DNS, ARP, and Ping)

IP routing relies on a suite of "Helper Protocols" to resolve names and physical addresses. Without these, you would have to type binary addresses into your browser for every website you visited.

### Verification Checklist

- [ ] **DNS (Domain Name System):**
    - **Action:** Configure the Server in Subnet 4. Under **Services > DNS**, add a record: `Server1` = `150.150.4.10` (PC2). On PC1, set the DNS Server IP to `150.150.4.2`.
    - **Verification:** On PC1’s Command Prompt, type `ping Server1`. If it resolves to the IP, DNS is functional.
- [ ] **ARP (Address Resolution Protocol):**
    - **Action:** On PC1, type `arp -a`.
    - **Why:** Even with an IP, PC1 cannot build an Ethernet frame without R1's MAC address. ARP discovers the MAC address of the default gateway (R1) so the first frame can be built.
- [ ] **Ping (ICMP):**
    - **Action:** From PC1, type `ping 150.150.4.10`.
    - **Why:** An ICMP Echo Reply confirms that Layers 1, 2, and 3 are functional across the entire path. If this fails, check your Routing Table (`show ip route`) to see if the router knows about Subnet 4.

--------------------------------------------------------------------------------

## 6. Lab Summary and Critical Takeaways

This lab illustrates the modular beauty of the TCP/IP model. By separating the destination (Layer 3) from the delivery vehicle (Layer 2), we can move data across Serial Leased Lines, Fiber E-Line services, and Copper LANs without ever changing the core IP packet.

### Critical Takeaways

1. **Routers route packets:** They use a routing table to forward traffic based on the **Destination IP address**, which remains unchanged end-to-end.
2. **Data-links move frames:** Layer 2 headers (HDLC, Ethernet) are local to a single link; they are discarded and rebuilt at every router hop after an **FCS integrity check**.
3. **Subnets group addresses:** Effective routing requires grouping local IPs into subnets. **Routers are the boundaries** between these subnets.
4. **Protocols automate learning:** DNS translates names to IPs, while ARP translates IPs to MAC addresses, allowing the network to function without manual user intervention.

Well done on completing this foundational exploration of WANs and Routing. You have successfully navigated the complexities of the network layer. You are now prepared for **Part II: Implementing Ethernet LANs**.
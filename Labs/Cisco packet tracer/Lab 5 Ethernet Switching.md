## 1. Introduction: The "Out-of-the-Box" Catalyst Experience

One of the most critical concepts for a network engineer to grasp is the innate intelligence of a Cisco Catalyst switch. From a strategic curriculum perspective, you must understand that these devices are engineered for "plug-and-play" operation. When you take a Catalyst switch out of the box and power it on, it is immediately ready to forward frames without any manual configuration. This immediate readiness is not magic; it is the result of a sophisticated set of default behaviors and underlying logic. A beginner must understand this logic—how a switch independently builds its own map of the network—to successfully troubleshoot and scale complex environments.

### Lab Prerequisites and Hardware List

To simulate the default "out-of-the-box" experience, ensure your laboratory environment meets the following specifications based on factory default settings:

- **Hardware:** One [[Cisco Catalyst 2960 Switch]] and four Host PCs.
- **Cabling:** Standard Unshielded Twisted-Pair (UTP) cables.
- **Default Configuration Parameters:**
    - **Interface Status:** All interfaces are administratively enabled by default.
    - **VLAN Assignment:** All ports are members of **VLAN 1**.
    - **Speed/Duplex:** All 10/100 and 10/100/1000 ports have **autonegotiation** active.
    - **Operational Logic:** MAC learning, frame switching, and Spanning Tree Protocol (STP) are enabled globally.

With the physical components identified, we begin by verifying that the physical layer is stable and the switch recognizes its connected neighbors.

--------------------------------------------------------------------------------

## 2. Phase 1: Building the Topology and Verifying Interface Status

Verifying interface status is the first critical step in network troubleshooting. Before a switch can engage its higher-level logic, the physical layer must be established. The `show interfaces status` command provides a high-level health check, allowing you to confirm that the electrical signal is present and the port is ready for the data-link layer mission.

### Step 1: Build the Single Switch Topology

Recreate the topology found in Figure 5-9 of the curriculum:

1. Place a **2960 Catalyst Switch** in the workspace.
2. Deploy four PCs: **Fred**, **Barney**, **Wilma**, and **Betty**.
3. Connect the PCs to the following switch ports using UTP cabling:
    - **Fred:** Fa0/1
    - **Barney:** Fa0/2
    - **Wilma:** Fa0/3
    - **Betty:** Fa0/4

### Step 2: Analyze Interface Output

Access the switch CLI and execute `show interfaces status`. This command summarizes the state of every port on the device.

| Port               | Status       | Description                                                                                                                                                      |
| ------------------ | ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Fa0/1 – Fa0/4**  | `connected`  | These ports have a cable installed and a functional link with the PCs.                                                                                           |
| **Fa0/5 – Fa0/24** | `notconnect` | Per Source Context (p. 124), this indicates the port is not yet functioning. This usually means no cable is currently installed, rather than a hardware failure. |

**The "So What?" Layer:** A "connected" status is the absolute prerequisite for the switch to begin its primary mission: frame forwarding. Once the physical link is up, the switch can begin listening to traffic and building its "brain," formally known as the MAC address table.

--------------------------------------------------------------------------------

## 3. Phase 2: MAC Learning and Known Unicast Forwarding

The MAC address table (or Content-Addressable Memory / CAM table) is the brain of the switch. It enables intelligent forwarding, allowing the switch to perform the **forward-versus-filter** decision. Instead of acting as a simple repeater that sends data everywhere, the switch intelligently directs frames only to the port where the destination resides.

### Step 1: The Empty Table

Initially, the switch has no knowledge of the connected devices. To confirm this, run the command: `show mac address-table dynamic` The table will be empty because no host has yet transmitted a frame for the switch to inspect.

### Step 2: The Learning Process

When **Fred** (MAC `0200.1111.1111`) sends a frame to **Barney**, the switch examines the **Source MAC** field in the Ethernet header.

**Switch Logic Synthesis:** _"The source is MAC 0200.1111.1111, the frame entered interface Fa0/1; therefore, 0200.1111.1111 is reachable via Fa0/1."_

### Step 3: Verify the Updated Table

Once traffic has been exchanged, the table will populate dynamically as shown in Example 5-1.

| Vlan | Mac Address    | Type    | Ports |
| ---- | -------------- | ------- | ----- |
| 1    | 0200.1111.1111 | DYNAMIC | Fa0/1 |
| 1    | 0200.2222.2222 | DYNAMIC | Fa0/2 |

**The "So What?" Layer:** This dynamic learning process removes the administrative burden of manual configuration. Furthermore, it enables **Filtering**. Filtering is the critical decision to _not_ forward a frame out a specific port—most notably, the switch will never forward a frame back out the same port it arrived on. This ensures efficiency and prevents basic traffic loops.

--------------------------------------------------------------------------------

## 4. Phase 3: Analyzing Unknown Unicast and Broadcast Flooding

If a switch does not have a destination MAC address in its table, it must still ensure delivery. This is achieved through **flooding**. While less efficient than known unicast forwarding, flooding is a strategic necessity that ensures connectivity when location data is missing.

### Simulation: The Unknown Unicast

To observe this, you must ensure the switch's table is empty (either by using a new switch or clearing the table as shown in Phase 5). When **Fred** sends a frame to **Barney** and Barney's MAC is not yet learned:

1. The switch receives the frame on **Fa0/1**.
2. The switch fails to find a match for the destination MAC in its table.
3. **Flooding Logic:** The switch **replicates the frame out all ports in the VLAN** except the port on which the frame was received. Barney (Fa0/2), Wilma (Fa0/3), and Betty (Fa0/4) all receive a copy.

### Logic Flow for Broadcast Frames

Broadcast frames (destination **FFFF.FFFF.FFFF**) follow a similar logic to ensure all devices in the LAN receive the message:

- **Identification:** The switch identifies the destination as the universal broadcast address.
- **Replication:** The switch replicates the frame.
- **Delivery:** It floods the frame out every port in the VLAN except the incoming interface.

**The "So What?" Layer:** Flooding is vital for delivery, but it increases network overhead. In a network with redundant physical paths, unchecked flooding leads to broadcast storms that can collapse a network. This necessitates a loop-prevention mechanism.

--------------------------------------------------------------------------------

## 5. Phase 4: Multi-Switch Topologies and Loop Prevention (STP)

In enterprise networks, switches are interconnected to provide redundancy. This creates a trade-off: multiple links provide reliability but introduce the risk of "endless loops" where frames rotate indefinitely. To prevent this, Cisco switches use the **Spanning Tree Protocol (STP)**.

### Step 1: Expand to a Two-Switch Topology

Expand your lab to match Figure 5-10:

1. Add a second switch (**SW2**).
2. Connect **SW1 (G0/1)** to **SW2 (G0/2)**.
3. Move **Wilma** to **Fa0/3** on **SW2**.
4. Move **Betty** to **Fa0/4** on **SW2**.

### Step 2: Loop Prevention via STP

STP prevents loops by placing each interface into one of two logical states:

- **Forwarding:** The interface is fully functional and sends/receives data frames.
- **Blocking:** The interface is logically disabled. It does not forward or receive data frames, effectively "breaking" the loop.

**The "So What?" Layer:** STP is enabled by default to protect the network from catastrophic failure (Figure 5-8). It is vital to remember that in this multi-switch environment, each switch makes its forwarding decisions **independently**. SW1 does not know what is in SW2's MAC table; each switch relies solely on its local table to decide where to send a frame.

--------------------------------------------------------------------------------

## 6. Phase 5: MAC Table Management and Verification

Effective "Network Housekeeping" ensures the switch's CAM table remains accurate. Because devices can be moved between ports, the switch must not keep entries indefinitely.

### Essential Verification Commands

The following commands are used for monitoring and troubleshooting. Note that `show` commands work in both user and **enable** (privileged EXEC) mode.

| Command                             | Purpose                                                          | Scenario for Use                                                    |
| ----------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------- |
| `show mac address-table count`      | Displays the number of learned addresses and remaining capacity. | Checking if the CAM table is reaching its physical memory limit.    |
| `show interfaces id counters`       | Lists the number of unicast, multicast, and broadcast frames.    | Troubleshooting broadcast storms or identifying high-traffic hosts. |
| `show mac address-table aging-time` | Shows the global and per-VLAN aging timeout.                     | Verifying how long inactive entries persist in the table.           |

### The Aging Timer and Table Clearing

By default, Cisco switches use a **300-second aging timer**. It is important to note that the switch **resets the timer to 0 only when it receives a frame with that specific source MAC address**. If a timer reaches 300 seconds without being reset, the entry is flushed.

To force the switch to re-learn the topology, an administrator can manually flush the table. **Note:** The `clear` command is an **enable mode** command only.

- **Command:** `clear mac address-table dynamic`
- **Use Case:** Use this after moving cables or changing the physical topology to ensure the switch doesn't use stale location data.

### Key Takeaways

1. **Learning:** Switches dynamically build the MAC table by inspecting the **Source MAC** of every incoming frame.
2. **Forwarding and Filtering:** Switches perform a **forward-versus-filter** decision based on the **Destination MAC**. If the destination is known, the switch forwards it to one port and filters (ignores) it on all others.
3. **Loop Prevention:** **STP** is active by default, placing redundant ports in a **blocking** state to prevent frames from looping forever.
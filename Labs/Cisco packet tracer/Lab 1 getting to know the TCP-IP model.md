# Lab Guide: Visualizing the TCP/IP Model with Cisco Packet Tracer

### 1. Introduction to the TCP/IP Blueprint

In the discipline of network engineering, the TCP/IP model is far more than a conceptual framework; it is an architectural blueprint that ensures interoperability across a global landscape of diverse hardware and software. Much like a structural blueprint allows framers, electricians, and plumbers to work toward a singular, stable residence, networking models provide the standardized protocols and physical requirements necessary for a unified communication system. Cisco Packet Tracer serves as a premier pedagogical laboratory, transforming abstract Requests For Comments (RFC) standards into a tangible environment. Within this simulation, you can move beyond reading about standards to observing the live movement of data across a functional topology.

![[1. Terminology in the learning order of this course#tangible]]

The primary mission of this lab is to replicate the communication flow between two specific host machines: **Web Server "Larry"** and **Web Browser "Bob."** As you build this environment, ***you will trace the journey of an HTTP request as it travels across the network, witnessing <u>how the model's layers collaborate to deliver data</u>.***

Having established the theoretical value of the networking model, we will now proceed to construct the physical foundation of our enterprise network.

--------------------------------------------------------------------------------

### 2. Lab Topology: Building the Physical Infrastructure

The Physical Layer (Layer 1) is the bedrock of the TCP/IP model. It defines the electrical, optical, and mechanical specifications for transmitting raw bitstreams. In a professional enterprise environment, the strategic selection of media—such as copper for Local Area Networks (LANs) and fiber-optic cabling for high-speed backbones—*determines the network's reliability and performance*. Precision at Layer 1 is non-negotiable; even the most advanced logical configurations will fail if the physical links are flawed.

#### Step-by-Step Implementation

Construct the topology using the following commands. 

> [!attention]
> **Prohibition:** You are forbidden from using the "Automatic Connection" tool. You must manually select the appropriate cabling to demonstrate mastery of Physical Layer interface types.

1. **Deploy End Devices:** Select the **Server-PT** icon and place it in the workspace (Label: **Larry**). Select the **PC-PT** icon and place it in the workspace (Label: **Bob**).

![[1. Terminology in the learning order of this course#deploy]]

![[Questions I asked while learning this course (ordered)#what does the PT mean in PC-PT ,for example, in Cisco Packet Tracer ?]]

![[Pasted image 20260303124207.png]]

2. **Deploy Intermediary Devices:** Select three **Cisco 2911 Routers** and place them in the workspace (Labels: **R1**, **R2**, and **R3**). So we need 3 routers for this one :

![[Pasted image 20260303124328.png]]

For the *labeling*, remember to click on the device and change the interface label like this :

![[Pasted image 20260303124544.png]]

3. **Prepare Fiber Interfaces:** Routers R1, R2, and R3 require **hardware modifications**, which means we need to add some modules, to support fiber-optic links.
    - Click on each router, go to the **Physical** tab, and **Power Off** the device.
    - Drag the **[[HWIC-1GE-SFP]]** module into an empty slot.
    - Drag the **[[GLC-LH-SMD]]** (SFP Transceiver) into the newly added HWIC slot.
    - **Power On** the device.


4. **Interconnect Devices:** Use the following cable selections:
    - **Copper Straight-Through:** Connect **Larry (FastEthernet0)** to **R1 (GigabitEthernet0/0)**.
    - **Copper Straight-Through:** Connect **Bob (FastEthernet0)** to **R2 (GigabitEthernet0/0)**.
    - **Fiber (Single-mode):** Connect **R1 (GigabitEthernet0/1/0)** to **R3 (GigabitEthernet0/1/1)**.
    - **Fiber (Single-mode):** Connect **R2 (GigabitEthernet0/1/0)** to **R3 (GigabitEthernet0/1/0)**.

#### Physical Layer Connectivity Map

|   |   |   |   |   |
|---|---|---|---|---|
|Local Device|Local Interface|Media Type|Remote Device|Remote Interface|
|**Server: Larry**|FastEthernet0|Copper|**Router: R1**|GigabitEthernet0/0|
|**PC: Bob**|FastEthernet0|Copper|**Router: R2**|GigabitEthernet0/0|
|**Router: R1**|Gig0/1/0 (SFP)|Fiber-Optic|**Router: R3**|GigabitEthernet0/1/1|
|**Router: R2**|Gig0/1/0 (SFP)|Fiber-Optic|**Router: R3**|GigabitEthernet0/1/0|

With the physical "roads" established, we now introduce the logical addressing and routing logic that allows pulses of light and electricity to find their specific destination.

--------------------------------------------------------------------------------

### 3. Network Layer Logic: IP Addressing and Routing Basics

The Network Layer (or Internet Layer) operates on a logic similar to a National Postal Service. For a letter to be delivered, it requires a unique address and a system of sorting facilities (routers) that understand how to forward mail toward its destination. IP uses "Dotted-Decimal Notation" (DDN) to provide unique identification for every host. Routers act as the post offices, evaluating the destination IP address of every packet and comparing it against their internal "routing tables" to choose the most efficient path.

#### Address Plan and Gateway Configuration

Implement the following IP assignments. Note that for Bob and Larry to communicate outside their local segments, they must be configured with a **Default Gateway** (the router interface on their local segment).

|           |           |            |             |                 |
| --------- | --------- | ---------- | ----------- | --------------- |
| Device    | Interface | IP Address | Subnet Mask | Default Gateway |
| **Larry** | F0        | 1.1.1.1    | 255.0.0.0   | 1.1.1.254       |
| **Bob**   | F0        | 2.2.2.2    | 255.0.0.0   | 2.2.2.254       |
| **R1**    | G0/0      | 1.1.1.254  | 255.0.0.0   | N/A             |
| **R1**    | G0/1/0    | 3.1.1.1    | 255.0.0.0   | N/A             |
| **R3**    | G0/1/1    | 3.1.1.3    | 255.0.0.0   | N/A             |
| **R3**    | G0/1/0    | 3.2.2.3    | 255.0.0.0   | N/A             |
| **R2**    | G0/1/0    | 3.2.2.2    | 255.0.0.0   | N/A             |
| **R2**    | G0/0      | 2.2.2.254  | 255.0.0.0   | N/A             |

#### The Router as an Intelligent Decision-Maker

In this environment, R1 and R2 are not merely extending cables; they are performing IP routing. When Bob (2.2.2.2) sends a request to Larry (1.1.1.1), Router R2 receives the packet, strips the local Ethernet header, and examines the destination IP. It then identifies that the 1.x.x.x network is reachable via R3. This process of forwarding data based on logical addressing is the core function of the Network Layer.

Now that the logical paths are established, we will examine the specific rules of encapsulation that govern how data is prepared for its journey.

---

### 4. The Encapsulation Journey: From HTTP Request to Bitstream

Encapsulation is the strategic process of wrapping data in protocol-specific headers and trailers as it moves down the stack. This is the "So What?" of the TCP/IP model: it is the mechanism that provides the instructions for delivery, error recovery, and media access. To visualize this process, switch to **Simulation Mode** (bottom-right corner) and use the **Edit Filters** tool to ensure only HTTP and TCP are visible.

#### Exercise 4.1: Inspecting the PDU

Trigger a request by opening **Bob’s Web Browser** and entering Larry’s IP (**1.1.1.1**). Click the "Capture/Forward" button to advance the simulation, then click the **PDU Envelope** to open the **PDU Information** window. Navigate between the **OSI Model** tab and the **Outbound PDU Details** tab to observe these five stages:

1. **Application Layer:** The process initiates with an **HTTP GET** request. Bob's browser creates a message requesting `home.htm`.
2. **Transport Layer:** The data is encapsulated into a **TCP Segment**. Here, sequence numbers are assigned. Inspect the TCP header to find the initial sequence numbers used for session synchronization.
3. **Network Layer:** The segment is encapsulated into an **IP Packet**. In the "Outbound PDU Details," verify the Source IP (2.2.2.2) and Destination IP (1.1.1.1).
4. **Data Link Layer:** The packet is encapsulated into an **Ethernet Frame**. This adds a Link Header containing MAC addresses and a **Link Trailer**. The trailer includes the **Frame Check Sequence (FCS)**, which the receiver uses for error detection to ensure the data was not corrupted during transmission.
5. **Physical Layer:** The frame is converted into a **Bitstream** and pulsed across the copper or fiber media.

Upon arrival, Larry performs "de-encapsulation"—the reverse process—stripping away headers to interpret the original HTTP request.

--------------------------------------------------------------------------------

### 5. Analyzing Layer Interactions and Protocol Mechanisms

Effective troubleshooting requires distinguishing between **Same-Layer Interaction** (communication between the same layers on different hosts) and **Adjacent-Layer Interaction** (how a lower layer provides a service to the layer directly above it on the same host).

#### Observation Scenarios

Analyze the following interactions within the PDU Information window:

- **Scenario A (Same-Layer):** Observe the TCP header during the transfer. As specified in Figure 1-7, Larry marks segments with sequence numbers **1, 2, and 3**. If Bob fails to receive sequence 2, his TCP process communicates directly with Larry’s TCP process to request a retransmission. This is the "Error Recovery" service.
- **Scenario B (Adjacent-Layer):** Observe how HTTP (Application) relies on TCP (Transport). HTTP does not have its own mechanism for guaranteeing delivery; it simply passes the data to TCP and "asks" the lower layer to handle the reliability requirements.

#### TCP/IP vs. OSI: Layer Mapping

While this lab focuses on the 5-layer TCP/IP model, you must be fluent in the 7-layer OSI model terminology frequently used in professional discourse.

|   |   |   |   |
|---|---|---|---|
|Layer #|OSI 7-Layer Model|TCP/IP 5-Layer Model|PDU Term|
|**7**|Application|Application|Data (Messages)|
|**6**|Presentation|Application|Data (Messages)|
|**5**|Session|Application|Data (Messages)|
|**4**|Transport|Transport|**Segment**|
|**3**|Network|Network (Internet)|**Packet**|
|**2**|Data Link|Data Link|**Frame**|
|**1**|Physical|Physical|Bits|

This mapping allows engineers to use "Layer 3" and "Network Layer" interchangeably when discussing IP routing and packet delivery.

--------------------------------------------------------------------------------

### 6. Summary of PDU Terminology and Lab Conclusion

Mastering the TCP/IP model represents the transition from being a network user to being a network architect. Through this lab, you have seen that communication is not a singular event but a series of precise handoffs. For a network engineer, precision in nomenclature is vital. Using the term "Packet" when you mean "Frame" can lead to significant confusion during high-stakes troubleshooting.

By building this topology, you have transformed the "architectural blueprint" into a functional enterprise simulation, proving that protocols are not just theory—they are the active instructions that power the global internet.

#### Vocabulary Checkpoint

- **Segment:** The PDU of the **Transport Layer** (TCP/UDP); defines sequence numbers for error recovery and port numbers for application multiplexing.
- **Packet:** The PDU of the **Network Layer** (IP); defines the logical source and destination addresses used for routing decisions.
- **Frame:** The PDU of the **Data Link Layer** (Ethernet); defines the physical media access rules and includes the FCS trailer for error detection.
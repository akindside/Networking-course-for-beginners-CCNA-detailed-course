### IBM’s Systems Network Architecture (SNA)

**Systems Network Architecture (SNA)** is a proprietary networking stack developed by IBM in **1974**. It was created to provide a consistent framework for connecting IBM’s mainframe computers (like the System/370) with remote terminals and peripheral devices.

Before SNA, every new piece of hardware required its own specialized communication link, creating a "spaghetti" of incompatible cables. SNA standardized this, becoming the dominant networking model for the banking and financial sectors throughout the 1980s.

---

### The Architecture: A 7-Layer Stack

While SNA has seven layers, it predates the OSI model. It was designed with a strictly **hierarchical** philosophy—meaning a central "brain" (the mainframe) controlled all the "dumb" terminals (green-screen monitors).

1. **Physical Control:** Defines the electrical and mechanical characteristics of the transmission media.
    
2. **Data Link Control:** Manages the flow of data between nodes (using the **SDLC** protocol, a precursor to modern HDLC).
    
3. **Path Control:** Handles routing and fragmenting data packets across the network.
    
4. **Transmission Control:** Synchronizes the data flow and provides session-level encryption.
    
5. **Data Flow Control:** Manages the "dialogue" (who speaks when) to prevent data collisions.
    
6. **Presentation Services:** Formats the data for specific displays or printers (similar to OSI Layer 6).
    
7. **Transaction Services:** Provides application services like database access or document exchange.
    

---

### Key Concepts & Components

To understand SNA, you have to look at how it viewed the "world" of the network:

- **Logical Units (LU):** These are the "ports" or software points that applications use to communicate.
    
    - _LU 6.2 (APPC)_ is famous for allowing program-to-program communication.
        
- **Physical Units (PU):** This refers to the hardware. A mainframe is a PU 5, a communications controller is a PU 4, and a terminal is a PU 2.
    
- **System Services Control Point (SSCP):** The "boss" software (running on the mainframe) that manages all resources in an SNA domain.
    

### Why does it matter today?

While most modern networks have moved to **TCP/IP**, SNA is not dead. It is considered a "legacy" protocol, but it remains heavily used in:

- **Mainframe environments:** Particularly in ATM networks and large-scale insurance/banking backends.
    
- **Data Integrity:** SNA was built for extreme reliability; it ensures that a transaction (like a bank withdrawal) either completes 100% or fails 100%, with no "maybe" in between.
    

---

### Comparison: SNA vs. TCP/IP

|**Feature**|**IBM SNA**|**TCP/IP (Modern Internet)**|
|---|---|---|
|**Hierarchy**|Centralized (Mainframe is King)|Decentralized (Peer-to-Peer)|
|**Control**|Master-Slave|Collaborative|
|**Complexity**|High (Hard to configure)|Medium (Highly automated)|
|**Reliability**|Exceptionally High|High (Best-effort)|

**Electromagnetic Interference (EMI)**, often called "line noise," is a disturbance caused by an external source that affects an electrical circuit via electromagnetic induction, electrostatic coupling, or conduction. In networking, it is the invisible enemy that degrades signal integrity, leading to data corruption and sluggish speeds.

---

### 1. How it Works: The "Shouting" Effect

Imagine trying to have a conversation in a quiet room versus a loud construction site.

- **Copper Cables:** Ethernet cables work by sending low-voltage electrical pulses. When a copper cable is placed near a source of EMI, the external magnetic field induces its own "ghost" current into the wire.
    
- **The Result:** The receiving device (like a switch) can no longer distinguish between the actual data bits and the "noise" created by the interference, resulting in **CRC (Cyclic Redundancy Check) errors** and dropped packets.
    

---

### 2. Common Sources of EMI

EMI is everywhere in a standard office or industrial environment:

- **Motors and Compressors:** Elevators, air conditioners, and refrigerators.
    
- **Lighting:** Fluorescent ballasts are notorious for emitting high-frequency noise.
    
- **Power Lines:** Running Ethernet cables parallel to high-voltage electrical conduits.
    
- **Wireless Devices:** Microwaves and certain Bluetooth devices can interfere with specific frequencies.
    

---

### 3. Mitigation Strategies (The Defense)

To decide which cabling program or certification to pursue, you must understand how we fight EMI:

- **Twisted Pair:** By twisting the wire pairs (UTP), the interference hits both wires equally but in opposite phases, allowing the receiver to cancel out the noise—a process called **Differential Signaling**.
    
- **Shielding (STP):** Cables like **Cat6A STP** include a foil or braided shield that acts as a Faraday cage, reflecting the EMI before it reaches the copper.
    
- **Fiber-Optics:** Because fiber uses light (photons) instead of electricity (electrons), it is **100% immune** to EMI. This is why fiber is mandatory in industrial plants or hospital MRI rooms.
    

---

### 4. EMI in Cybersecurity

From a security perspective, EMI is a vulnerability. **TEMPEST** is a standard used to protect against "side-channel" attacks where an attacker uses a high-sensitivity antenna to "read" the EMI leaking from a computer monitor or unshielded cable to reconstruct what is on the screen or being typed.

---

### Summary Table

|**Media Type**|**EMI Susceptibility**|**Primary Defense**|
|---|---|---|
|**UTP (Unshielded)**|High|Twisted Pair Geometry|
|**STP (Shielded)**|Low|Foil Shielding & Grounding|
|**Fiber-Optic**|Zero|Non-conductive Glass Media|


![[Pasted image 20260227030149.png]]

In networking, **pinouts** refer to the specific arrangement of the eight individual wires within an RJ-45 connector. Standardizing these positions is critical to ensure that the transmit ($TX$) pins on one device align with the receive ($RX$) pins on the other.

---

## 1. T568A vs. T568B Standards

The Telecommunications Industry Association (TIA) defines two main wiring schemes. Both use the same four pairs, but the **Green** and **Orange** pairs are swapped.

- **T568B (The Industry Standard):** Most commonly used in commercial and residential installations in the US.
    
- **T568A:** Historically used in residential phone systems and government contracts; it is the "default" in Canada.
    

---

## 2. Cable Types and Logic

Depending on how you combine these standards at each end of the cable, you create different functional types:

### Straight-Through Cable

- **Wiring:** Both ends use the **same standard** (usually B to B).
    
- **Use Case:** Connecting different types of devices (e.g., a **PC to a Switch** or a **Switch to a Router**).
    
- **Logic:** Pin 1 on end A goes directly to Pin 1 on end B.
    

### Crossover Cable

- **Wiring:** One end is **T568A** and the other is **T568B**.
    
- **Use Case:** Connecting two of the same type of device (e.g., **Switch to Switch** or **PC to PC**).
    
- **Logic:** It manually crosses the transmit pair ($1,2$) to the receive pair ($3,6$) so the devices can "hear" each other.
    

---

## 3. Pin Assignments by Speed

Modern Ethernet uses all eight wires, but older standards did not:

- **10/100 Mbps (Fast Ethernet):** Only uses **4 wires** (Pins 1, 2, 3, and 6). The other four are unused.
    
- **1 Gbps (Gigabit Ethernet):** Uses all **8 wires** simultaneously for bidirectional data.
    
- **PoE (Power over Ethernet):** Delivers DC power over the same pins used for data (Mode A) or the "spare" pins (Mode B).
    

---

## 4. Advanced: Rollover (Console) Cable

Used for management, not data transfer. The pinout is completely reversed: Pin 1 on end A connects to Pin 8 on end B. This is used to connect a PC's serial port to the **Console Port** of a Cisco switch or router for configuration.
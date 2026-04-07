![[Pasted image 20260227025055.png]]

Unshielded Twisted-Pair (UTP) is the most prevalent cabling medium in modern networking, consisting of color-coded copper wires twisted into pairs to form a communication circuit. Unlike its shielded counterpart (STP), UTP lacks internal metallic foil or braiding, relying instead on the physics of the "twist" and differential signaling to protect data integrity against electromagnetic interference (EMI) and crosstalk.

---

### Key Technical Aspects

- **The Twist:** The primary defense mechanism of UTP. By twisting the wires, any external noise affects both wires in the pair equally; the receiving hardware can then cancel out this common noise, a process known as **differential mode signaling**.
    
- **Categories (Performance):** UTP is classified by categories that dictate bandwidth capacity. **Cat5e** supports up to 1 Gbps at 100 MHz, **Cat6** adds a physical plastic separator (spline) to reduce crosstalk for 10 Gbps over short distances, and **Cat6a** is designed for 10 Gbps over the full 100-meter standard.
    
- **The 100-Meter Limit:** Due to electrical attenuation (signal weakening), UTP runs are strictly limited to 100 meters. This usually includes 90 meters of solid core horizontal cabling in walls and 10 meters of stranded patch cables at the ends.
### Advantages and Limitations

### When to Choose UTP

UTP is the standard choice for the **Access Layer** of an enterprise network, connecting end-user workstations, VoIP phones, and wireless access points to the local switch. It is perfectly suited for indoor office environments where extreme electromagnetic interference is not present and cable runs are under 100 meters.

You should compare UTP to **[[Shielded Twisted-Pair (STP)]]** for use in industrial or high-interference environments to see the differences.
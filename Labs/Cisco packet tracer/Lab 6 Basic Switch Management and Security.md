# Lab Guide: Mastering Basic Switch Management and Security in Cisco Packet Tracer

## 1. Strategic Lab Foundation: Topology and Scenario

In professional network architecture, the functions of a switch are categorized into three distinct "planes." The **Data Plane** handles the high-speed forwarding of user frames. The **Control Plane** manages the logic that dictates those forwarding behaviors, such as the Spanning Tree Protocol (STP) or interface speed/duplex negotiations. Finally, the **Management Plane** provides the administrative access required to monitor and configure the device.

This lab focuses on hardening the Management Plane. You will transition from high-touch physical interaction (Console) to secure remote management (SSH). By the end of this exercise, you will have moved the switch from its vulnerable default state to a secure posture suitable for a production environment.

### Lab Topology

Assemble the following components in Cisco Packet Tracer:

- **One Switch:** Cisco Catalyst 2960 (Hostname: **SW1**).
- **One PC:** **Admin-PC** (Connected to any FastEthernet port on SW1).
- **One Router:** **R1** (Connected to any FastEthernet port on SW1 to simulate the gateway).

### Lab Parameters

Establish the following logical configuration based on the project requirements:

|   |   |
|---|---|
|Parameter|Value|
|**VLAN 1 Management IP**|192.168.1.200|
|**Subnet Mask**|255.255.255.0|
|**Default Gateway**|192.168.1.1|
|**Console Password**|faith|
|**VTY Password**|hope|
|**Enable Secret**|love|

A clear topology and parameter set serve as the fundamental blueprint for a successful deployment, ensuring logical intent matches physical reality.

--------------------------------------------------------------------------------

## 2. Securing the CLI: Access Control and Privilege Levels

By default, Cisco switches allow console access without any password challenges. This is a critical security risk; anyone with physical access can reach privileged mode to reload the device or extract sensitive configuration data.

### Basic Password Configuration

Execute the following commands to establish baseline access control:

**Step 1: Protect Privileged Mode** The `enable secret` command is the professional standard. Unlike the older `enable password` command, which uses weak encryption, the `secret` variant uses a strong cryptographic hash to protect access to privileged EXEC mode.

```bash
SW1(config)# enable secret love
```

**Step 2: Secure the Physical Console**

```bash
SW1(config)# line console 0
SW1(config-line)# password faith
SW1(config-line)# login
```

With the CLI now protected from casual physical intrusion, we must prepare the switch for modern, remote connectivity.

--------------------------------------------------------------------------------

## 3. The Management Plane: Configuring IPv4 and the SVI

To manage a switch over the network, it must behave like an IP host. While a PC has a physical NIC, a Layer 2 switch uses a **Switch Virtual Interface (SVI)**—essentially a "virtual NIC" inside the box. This reflects the "Host Concept Inside Switch," where the management logic of the switch acts as an internal workstation connected to a VLAN.

### SVI and Gateway Configuration

Configure the SVI on VLAN 1 and define the path to external subnets:

```bash
SW1(config)# interface vlan 1
SW1(config-if)# ip address 192.168.1.200 255.255.255.0
SW1(config-if)# no shutdown
SW1(config-if)# exit
SW1(config)# ip default-gateway 192.168.1.1
```

### The "So What?" of the Default Gateway

The `ip default-gateway` command allows the switch to act as a host when communicating across subnets. If the `Admin-PC` is on a different network, the switch follows standard host logic: it sends response packets to its local router (R1) to reach the remote destination. Without this, remote management is restricted to the local subnet only.

**Troubleshooting Tip:** A common point of failure is seeing the SVI in an "administratively down" or "down/down" state. Ensure you have issued `no shutdown`. Additionally, the SVI will not reach an "up/up" status unless at least one physical port assigned to that VLAN is active.

--------------------------------------------------------------------------------

## 4. Transitioning to Secure Remote Access: SSH vs. Telnet

While Telnet is easy to configure, it transmits all data—including administrative passwords—in clear text. This exposes the device to "Man-in-the-Middle" attacks. **Secure Shell (SSH)** encrypts the entire session, ensuring confidentiality.

### Hardening Remote Access

SSH requires the device to have a unique identity to generate cryptographic keys. Note the use of the newer syntax `ip domain name` (with a space) rather than the legacy hyphenated version.

**Step 1: Establish Identity and Encryption** We use a 1024-bit modulus because SSH Version 2 requires a minimum of 768 bits to function securely.

```bash
SW1(config)# hostname SW1
SW1(config)# ip domain name example.com
SW1(config)# crypto key generate rsa
# Modulus bits: 1024
SW1(config)# ip ssh version 2
```

**Step 2: Local Authentication and User Privilege** Creating a local user with `privilege 15` ensures the administrator is dropped directly into privileged mode upon login, streamlining the workflow.

```bash
SW1(config)# username admin privilege 15 secret love
```

**Step 3: Restricting Access Lines** The `transport input ssh` command is a vital hardening step that explicitly disables Telnet.

```bash
SW1(config)# line vty 0 15
SW1(config-line)# login local
SW1(config-line)# transport input ssh
```

By requiring local authentication, you gain accountability in system logs, identifying exactly which user performed a configuration change.

--------------------------------------------------------------------------------

## 5. Administrative Optimization: The "Lab-Ready" Configuration

A professional configuration should be efficient for the administrator. These refinements distinguish a novice setup from an expert deployment.

### Pro-Tips for the Lab Environment

- `**logging synchronous**`: Prevents unsolicited syslog messages from interrupting your command input by waiting for a natural break in output.
- `**exec-timeout 0 0**`: Prevents the switch from timing out your session during long lab sessions. **Warning:** This is extremely dangerous in production as it leaves active sessions open to unauthorized physical access.
- `**no ip domain-lookup**`: When you mistype a command, IOS assumes you are trying to Telnet to a hostname and attempts a DNS resolution. This command prevents the CLI from "hanging" while it waits for a lookup that will never succeed.
- `**history size 20**`: Increases the history buffer, allowing you to recall the last 20 commands using the up-arrow, significantly speeding up repetitive tasks.

```bash
SW1(config)# no ip domain-lookup
SW1(config)# line console 0
SW1(config-line)# logging synchronous
SW1(config-line)# exec-timeout 0 0
SW1(config-line)# history size 20
SW1(config-line)# exit
```

--------------------------------------------------------------------------------

## 6. Validation and Verification: The Engineer’s Checklist

"Trust but Verify" is the core tenet of network engineering. Use these commands to prove your configuration is active and functional.

### Essential Verification Commands

|   |   |
|---|---|
|Command|What it Proves|
|`show running-config`|Verifies the active configuration and confirms all parameters were accepted.|
|`show interfaces vlan 1`|Confirms the "Up/Up" status, the IP assignment, and that the SVI is active.|
|`show ip ssh`|Confirms SSH is enabled and specifically verifies that **Version 2.0** is running.|
|`show ip default-gateway`|Confirms the switch has a path to respond to traffic from external subnets.|
|`show history`|Displays the current buffer of executed commands.|

### Lab Success Criteria

1. **Connectivity:** Can you `ping 192.168.1.200` from the Admin-PC?
2. **Encryption:** Can you successfully log in to the switch from the Admin-PC via SSH using the username `admin` and secret `love`?
3. **Privilege:** Does the SSH login drop you directly into the `SW1#` prompt (Privilege 15)?

If these criteria are met, your switch is successfully hardened and managed.
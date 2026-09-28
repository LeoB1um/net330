# Multi-Zone Hospital Network Configuration - Unique Command Reference

This document catalogs the unique syntax structures and parameters utilized to build the hierarchical hospital network topology in Cisco Packet Tracer.

---

## IP Subnet Allocation Scheme (Base: 10.20.0.0/16)

| Subnet Name | VLAN ID | Network ID | Subnet Mask | Default Gateway | DHCP Pool Range |
| :--- | :---: | :--- | :--- | :--- | :--- |
| **Clinic** | 10 | `10.20.0.0 /23` | `255.255.254.0` | `10.20.0.1` | `10.20.0.2` - `10.20.1.254` |
| **Visitor** | 20 | `10.20.2.0 /23` | `255.255.254.0` | `10.20.2.1` | `10.20.2.2` - `10.20.3.254` |
| **Office** | 30 | `10.20.4.0 /23` | `255.255.254.0` | `10.20.4.1` | `10.20.4.2` - `10.20.5.254` |
| **Default Subnet** | 1 | `10.20.6.0 /24` | `255.255.255.0` | `10.20.6.1` | `10.20.6.10` - `10.20.6.254`* |
| **Counseling** | 40 | `10.20.7.0 /24` | `255.255.255.0` | `10.20.7.1` | `10.20.7.2` - `10.20.7.254` |

---

## Unique Configuration Commands Reference

### Global System Configuration
```text
Switch(config)# hostname Hospital-MLS
```
* **Purpose:** Sets the unique device identifier name in the CLI prompt.

```text
Hospital-MLS(config)# ip routing
```
* **Purpose:** Globally enables Layer 3 IP routing and routing table processing on Multilayer Switches (like the Catalyst 3650).

---

### Layer 2 VLAN Management
```text
Hospital-MLS(config)# vlan 10
Hospital-MLS(config-vlan)# name Clinic
```
* **Purpose:** Creates a VLAN ID database entry and maps a descriptive alphanumeric name label to it.

```text
North-Edge(config)# interface range FastEthernet0/1 - 6
```
* **Purpose:** Selects a bulk block of interfaces simultaneously so that matching access configuration commands apply to all grouped ports instantly.

```text
North-Edge(config-if-range)# switchport mode access
```
* **Purpose:** Configures the targeted interface(s) to operate strictly as a Layer 2 access link to connect end-host workstations (PCs, Laptops, Servers).

```text
North-Edge(config-if-range)# switchport access vlan 10
```
* **Purpose:** Static port association that forces the untagged frames from connected hosts into a designated broadcast domain (VLAN 10).

---

### Layer 3 SVI & Gateway Configuration
```text
Hospital-MLS(config)# interface vlan 10
```
* **Purpose:** Creates and drops into the virtual layer 3 interface mode (Switch Virtual Interface) to act as the default gateway for that specific subnet broadcast pool.

```text
Hospital-MLS(config-if)# ip address 10.20.0.1 255.255.254.0
```
* **Purpose:** Binds a static IPv4 network address and its corresponding subnet mask to the local interface or SVI.

```text
Hospital-MLS(config-if)# ip helper-address 10.20.6.2
```
* **Purpose:** Acts as a DHCP Relay Agent. Converts incoming layer 2 client broadcast discovery traffic into a targeted layer 3 unicast request forwarded directly to the central DHCP Server (`10.20.6.2`).

```text
Hospital-MLS(config-if)# no shutdown
```
* **Purpose:** Administratively activates the logical or physical interface, forcing its hardware layer operational status into an active state.

---

### 802.1Q Inter-Switch Trunking Architecture
```text
Hospital-MLS(config)# interface range GigabitEthernet1/0/1 - 2
Hospital-MLS(config-if-range)# switchport mode trunk
```
* **Purpose:** Transforms physical links into permanent 802.1Q tagged trunk links capable of multiplexing and carrying multiple VLAN channels across core nodes simultaneously. *Note: Native to Catalyst 3650 architecture, dot1q encapsulation is implicitly configured upon applying this mode.*

---

### Diagnostics, Maintenance & Status Auditing
```text
Hospital-MLS# show vlan brief
```
* **Purpose:** Displays an inventory summary listing all active VLAN database entries, structural states, and associated access port maps.

```text
Hospital-MLS# show interfaces trunk
```
* **Purpose:** Audits operating operational parameters of inter-switch trunks, showing native VLAN identifiers and the exact VLAN list currently allowed to cross the path.

```text
Hospital-MLS# show ip interface brief
```
* **Purpose:** Generates a compressed network dashboard summary auditing physical/virtual interface addresses, layer 1 line status, and layer 2 software protocol health.

```text
Hospital-MLS# write memory
```
* **Purpose:** Saves the volatile active operational state configurations out of temporary RAM directly down into persistent Non-Volatile RAM (NVRAM).

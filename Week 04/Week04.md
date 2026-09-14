# Packet Tracer Assignment — CLI Command Notes

Multi-site VLAN/routing lab (West / Central / East). Central-MLS handles all inter-VLAN routing; West-MLS and East-MLS are Layer 2 only.

## VLAN Addressing Scheme

| VLAN Name      | VLAN # | Net/Mask         | Default Gateway |
|-----------------|--------|-------------------|------------------|
| West Clinic     | 100    | 192.168.10.0/24   | 192.168.10.1     |
| West Admin      | 110    | 192.168.11.0/24   | 192.168.11.1     |
| Central Clinic  | 200    | 192.168.20.0/24   | 192.168.20.1     |
| Central Admin   | 210    | 192.168.21.0/24   | 192.168.21.1     |
| East Clinic     | 300    | 192.168.30.0/24   | 192.168.30.1     |
| East Admin      | 310    | 192.168.31.0/24   | 192.168.31.1     |

## Topology Notes

- 3 MLS switches: West-MLS, Central-MLS, East-MLS
- Central-MLS is the only routing switch (`ip routing` + all SVIs live here)
- West-MLS / East-MLS only need their own local VLANs (100/110 and 300/310 respectively)  they don't need to know about the other sites' VLANs
- Each edge (2960) switch has only one PC attached, so it only needs the single VLAN that PC belongs to, not both site VLANs
- Uplinks from edge switches to their MLS, and MLS-to-MLS links, are all trunk ports regardless of how many VLANs actually cross them

## Commands Used

### Hostname
```
hostname West-MLS
```

### Creating VLANs (VLAN database mode)
```
vlan 100
 name West-Clinic
exit
```

### Enabling Layer 3 routing (Central-MLS only)
```
ip routing
```

### Creating VLAN interfaces (SVIs) default gateways (Central-MLS only)
```
interface vlan 100
 ip address 192.168.10.1 255.255.255.0
 no shutdown
```

### Trunk encapsulation (3560/MLS switches need dotq1 set before trunk mode)
```
interface gi0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
```

### Trunk mode on edge switches (2960s have no encapsulation command, dot1q is automatic)
```
interface fastEthernet 0/1
 switchport mode trunk
```

### Access ports (edge switches: assigning a PC's port to its VLAN)
```
interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 100
```

### Configuring a range of interfaces at once
```
interface range fastEthernet 0/1 - 2
 switchport trunk encapsulation dot1q
 switchport mode trunk
```

### Saving configuration
```
end
write memory
```
(older equivalent: `copy running-config startup-config`)

### Verification / troubleshooting commands
```
show interfaces fa0/1 switchport
show running-config
show vlan brief
show ip route
```

## New Commands Learned

- `switchport trunk encapsulation dot1q` — required on 3560/MLS trunk ports before `switchport mode trunk` will take; not needed/available on 2960 edge switches, which default to dot1q automatically.
- `interface range` — lets you apply identical config to multiple interfaces at once instead of configuring them one by one.

## Troubleshooting / Issues Log

- Issue: Systems not pinging
- Cause: I forgot to set vlans on the 2960s
- Resolution: configure the vlans for the 2960s


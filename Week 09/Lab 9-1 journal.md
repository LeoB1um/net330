# Tech Journal: Lab 9, OSPF Across Burlington and Montreal (Foster 202, AS 3033)

**Goal:** Enable OSPF so every network inside the Foster 202 company can reach every other one: Burlington users (VLAN 5), Burlington data center (VLAN 6), the BTV core link (VLAN 10), and the Montreal LAN.

## Addressing

| Network | Address | Notes |
|---|---|---|
| BTV-MTL link | 172.16.0.0/30 | BTV .1, MTL .2 (serial) |
| BTV Core (VLAN 10) | 172.16.10.0/24 | BTV-Router .1, MLS .2 |
| BTV Users (VLAN 5) | 172.16.5.0/24 | MLS gateway .1 |
| BTV Data Center (VLAN 6) | 172.16.6.0/24 | MLS gateway .1 |
| MTL Net | 172.16.20.0/24 | MTL-Router gateway .1 |

The 192.168.2.0/30 link to Vermont ISP (AS 1010) is another AS, so it is **not** advertised in OSPF.

## Steps

1. VLANs were preconfigured; on the MLS, turn on routing and set an IP on each SVI.
2. Give every host a static IP, mask, and gateway (no DHCP server in this lab, so no `ip helper-address`).
3. Ping every directly connected interface.
4. Enable OSPF (`router ospf 1`) with a `network <address> <wildcard> area 0` line for each directly connected network.
5. Verify neighbors and routes, then ping end to end.

## Configuration Commands

**BTV MLS (3550/3560 Dist)**

The VLANs (5 `users`, 6 `dc`, 10 `core`) and their port assignments were preconfigured for this lab, so I only enabled routing, set the SVI IPs, and added OSPF.

```
enable
configure terminal
ip routing
interface vlan 5
 ip address 172.16.5.1 255.255.255.0
 no shutdown
interface vlan 6
 ip address 172.16.6.1 255.255.255.0
 no shutdown
interface vlan 10
 ip address 172.16.10.2 255.255.255.0
 no shutdown
router ospf 1
 network 172.16.10.0 0.0.0.255 area 0
 network 172.16.5.0 0.0.0.255 area 0
 network 172.16.6.0 0.0.0.255 area 0
end
write memory
```

**BTV-Router**
```
enable
configure terminal
router ospf 1
 network 172.16.0.0 0.0.0.3 area 0
 network 172.16.10.0 0.0.0.255 area 0
end
write memory
```

**MTL-Router**
```
enable
configure terminal
router ospf 1
 network 172.16.0.0 0.0.0.3 area 0
 network 172.16.20.0 0.0.0.255 area 0
end
write memory
```

**Hosts (static):** User-Laptop 172.16.5.2 gw 172.16.5.1, DataCenter-Station 172.16.6.2 gw 172.16.6.1, Montreal Laptop 172.16.20.2 gw 172.16.20.1.

## Verification Commands

```
show ip interface brief
show ip ospf neighbor
show ip route
show ip protocols
show ip ospf interface
show vlan brief
```

- `show ip ospf neighbor`: BTV shows two FULL neighbors (MTL and the MLS); MTL and the MLS show one each.
- `show ip route`: every device has `C` routes for its own networks and `O` routes for the rest.
- Pings succeed between User-Laptop, DataCenter-Station, and the Montreal Laptop.

## Troubleshooting the MLS

| Symptom | Cause | Fix |
|---|---|---|
| MLS-BTV OSPF neighbor stuck in EXSTART; MLS routing table had only connected routes | The MLS Vlan10 SVI was using 172.16.10.1, the same IP as BTV-Router Fa0/0 (duplicate IP, and the MLS also picked 172.16.10.1 as its router ID) | Changed the Vlan10 SVI to 172.16.10.2, then `clear ip ospf process` |
| After that, User-Laptop could reach everything but DataCenter-Station could only ping the MLS | No `network 172.16.6.0 0.0.0.255 area 0` statement on the MLS, so BTV and MTL had no return route to VLAN 6 | Added the missing `network` line under `router ospf 1` |

Checked along the way: Vlan6 was up/up, VLAN 6 existed, and Fa0/6 was an access port in VLAN 6, so the SVI was not the problem.

### Troubleshooting Commands Used

**Diagnosing**
```
show ip interface brief
show vlan brief
show interfaces trunk
show ip ospf neighbor
show ip ospf interface
show ip route
show ip protocols
show run | section ospf
show interfaces vlan 6
show vlan id 6
show interfaces fa0/6 switchport
```

**Fixing**
```
interface vlan 10
 ip address 172.16.10.2 255.255.255.0
router ospf 1
 network 172.16.6.0 0.0.0.255 area 0

# restarts OSPF process on device in the event that you need to resend IDs
clear ip ospf process
```

## Takeaways

- Two devices on one subnet can never share an IP. Duplicate IPs show up as OSPF neighbors stuck in EXSTART/INIT.
- The OSPF router ID is picked when the process starts (highest interface IP unless set). Changing an IP does not change it until `clear ip ospf process` (answer `yes`), or you can fix it with `router-id`.
- Every directly connected network needs its own `network` statement. A missing one means a one-way ping: packets get there, but the replies have no route back.
- If a host can ping its gateway but nothing past it, check the missing routes and the host's gateway.
- `ip helper-address` only relays DHCP, and it is not needed without a DHCP server. Routing between VLANs comes from `ip routing` plus an SVI per VLAN.
- Keep the ISP-facing link (192.168.2.0/30) out of the Area 0 `network` statements.

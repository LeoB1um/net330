# Tech Journal: Lab 8-1B, Configuring OSPF

**Goal:** Configure OSPF on four routers so the East PC can ping the West PC.

## Addressing (X = 21)

| Network | Address | Mask |
|---|---|---|
| Border to Data Center | 10.21.1.0 | /29 |
| Data Center to East | 10.21.1.20 | /30 |
| Data Center to West | 10.21.1.32 | /30 |
| East LAN | 10.21.20.0 | /24 |
| West LAN | 10.21.40.0 | /24 |

Data Center cabling: g0/0 to Border, g0/1 to East, g0/2 to West. East and West use g0/0 toward Data Center and g0/1 toward their PC.

## Steps

1. Set a hostname with my name on each router.
2. Assign an IP to each interface and run `no shutdown`.
3. Assign PC addresses: East 10.21.20.10, West 10.21.40.10, with each LAN router as the gateway.
4. Enable OSPF with `router ospf 1`.
5. Add a `network <address> <wildcard mask> area 0` statement for every directly connected network.
6. Verify with `sh ip route` and `show ip ospf neighbor`, then ping East PC to West PC.

## Configuration Commands

**Border (Router 1)**
```
enable
configure terminal
hostname Leo-Border
interface g0/0
 ip address 10.21.1.1 255.255.255.248
 no shutdown
exit
router ospf 1
 network 10.21.1.0 0.0.0.7 area 0
end
write memory
```

**Data Center (Router 3)**
```
enable
configure terminal
hostname Leo-DataCenter
interface g0/0
 ip address 10.21.1.2 255.255.255.248
 no shutdown
exit
interface g0/1
 ip address 10.21.1.22 255.255.255.252
 no shutdown
exit
interface g0/2
 ip address 10.21.1.33 255.255.255.252
 no shutdown
exit
router ospf 1
 network 10.21.1.0 0.0.0.7 area 0
 network 10.21.1.20 0.0.0.3 area 0
 network 10.21.1.32 0.0.0.3 area 0
end
write memory
```

**East (Router 2)**
```
enable
configure terminal
hostname Leo-EastRouter2
interface g0/0
 ip address 10.21.1.21 255.255.255.252
 no shutdown
exit
interface g0/1
 ip address 10.21.20.1 255.255.255.0
 no shutdown
exit
router ospf 1
 network 10.21.1.20 0.0.0.3 area 0
 network 10.21.20.0 0.0.0.255 area 0
end
write memory
```

**West (Router 4)**
```
enable
configure terminal
hostname Leo-WestRouter4
interface g0/0
 ip address 10.21.1.34 255.255.255.252
 no shutdown
exit
interface g0/1
 ip address 10.21.40.1 255.255.255.0
 no shutdown
exit
router ospf 1
 network 10.21.1.32 0.0.0.3 area 0
 network 10.21.40.0 0.0.0.255 area 0
end
write memory
```

## Verification

- `sh ip route` on Data Center, East, and West shows the East and West networks as `O` (OSPF) routes.
- `show ip ospf neighbor` on Data Center lists three neighbors.
- East PC successfully pings West PC (10.21.40.10).

## Takeaways

- Every directly connected network needs its own `network` statement.
- Wildcard mask is the inverse of the subnet mask: /29 = 0.0.0.7, /30 = 0.0.0.3, /24 = 0.0.0.255.
- All routers must use the same area (Area 0).
- The first ping may time out because of ARP. Ping again.
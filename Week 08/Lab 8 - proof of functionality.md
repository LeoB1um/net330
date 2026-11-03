# Assignment 8: OSPF Authentication Verification (Leo-DataCenter3)

Output from the Data Center router (Router 3) showing OSPF MD5 authentication in effect.

## 1. OSPF neighbors

All three neighbors (Border, East, West) are in FULL state with authentication enabled.

```
Leo-DataCenter3#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
10.21.1.1         1   FULL/BDR        00:00:39    10.21.1.1       GigabitEthernet0/0
10.21.20.1        1   FULL/DR         00:00:39    10.21.1.21      GigabitEthernet0/1
10.21.40.1        1   FULL/DR         00:00:39    10.21.1.34      GigabitEthernet0/2
```

## 2. MD5 authentication enabled (g0/0)

The output shows "Message digest authentication enabled" with youngest key id 1.

```
Leo-DataCenter3#show ip ospf interface g0/0

GigabitEthernet0/0 is up, line protocol is up
  Internet address is 10.21.1.2/29, Area 0
  Process ID 1, Router ID 10.21.1.33, Network Type BROADCAST, Cost: 1
  Transmit Delay is 1 sec, State DR, Priority 1
  Designated Router (ID) 10.21.1.33, Interface address 10.21.1.2
  Backup Designated Router (ID) 10.21.1.1, Interface address 10.21.1.1
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    Hello due in 00:00:02
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 10.21.1.1  (Backup Designated Router)
  Suppress hello for 0 neighbor(s)
  Message digest authentication enabled
    Youngest key id is 1
```

## 3. Routing table

The East (10.21.20.0/24) and West (10.21.40.0/24) networks are still learned through OSPF (`O` routes).

```
Leo-DataCenter3#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 8 subnets, 4 masks
C       10.21.1.0/29 is directly connected, GigabitEthernet0/0
L       10.21.1.2/32 is directly connected, GigabitEthernet0/0
C       10.21.1.20/30 is directly connected, GigabitEthernet0/1
L       10.21.1.22/32 is directly connected, GigabitEthernet0/1
C       10.21.1.32/30 is directly connected, GigabitEthernet0/2
L       10.21.1.33/32 is directly connected, GigabitEthernet0/2
O       10.21.20.0/24 [110/2] via 10.21.1.21, 01:26:40, GigabitEthernet0/1
O       10.21.40.0/24 [110/2] via 10.21.1.34, 01:23:40, GigabitEthernet0/2
```

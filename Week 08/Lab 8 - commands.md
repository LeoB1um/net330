# Assignment 8: OSPF Authentication Commands

**Method:** OSPF MD5 authentication, applied per interface on the router-to-router links.
**Key ID:** 1 | **Key:** Leo21key (identical on both ends of every link)

| Router | Name | Authenticated interfaces |
|---|---|---|
| Router 1 | Leo-Border | g0/0 (to Data Center) |
| Router 2 | Leo-EastRouter2 | g0/0 (to Data Center) |
| Router 3 | Leo-DataCenter3 | g0/0 (Border), g0/1 (East), g0/2 (West) |
| Router 4 | Leo-WestRouter4 | g0/0 (to Data Center) |

LAN-facing ports (g0/1 on East and West) are not authenticated.

## Router 1: Border
```
enable
configure terminal
interface g0/0
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 Leo21key
end
write memory
```

## Router 2: East
```
enable
configure terminal
interface g0/0
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 Leo21key
end
write memory
```

## Router 3: Data Center

Shortcut (applies the same two lines to g0/0, g0/1, and g0/2):
```
enable
configure terminal
interface range g0/0 - 2
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 Leo21key
end
write memory
```

Equivalent, one interface at a time:
```
enable
configure terminal
interface g0/0
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 Leo21key
exit
interface g0/1
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 Leo21key
exit
interface g0/2
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 Leo21key
end
write memory
```

## Router 4: West
```
enable
configure terminal
interface g0/0
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 Leo21key
end
write memory
```

## Verification

- `show ip ospf neighbor` on Data Center: three neighbors in FULL state.
- `show ip ospf interface g0/0`: shows "Message digest authentication enabled."
- `show ip route`: East and West networks still appear as `O` routes.
- East PC can ping West PC (10.21.40.10).

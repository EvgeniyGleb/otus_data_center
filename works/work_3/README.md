# Домашнее задание №3

## Underlay. IS-IS

### Задача:

- Настроить протокол IS-IS для Underlay сети
- Проверить связанность между устройствами

## Выполнение:

### Схема сети

![](ISIS.png)

### Конфигурация оборудования

- #### [leaf_1](config/leaf_1.conf)

```
hostname leaf_1
!
spanning-tree mode mstp
!
interface Ethernet1
   description to-spine_1
   no switchport
   ip address 10.2.1.1/31
   isis enable 1
!
interface Ethernet2
   description to-spine_2
   no switchport
   ip address 10.2.2.1/31
   isis enable 1
!
interface Loopback2
   ip address 10.1.0.1/32
   isis enable 1
!
ip routing
!
router isis 1
   net 49.0001.0000.0000.0001.00
   is-type level-1
   !
   address-family ipv4 unicast
!
end

```

- #### [leaf_2](config/leaf_2.conf)

```
hostname leaf_2
!
spanning-tree mode mstp
!
interface Ethernet1
   description to-spine_1
   no switchport
   ip address 10.2.1.3/31
   isis enable 1
!
interface Ethernet2
   description to-spine_2
   no switchport
   ip address 10.2.2.3/31
   isis enable 1
!
interface Loopback2
   ip address 10.1.0.2/32
   isis enable 1
!
ip routing
!
router isis 1
   net 49.0001.0000.0000.0002.00
   is-type level-1
   !
   address-family ipv4 unicast
!
end
```

- #### [leaf_3](config/leaf_3.conf)

```
hostname leaf_3
!
spanning-tree mode mstp
!
interface Ethernet1
   description to-spine_1
   no switchport
   ip address 10.2.1.5/31
   isis enable 1
!
interface Ethernet2
   description to-spine_2
   no switchport
   ip address 10.2.2.5/31
   isis enable 1
!
interface Loopback2
   ip address 10.1.0.3/32
   isis enable 1
!
ip routing
!
router isis 1
   net 49.0001.0000.0000.0003.00
   is-type level-1
   !
   address-family ipv4 unicast
!
end
```

- #### [spine_1](config/spine_1.conf)

```
hostname spine_1
!
spanning-tree mode mstp
!
interface Ethernet1
   description to-leaf_1
   no switchport
   ip address 10.2.1.0/31
   isis enable 1
!
interface Ethernet2
   description to-leaf_2
   no switchport
   ip address 10.2.1.2/31
   isis enable 1
!
interface Ethernet3
   description to-leaf_3
   no switchport
   ip address 10.2.1.4/31
   isis enable 1
!
interface Loopback1
   ip address 10.1.1.0/32
   isis enable 1
!
ip routing
!
router isis 1
   net 49.0001.0000.0000.0100.00
   is-type level-1
   !
   address-family ipv4 unicast
!
end
```

- #### [spine_2](config/spine_2.conf)

```
hostname spine_2
!
spanning-tree mode mstp
!
interface Ethernet1
   description to-leaf_1
   no switchport
   ip address 10.2.2.0/31
   isis enable 1
!
interface Ethernet2
   description to-leaf_2
   no switchport
   ip address 10.2.2.2/31
   isis enable 1
!
interface Ethernet3
   description to-leaf_3
   no switchport
   ip address 10.2.2.4/31
   ip ospf area 0.0.0.0
   isis enable 1
!
interface Loopback1
   ip address 10.1.2.0/32
   isis enable 1
!
ip routing
!
router isis 1
   net 49.0001.0000.0000.0200.00
   is-type level-1
   !
   address-family ipv4 unicast
!
end
```

---

### Проверка связанности устройств по протоколу IS-IS

- #### spine_1

```
spine_1#sh isis neighbors

Instance  VRF      System Id        Type Interface          SNPA              State Hold time   Circuit Id
1         default  leaf_1           L1   Ethernet1          50:0:0:d7:ee:b    UP    9           leaf_1.09
1         default  leaf_2           L1   Ethernet2          50:0:0:cb:38:c2   UP    7           leaf_2.09
1         default  leaf_3           L1   Ethernet3          50:0:0:d5:5d:c0   UP    8           leaf_3.0a


spine_1#sh ip route

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

Gateway of last resort is not set

 I L1     10.1.0.1/32 [115/20] via 10.2.1.1, Ethernet1
 I L1     10.1.0.2/32 [115/20] via 10.2.1.3, Ethernet2
 I L1     10.1.0.3/32 [115/20] via 10.2.1.5, Ethernet3
 C        10.1.1.0/32 is directly connected, Loopback1
 I L1     10.1.2.0/32 [115/30] via 10.2.1.1, Ethernet1
                               via 10.2.1.3, Ethernet2
                               via 10.2.1.5, Ethernet3
 C        10.2.1.0/31 is directly connected, Ethernet1
 C        10.2.1.2/31 is directly connected, Ethernet2
 C        10.2.1.4/31 is directly connected, Ethernet3
 I L1     10.2.2.0/31 [115/20] via 10.2.1.1, Ethernet1
 I L1     10.2.2.2/31 [115/20] via 10.2.1.3, Ethernet2
 I L1     10.2.2.4/31 [115/20] via 10.2.1.5, Ethernet3
```

- #### spine_2

```
spine_2#sh isis neighbors

Instance  VRF      System Id        Type Interface          SNPA              State Hold time   Circuit Id
1         default  leaf_1           L1   Ethernet1          50:0:0:d7:ee:b    UP    8           leaf_1.0a
1         default  leaf_2           L1   Ethernet2          50:0:0:cb:38:c2   UP    7           leaf_2.0a
1         default  leaf_3           L1   Ethernet3          50:0:0:d5:5d:c0   UP    7           leaf_3.0b


spine_2#sh ip route

VRF: default
Codes: C - connected, S - static, K - kernel,
       O - OSPF, IA - OSPF inter area, E1 - OSPF external type 1,
       E2 - OSPF external type 2, N1 - OSPF NSSA external type 1,
       N2 - OSPF NSSA external type2, B - Other BGP Routes,
       B I - iBGP, B E - eBGP, R - RIP, I L1 - IS-IS level 1,
       I L2 - IS-IS level 2, O3 - OSPFv3, A B - BGP Aggregate,
       A O - OSPF Summary, NG - Nexthop Group Static Route,
       V - VXLAN Control Service, M - Martian,
       DH - DHCP client installed default route,
       DP - Dynamic Policy Route, L - VRF Leaked,
       G  - gRIBI, RC - Route Cache Route

Gateway of last resort is not set

 I L1     10.1.0.1/32 [115/20] via 10.2.2.1, Ethernet1
 I L1     10.1.0.2/32 [115/20] via 10.2.2.3, Ethernet2
 I L1     10.1.0.3/32 [115/20] via 10.2.2.5, Ethernet3
 I L1     10.1.1.0/32 [115/30] via 10.2.2.1, Ethernet1
                               via 10.2.2.3, Ethernet2
                               via 10.2.2.5, Ethernet3
 C        10.1.2.0/32 is directly connected, Loopback1
 I L1     10.2.1.0/31 [115/20] via 10.2.2.1, Ethernet1
 I L1     10.2.1.2/31 [115/20] via 10.2.2.3, Ethernet2
 I L1     10.2.1.4/31 [115/20] via 10.2.2.5, Ethernet3
 C        10.2.2.0/31 is directly connected, Ethernet1
 C        10.2.2.2/31 is directly connected, Ethernet2
 C        10.2.2.4/31 is directly connected, Ethernet3
```


- #### leaf_1

```
leaf_1#ping 10.1.0.3
PING 10.1.0.3 (10.1.0.3) 72(100) bytes of data.
80 bytes from 10.1.0.3: icmp_seq=1 ttl=63 time=10.0 ms
80 bytes from 10.1.0.3: icmp_seq=2 ttl=63 time=9.29 ms
80 bytes from 10.1.0.3: icmp_seq=3 ttl=63 time=9.02 ms
80 bytes from 10.1.0.3: icmp_seq=4 ttl=63 time=9.18 ms
80 bytes from 10.1.0.3: icmp_seq=5 ttl=63 time=9.60 ms

--- 10.1.0.3 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 42ms
rtt min/avg/max/mdev = 9.028/9.438/10.089/0.395 ms, ipg/ewma 10.604/9.760 ms
leaf_1#ping 10.1.0.2
PING 10.1.0.2 (10.1.0.2) 72(100) bytes of data.
80 bytes from 10.1.0.2: icmp_seq=1 ttl=63 time=8.69 ms
80 bytes from 10.1.0.2: icmp_seq=2 ttl=63 time=8.79 ms
80 bytes from 10.1.0.2: icmp_seq=3 ttl=63 time=10.9 ms
80 bytes from 10.1.0.2: icmp_seq=4 ttl=63 time=11.2 ms
80 bytes from 10.1.0.2: icmp_seq=5 ttl=63 time=11.5 ms

--- 10.1.0.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 43ms
rtt min/avg/max/mdev = 8.698/10.256/11.503/1.244 ms, pipe 2, ipg/ewma 10.878/9.560 ms
```
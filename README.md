# pppoe поверх vxlan

### Топология
![Текущая топология](topology.png "Текущая топология")

### Конфигурация 
Листинг конфигурации pppoe клиента и сервера находятся в конфигах  

Настройка leaf1 для статического туннеля vxlan
```
interface Ethernet1
   switchport access vlan 10
!
interface Ethernet3
   no switchport
   ip address 10.0.0.0/31
   ip ospf network point-to-point
!
interface Loopback0
   ip address 1.1.1.1/32
!
ip routing
!
router ospf 1
   network 1.1.1.1/32 area 0.0.0.0
   network 10.0.0.0/32 area 0.0.0.0
! 
 interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 10 flood vtep 2.2.2.2
 ```

Настройка leaf2 для статического туннеля vxlan
```
interface Ethernet1
   switchport access vlan 10
!
interface Ethernet3
   no switchport
   ip address 10.0.0.1/31
   ip ospf network point-to-point
!
interface Loopback0
   ip address 2.2.2.2/32
!
ip routing
!
router ospf 1
   network 2.2.2.2/32 area 0.0.0.0
   network 10.0.0.1/32 area 0.0.0.0
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 10 flood vtep 1.1.1.1
```

### Проверка
На leaf1 проверяем ospf и vxlan туннель
```
Leaf1#ping 2.2.2.2 source 1.1.1.1
PING 2.2.2.2 (2.2.2.2) from 1.1.1.1 : 72(100) bytes of data.
80 bytes from 2.2.2.2: icmp_seq=1 ttl=64 time=17.3 ms
80 bytes from 2.2.2.2: icmp_seq=2 ttl=64 time=11.4 ms
80 bytes from 2.2.2.2: icmp_seq=3 ttl=64 time=5.79 ms
80 bytes from 2.2.2.2: icmp_seq=4 ttl=64 time=4.67 ms
80 bytes from 2.2.2.2: icmp_seq=5 ttl=64 time=4.21 ms

--- 2.2.2.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 57ms
rtt min/avg/max/mdev = 4.218/8.691/17.352/5.039 ms, pipe 2, ipg/ewma 14.422/12.720 ms

```

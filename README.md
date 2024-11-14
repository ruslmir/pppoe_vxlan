# pppoe поверх vxlan

### Топология
![Текущая топология](topology.png "Текущая топология")

### Конфигурация 
Листинг конфигурации pppoe клиента и сервера находятся в конфигах  

Настройка leaf1 для статического туннеля vxlan
```
interface Ethernet3
   switchport mode trunk
   no switchport
   ip address 10.0.0.0/31
   ip ospf network point-to-point

interface Loopback0
   ip address 1.1.1.1/32

router ospf 1
   network 1.1.1.1/32 area 0.0.0.0
   network 10.0.0.0/32 area 0.0.0.0
 
 
 interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 10 flood vtep 2.2.2.2
  
```

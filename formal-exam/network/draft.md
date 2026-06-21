- switch2
```bash
en 
conf ter

vlan 10
exit
 
int f0/5
swi mode access
swi access vlan 10
exit

int f0/24
swi mode trunk
exit
```
- switch1
```bash
en
conf ter

vlan 10
exit

vlan 20
exit

ip routing

int f0/5
swi mode access
swi access vlan 10
exit

int f0/8
swi mode access
swi access vlan 20
exit

int f0/24
swi trunk encaps dot1q
swi mode trunk
exit

int vlan 10
ip addr 192.168.10.1 255.255.255.0
no shut
exit
```
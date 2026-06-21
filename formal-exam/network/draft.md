en
conf ter

vlan 10
exit

vlan 20
exit

vlan 200
exit

int f0/1 - 2
swi trunk encaps dot1q
swi mode trunk
exit

int vlan 10
ip addr 192.168.2.1 255.255.255.0
no shut
exit

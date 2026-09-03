# SOHO Network - Mwas LAN | 192.168.10.0/24

## Author
Mwas - Branch: mwas - File: soho-mwas.pkt - Port: G0/1

## Overview
Part of https://github.com/codekarimi/CCNA_LABS
Multi-LAN SOHO project. My LAN = 2 departments, 70 hosts total, on assigned block 192.168.10.0/24.

## VLSM
D2 123-Dept 50 users VLAN20 192.168.10.0/26 GW 192.168.10.1 Usable .2-.62
D1 Archives 20 users VLAN10 192.168.10.64/27 GW 192.168.10.65 Usable .66-.94
Free 192.168.10.96-255 reserved

## Topology
R1 G0/1 (trunk) -> Mwas-Switch -> Archives-Switch3 (VLAN10) + 123-Switch4 (VLAN20)

## Router Config
enable
conf t
int g0/1
 no shut
int g0/1.20
 encap dot1Q 20
 ip add 192.168.10.1 255.255.255.192
int g0/1.10
 encap dot1Q 10
 ip add 192.168.10.65 255.255.255.224
ip dhcp ex 192.168.10.1 192.168.10.5
ip dhcp ex 192.168.10.65 192.168.10.69
ip dhcp pool V20
 network 192.168.10.0 255.255.255.192
 default-router 192.168.10.1
 dns-server 8.8.8.8
ip dhcp pool V10
 network 192.168.10.64 255.255.255.224
 default-router 192.168.10.65
 dns-server 8.8.8.8
enable secret cisco12345
username admin secret admin123
ip domain-name mwas.local
crypto key gen rsa mod 1024
line vty 0 4
 login local
 trans input ssh
no ip http server
end
wr

## Switch Config
Mwas-Switch:
vlan 10
vlan 20
int g0/1
 switchport mode trunk
int f0/1
 switchport mode trunk
int f0/2
 switchport mode trunk

Archives-Switch3:
vlan 10
int f0/24
 switchport mode trunk
int range f0/1-10
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security max 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky

123-Switch4:
vlan 20
int f0/24
 switchport mode trunk
int range f0/1-10
 switchport mode access
 switchport access vlan 20
 switchport port-security
 switchport port-security mac-address sticky

## Objectives
DHCP YES
IP Addressing YES VLSM
Switch Security YES sticky
Router Security YES SSH
Stretch YES sticky MAC

## Test
show ip dhcp binding
show vlan brief
ping 192.168.10.1
ping 192.168.10.65
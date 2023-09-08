>S1#show run    
Building configuration...  
Current configuration : 3827 bytes  
!  
! Last configuration change at 19:32:50 MSK Fri Sep 8 2023  
!  
version 15.2  
service timestamps debug datetime msec  
service timestamps log datetime msec  
no service password-encryption  
service compress-config  
!  
hostname S1  
!  
boot-start-marker  
boot-end-marker  
!  
!  
enable secret 5 $1$v1cc$Iuey573ulc9TjyWHd0nes/  
!  
no aaa new-model  
clock timezone MSK 3 0  
!  
!  
!           
!  
!  
!  
!  
!  
no ip domain-lookup  
ip cef  
no ipv6 cef  
!  
!  
!  
spanning-tree mode pvst  
spanning-tree extend system-id  
!  
!  
!   
!  
!  
!  
!  
!  
!  
!           
!  
!  
!  
!  
!  
interface GigabitEthernet0/0  
 switchport trunk allowed vlan 3,4,8  
 switchport trunk encapsulation dot1q  
 switchport trunk native vlan 8  
 switchport mode trunk  
 negotiation auto  
!  
interface GigabitEthernet0/1  
 switchport trunk allowed vlan 3,4,8  
 switchport trunk encapsulation dot1q  
 switchport trunk native vlan 8  
 switchport mode trunk  
 negotiation auto  
!  
interface GigabitEthernet0/2  
 switchport access vlan 3  
 switchport mode access  
 negotiation auto  
!  
interface GigabitEthernet0/3  
 switchport access vlan 7  
 switchport mode access  
 negotiation auto  
!  
interface GigabitEthernet1/0  
 switchport access vlan 7  
 switchport mode access  
 negotiation auto  
!
interface GigabitEthernet1/1  
 switchport access vlan 7  
 switchport mode access  
 negotiation auto  
!  
interface GigabitEthernet1/2  
 switchport access vlan 7  
 switchport mode access  
 negotiation auto  
!  
interface GigabitEthernet1/3  
 switchport access vlan 7  
 switchport mode access  
 negotiation auto  
!  
interface Vlan3  
 ip address 192.168.3.11 255.255.255.0  
 ipv6 address FC0::3:11/118  
!  
ip forward-protocol nd  
!  
ip http server  
ip http secure-server  
!  
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr  
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr  
!  
!  
!  
!  
!  
!  
control-plane  
!  
banner motd ^CThis is a secure system. Authorized Access Only!^C  
!  
line con 0      
 password cisco    
 logging synchronous  
 login  
line aux 0  
line vty 0 4  
 password cisco  
 logging synchronous  
 login  
!  
!  
end  

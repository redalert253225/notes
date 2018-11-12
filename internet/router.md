
* find  Gateway

$ sudo pacman -S net-tools

$ netstat -rn
```
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         192.168.1.254   0.0.0.0         UG        0 0          0 enp0s20u2
0.0.0.0         10.10.1.1       0.0.0.0         UG        0 0          0 wlp2s0
10.10.1.0       0.0.0.0         255.255.255.0   U         0 0          0 wlp2s0
192.168.1.0     0.0.0.0         255.255.255.0   U         0 0          0 enp0s20
```
* key Gateway  in Browser

* key  account password

default admin 1234

Network->interface
--

* set wan   

PPP:   
chose one you used  Edit
create new Object    
key the account , password from ISP   #account: XXXX@ip.hent.net      
use this file in wan

* set lan    

Ethernet:    
ip address  # Gateway    
subnet mask   #255.255.255.0
DNS      
DHCP    #start ip  and  size     
static dhcp      #let some ip fix in MAC

* set port    

port  Role:
chosehole of router use whic lan

Object->Service
--
set the port from outside   internet

Object->Address
--
set internal device  ip

Network->NAT
--
set which ip with which port will go to which port of which device

Security Policy->Policy Control
--
set which lan or  port can go to where lan , ip or port    
notice, if there no set to allow , NAT also cant work

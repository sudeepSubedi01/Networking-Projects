# Albion University - Campus Network Design

### Requirement

1. Albion University has **two campuses** that are **20 miles apart**.
2. Main Campus: <br>
   - Building A <br>
    Departments:
      - Admin
      - HR
      - Finance 
    - Building B <br>
    Departments:
      - Engineering and Computing
      - Art and Design
    - Building C <br>
    Departments:
      - Student Lab
      - IT Department - has internal servers
3. Smaller Campus - Faculty of Health and Sciences
   - 2nd Floor - Staff Lab
   - 1st Floor - Student Lab

4. The email server is hosted externally in the cloud.
5. Each faculty has to be in different IP network.
6. Use RIPv2 for routing.
7. Configure DHCP for all devices in the campus.

## Steps
### Enabling Router Interfaces <br>
1. Main Campus Router
``` 
en
config t
int gig0/0
no sh
```
2. Branch Campus Router
```
en
config t
int gig0/0
no sh
```

### Enabling Serial Interfaces in Router <br>
1. Main Campus Router
```
Install HWIC-2T module in the router

int se0/1/0
no sh
clock rate 64000
```

3. Branch Campus Router
```
Install HWIC-2T module in the router

int se0/1/0
no sh
clock rate 64000
```

### Assigning IP to Router Interfaces
1. Main Campus Router
```
int se0/1/0
ip address 10.10.10.5 255.255.255.252
no sh
exit

int se0/1/1
ip address 10.10.10.1 255.255.255.252
no sh
exit
```
2. Branch Campus Router
```
int se0/1/0
ip address 10.10.10.2 255.255.255.252
no sh
exit
```
3. Cloud Router
```
int se0/1/0
ip address 10.10.10.6 255.255.255.252
no sh
exit

int gig0/0
ip address 20.0.0.1 255.255.255.252
no sh
exit
```

### Configure VLANs
1. Switch - Admin
```
int range fa0/1-24
switchport mode access
switchport access vlan 10
```
2. Switch - HR
```
int range fa0/1-24
switchport mode access
switchport access vlan 20
```
3. Switch - Finance
```
int range fa0/1-24
switchport mode access
switchport access vlan 30
```
4. Switch - Business
```
int range fa0/1-24
switchport mode access
switchport access vlan 40
```
5. Switch - Engineering and Computing
```
int range fa0/1-24
switchport mode access
switchport access vlan 50
```
6. Switch - Arts and Design 
```
int range fa0/1-24
switchport mode access
switchport access vlan 60
```
7. Switch - Student Labs
```
int range fa0/1-24
switchport mode access
switchport access vlan 70
```
8. Switch - IT Depart
```
int range fa0/1-24
switchport mode access
switchport access vlan 80
```
9. Switch - 2nd Floor - Staff Lab
```
int range fa0/1-24
switchport mode access
switchport access vlan 90
```
10. Switch - 1st Lab - Student Lab
```
int range fa0/1-24
switchport mode access
switchport access vlan 100
```

### Configuring Trunk Interfaces
1. Main Campus L3 Switch
```
int gig1/0/1
switchport mode trunk
```
2. Branch Campus L3 Switch
```
int gig1/0/3
switchport mode trunk
```

### Sub-interfaces on Router
1. Main Campus Router
```
int  gig0/0.10
encapsulation dot1q 10
ip address 192.168.1.1 255.255.255.0
exit

int  gig0/0.20
encapsulation dot1q 20
ip address 192.168.2.1 255.255.255.0
exit

int  gig0/0.30
encapsulation dot1q 30
ip address 192.168.3.1 255.255.255.0
exit

int  gig0/0.40
encapsulation dot1q 40
ip address 192.168.4.1 255.255.255.0
exit

int  gig0/0.50
encapsulation dot1q 50
ip address 192.168.5.1 255.255.255.0
exit

int  gig0/0.60
encapsulation dot1q 60
ip address 192.168.6.1 255.255.255.0
exit

int  gig0/0.70
encapsulation dot1q 70
ip address 192.168.7.1 255.255.255.0
exit

int  gig0/0.80
encapsulation dot1q 80
ip address 192.168.8.1 255.255.255.0
exit
```
2. Branch Campus Router
```
int  gig0/0.90
encapsulation dot1q 90
ip address 192.168.9.1 255.255.255.0
exit

int  gig0/0.100
encapsulation dot1q 100
ip address 192.168.10.1 255.255.255.0
exit
```

### Configuring DHCP
1. Main Campus Router
```
service dhcp

ip dhcp pool Admin-Pool
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
dns-server 192.168.1.1
domain-name admin.com

ip dhcp pool HR-Pool
network 192.168.2.0 255.255.255.0
default-router 192.168.2.1
dns-server 192.168.2.1
domain-name hr.com

ip dhcp pool Finance-Pool
network 192.168.3.0 255.255.255.0
default-router 192.168.3.1
dns-server 192.168.3.1
domain-name finance.com

ip dhcp pool Business-Pool
network 192.168.4.0 255.255.255.0
default-router 192.168.4.1
dns-server 192.168.4.1
domain-name buniness.com

ip dhcp pool EnC-Pool
network 192.168.5.0 255.255.255.0
default-router 192.168.5.1
dns-server 192.168.5.1
domain-name enc.com

ip dhcp pool AnD-Pool
network 192.168.6.0 255.255.255.0
default-router 192.168.6.1
dns-server 192.168.6.1
domain-name and.com

ip dhcp pool Student-Lab-Pool
network 192.168.7.0 255.255.255.0
default-router 192.168.7.1
dns-server 192.168.7.1
domain-name and.com

ip dhcp pool IT-Depart-Pool
network 192.168.8.0 255.255.255.0
default-router 192.168.8.1
dns-server 192.168.8.1
domain-name itdepart.com
```
2. Branch Campus Router
```
ip dhcp pool Staff-Lab-Pool
network 192.168.9.0 255.255.255.0
default-router 192.168.9.1
dns-server 192.168.9.1
domain-name stafflab.com

ip dhcp pool Student-Lab-Pool
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 192.168.10.1
domain-name studentlab.com
```

### RIPv2
1. Main Campus Router
```
router rip
version 2
no auto-summary
network 10.10.10.0
network 10.10.10.4
network 192.168.1.0
network 192.168.2.0
network 192.168.3.0
network 192.168.4.0
network 192.168.5.0
network 192.168.6.0
network 192.168.7.0
network 192.168.8.0
```
2. Branch Campus Router
```
router rip
version 2
no auto-summary
network 10.10.10.0
network 192.168.9.0
network 192.168.10.0
```

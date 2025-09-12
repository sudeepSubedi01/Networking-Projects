# Trading Floor Support Center Topology Design #
## Information:
1. The company employs 600 staffs. It is moving to a new building. Plus the company is also expanding.
2. Floors and Departments:
    - 1st Floor : 
        - Sales and Marketing Department, 120 users
        - Human Resource and Logistic Department, 120 users
    - 2nd Floor:
        - Finance and Accounts Department, 120 users
        - Administration and Public Relations Department, 120 users
    - 3rd Floor:
        - ICT Department, 120 users
        - Server Room, 12 devices

## Requirements
1. Use hieratical model providing redundancy at every layer i.e. two routers and two multilayer switches are expected to be used to provide redundancy.
2. The network is also expected to connect to at least two ISPs to provide redundancy and each router to the connected to the two ISPs.
3. Each department is required to have a wireless network
4. Each department should be in a different VLAN and in different subnetwork.
6. Provided a base network of `172.16.1.0`, carry out subnetting to allocate the correct number of IP addresses to each department.
7. The company network is connected to the static, public IP addresses (Internet Protocol) 195.136.17.0/30, 195.136.17.4/30, 195.136.17.8/30 and 195.136.17.12/30 connected to the two Internet providers.
9. Devices in all the departments are required to communicate with each other with the respective multilayer switch configured for inter-VLAN routing.
10. The Multilayer switches are expected to carry out both routing and switching functionalities thus will be assigned IP addresses.
11. All devices in the network are expected to obtain an IP address dynamically from the dedicated DHCP servers located at the server room.
12. Devices in the server room are to be allocated IP address statically.
13. Use OSPF as the routing protocol to advertise routes both on the routers and multilayer switches.
14. Configure SSH in all the routers and layer three switches for remote login.
15. Configure port-security for the Finance and Accounts department to allow only one device to connect to a switchport, use sticky method to obtain mac-address and violation mode shutdown.
16. Configure PAT to use the respective outbound router interface IPv4 address. implement the necessary ACL rule.
17. Test Communication, ensure everything configured is working as expected.


## Steps
### Basic Config to all Switches
`(Hostname, Banner Message, Disable domain IP lookup, Enable Password, Line console password, VTTY Password)`
1. **F1-SALES**
    ```
    en
    config t
    hostname F1-SALES
    banner motd -m #Use login password to enter# 
    no ip domain lookup

    enable password cisco

    line console 0
    password cisco
    login
    exit

    line vty 0 4
    password cisco
    login
    exit

    service password-encryption
    ```
2. **F1-HR**: `hostname F2-HR`
3. **F2-FINANCE**: `hostname F2-FINANCE`
4. **F2-ADMIN**: `hostname F2-ADMIN`
5. **F3-ICT**: `hostname F3-ICT`
6. **F3-SERVER**: `hostname F3-SERVER`
7. **L3-Switch-1**: `hostname L3-Switch-1`
8. **L3-Switch-2**: `hostname L3-Switch-2`

### SSH in L3 Switches
1. **L3-Switch-1**
    ```
    hostname L3-Switch-1
    ip domain name cisco.net
    user admin cisco secret cisco
    crypto key generate rsa
    1048

    line vty 0 15
    login local
    transport input ssh
    ip ssh version 2 
    exit
    ```

2. **L3-Switch-2**
    ```
    hostname L3-Switch-1
    ip domain name cisco.net
    user admin cisco secret cisco
    crypto key generate rsa
    1048

    line vty 0 15
    login local
    transport input ssh
    ip ssh version 2 
    exit
    ```

### SSH in Core Routers
1. **Core-Router-1**
    ```
    hostname Core-Router-1
    ip domain name cisco.net
    user admin cisco secret cisco
    crypto key generate rsa
    1048

    line vty 0 15
    login local
    transport input ssh
    ip ssh version 2 
    exit
    ```

2. **Core-Router-2**
    ```
    hostname Core-Router-2
    ip domain name cisco.net
    user admin cisco secret cisco
    crypto key generate rsa
    1048

    line vty 0 15
    login local
    transport input ssh
    ip ssh version 2 
    exit
    ```

### VLAN and Trunk/Access Ports in L2 Switches
1. **F1-SALES**
    ```
    int range fa0/1-2
    switchport mode trunk

    int range fa0/3-24
    switchport mode access
    switchport access vlan 10
    ```
2. **F1-HR**: 
    ```
    int range fa0/1-2
    switchport mode trunk

    int range fa0/3-24
    switchport mode access
    switchport access vlan 20
    ```
3. **F2-FINANCE**:
    ```
    int range fa0/1-2
    switchport mode trunk

    int range fa0/3-24
    switchport mode access
    switchport access vlan 30
    ```
4. **F2-ADMIN**: `
    ```
    int range fa0/1-2
    switchport mode trunk

    int range fa0/3-24
    switchport mode access
    switchport access vlan 40
    ```
5. **F3-ICT**: 
    ```
    int range fa0/1-2
    switchport mode trunk

    int range fa0/3-24
    switchport mode access
    switchport access vlan 50
    ```
6. **F3-SERVER**: 
    ```
    int range fa0/1-2
    switchport mode trunk

    int range fa0/3-24
    switchport mode access
    switchport access vlan 60
    ```
7.  **Turning off Gig interfaces in all L2 Switches**
    ```
    vlan 99
    name BlackHole
    int range gig0/1-2
    switchport mode access
    switchport access vlan 99
    shutdown
    ```

### VLAN and Trunk/Access in L3 Switches
1. **L3-Switch-1**
    ```
    int range gig1/0/3-8
    switchport mode trunk
    ```
2. **L3-Switch-2**

### Switchport Security to Finance and Administration Departs
1. **F2-Finance**
    ```
    int range fa0/3-24
    switchport port-security
    switchport port-security maximum 1
    switchport port-security mac-address sticky
    switchport port-security violation shutdown 
    ```
4. Subnetting and IP addressing
5. OSPF on the routers and 13 switches.
6. Static IP address to serverRoom devices.
7. DHCP server device configuratiuons.
8. Inter-VLAN routing on the 13 switches plus ip dhcp helper addresses.
9. Wireless network configurations.
10. PAT Access Control List
11. Verifying and testing configurations.



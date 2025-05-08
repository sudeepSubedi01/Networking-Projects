# Bank Network Design

## Information: <br>
1. Radeon is a US based company that deals with banking and insurance.
2. The company is extending its services across African Continent.
3. The first branch is to be located at Kenya
4. The company has secured a 4-story building in Kenya to operate the branch
5. First Floor:<br>

   |                | First Floor |                  |
   |----------------|-------------|------------------|
   | Departments    | No. of PC   | No. of Printers  |
   | Management     | 20          | 4                |
   | Research       | 20          | 4                |
   | Human Resource | 20          | 4                |

6. Second Floor

   |                | Second Floor |                  |
   |----------------|--------------|------------------|
   | Departments    | No. of PC    | No. of Printers  |
   | Marketing      | 20           | 4                |
   | Accounting     | 20           | 4                |
   | Finance        | 20           | 4                |

7. Third Floor

   |                | Third Floor  |                  |
   |----------------|--------------|------------------|
   | Departments    | No. of PC    | No. of Printers  |
   | Logistics and Store | 20      | 4                |
   | Customer Care  | 20           | 4                |
   | Guest Area     | 40           | 2                |

8. Fourth Floor

   |                | Fourth Floor |                  |                  |
   |----------------|--------------|------------------|------------------|
   | Departments    | No. of PC    | No. of Printers  | No. of Servers   |
   | Administration | 20           | 2                | -                |
   | ICT            | 20           | 2                | -                |
   | Server Room    | 2 Admin PCs  |                  | 3 (DHCP,HTTP,Email)|


9. There should be one router on each floor. The router should be connecting switches on that floor
10. Use OSPF routing protocol to advertise routes
11. Each depart should have wireless network for users
12. Each department except the server room will be anticipated to have around 60 users both wired and wireless users.
13. Host devices in the network are required to obtain IPv4 addresses automatically.
14. Devices in all the departments are required to communicate with each other.
15. All devices in the network are expected to obtain an IP address dynamically from the dedicated DHCP servers located at the server room.
16. Create HTTP, and E-mail servers
17. Configure SSH in all the routers for remote login.
18. Basic config of devices: 
      - Hostnames
      - Line console and VTTY Passwords
      - Banner Messages
      - Disable domain IP lookup
18. Each department should be in different VLAN
19. Use base network: `192.168.10.0`
20. Configure port security:
      - Use sticky command to obtain MAC address
      - Violation mode of the shutdown

<br>
<br>
<br>

# Design Approach
1. **Mesh Topology**<br>
   A mesh topology means every node (device) is connected to multiple other nodes — not just to one central device.
   There are two types:
      - Full Mesh: Every device is connected to every other device.
      - Partial Mesh: Some devices are connected to multiple others, but not all to all. 

   Example:<br>
   The routers on each floor (F1, F2, F3, F4) are connected in a cycle (F1–F2–F4–F3–F1) and also cross-linked (F1↔F4 and F2↔F3). <br>
   This is a full mesh topology.

   🧠 Purpose: If one link fails, traffic can go through another route. For example, if F1–F2 fails, F1 can still reach F2 via F3 or F4.

2. **Redundancy** <br>
   Redundancy in networking means adding extra links or devices so that if one path or device fails, the network still works.

   In this topology:<br>
   Routers are interconnected in multiple ways — so if one link or router goes down, the rest of the network remains functional.

   Each L2 switch is connected to two L3 switches — so if one L3 switch fails, devices still reach the network through the other.

   🧠 Purpose: Ensures high availability and fault tolerance. Redundancy prevents a single point of failure from breaking the network.


3. **Hierarchical Design** <br>
   A Hierarchical Network Design organizes devices in layers — each with a clear role.

   The three main layers are: <br>


   | Layer            | Role                                                        | Devices Used               |
   |------------------|-------------------------------------------------------------|----------------------------|
   | **Core**         | Backbone of the network. Fast, efficient transport of data. | Inter-floor routers        |
   | **Distribution** | Connects core to access layer. Handles routing and VLANs.   | L3 switches (per floor)    |
   | **Access**       | Directly connects end devices (PCs, printers, APs).         | L2 switches in departments |

   Purpose:
   - Easier to manage, scale, and troubleshoot.
   - Each layer has a clear responsibility.
   - Enables redundancy and modular growth.

4. **Banner Message (MOTD - Message of the Day)**
   - A banner message is a text message displayed before a user logs into the device, typically shown right after they connect via console, SSH, or Telnet.
   - Purpose:
      - Used for legal disclaimers, warnings, or identification.
   - Example: "Unauthorized access is prohibited!"

5. **Console Password**
   - The console password protects physical access to the device using a console cable.
   - Purpose:
      - Ensures only authorized people can access the CLI via the device’s console port.
   - ``` 
      line console 0       (Enters the console port config)
      password cisco       (Sets the password)
      login                (Tells the system to require this password for login.)
      ```

6. **VTY Password (Virtual Terminal Password)**
   - The VTY password protects remote access through Telnet or SSH.
   - Purpose:
      - Secures the device from unauthorized remote logins.

   - VTY lines are like virtual terminals—used when someone connects from another networked device.
   - ```
      line vty 0 15     (Configures VTY lines (0 to 15 = up to 16 remote sessions).)
      password cisco    (Sets the remote access password)
      login             (Requires password authentication for remote access.) 
      ```

7. `no ip domain-lookup`
   - Disables DNS lookup when you mistype a command (otherwise it waits trying to resolve it).
8. `enable password cisco`
   - Sets the enable/privileged mode password to cisco.

9. `service password-encryption`
   - Encrypts all plaintext passwords in the config file (like cisco above), improving security.
10. **Why the link between F1-Switch and F1-Management made trunk?**
      - For now it only carries traffic of VLAN 10 but later if we add more VLANs to L2 switch, we dont need to configure the link
      - Network engineers often apply the same trunk config to all uplinks (from L3 to L2), even if currently only one VLAN is used — for simplicity and standardization.
      - In enterprise setups, all inter-switch links (even if one side only needs one VLAN) are often trunked as a matter of practice to avoid issues in the future.
11. **Which link is chosen between Redundant Links?**
      - F1-L3 Switch  <----trunk---->  F1-Management (VLAN 10)<br>
      F2-L3 Switch  <----trunk---->  F1-Management (VLAN 10)
      - If one link fails, traffic is routed via another route due to REDUNDANCY.
      - If both the links are working, SPANNING TREE PROTOCOL comes to play that:
         - Ensures no loops in a Layer 2 topology.
         - It will automatically block one of the redundant links, unless specifically configured otherwise.
         - So, only one link is active at a time. 
12. **Routed Port** 
      - A routed port is a physical interface on a Layer 3 switch that behaves like a router port — meaning it operates at Layer 3 (network layer) and can have an IP address directly assigned to it, allowing it to perform routing functions.
      - Not part of VLAN
      - Operates at Layer 3 (routing)	
      - Routed port acts like a router itself
13. **Ip helper address**

<br>
<br>
<br>
<br>

# Steps

### Basic Config to all Switches
`(Hostname, Line console password, VTTY Password, Banner Message, Disable domain IP lookup)`
1. **F1-Management**
   ```
   en
   config t

   hostname F1-Management
   enable password cisco
   no ip domain-lookup

   banner motd #Enter login password to access F1-Management CLI#

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

2. **F1-Research**:
   `hostname F1-Research`

   `Rest Same as 1`

3. **F1-HR**: `hostname F1-HR`

   `Rest Same as 1`

4. **F2-Marketing** :
   `hostname F2-Marketing`

5. **F2-Accounting**:
   `hostname F2-Accounting`

6. **F2-Finance**:
   `hostname F2-Finance` 

7. **F3-Logistic&Store**:
   `hostname F3-Logistic&Store` 

8. **F3-CustomerCare**:
   `hostname F3-CustomerCare` 

9. **F3-GuestArea**:
   `hostname F3-GuestArea` 

10. **F4-Administration**:
   `hostname F4-Administration` 

11. **F4-ICT**:
   `hostname F4-ICT` 

12. **F4-ServerRoom**:
   `hostname F4-ServerRoom` 

13. **F1-Switch**:
   `hostname F1-Switch` 

14. **F2-Switch**:
   `hostname F2-Switch` 

15. **F3-Switch**:
   `hostname F3-Switch` 
16. **F4-Switch**:
   `hostname F4-Switch` 

---
---
---
### Basic Config to all Routers
`(Hostname, Line console password, VTTY Password, Banner Message, Disable domain IP lookup)`
1. **F1-Router**
   ```
   en
   config t

   hostname F1-Router
   enable password cisco
   no ip domain-lookup

   banner motd #Enter login password to access F1-Router CLI#

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
2. **F2-Router** <br>
   `hostname F2-Router`
      
      `Rest same as 1`

3. **F3-Router** <br>
   `hostname F3-Router`
      
      `Rest same as 1`
4. **F4-Router** <br>
   `hostname F4-Router`
      
      `Rest same as 1`
---
---
---

### SSH on all L3 Switches
1. **F1-Switch**
   ```
   ip domain-name cisco.net
   username cisco password cisco
   crypto key generate rsa
   1024
   line vty 0 4
   login local
   transport input ssh
   exit
   ```
2. **F2-Switch** <br>
   `Same as 1`

3. **F3-Switch** <br>
   `Same as 1`

4. **F4-Switch** <br>
   `Same as 1`

---
---
---

### SSH on all Routers
1. **F1-Router**
   ```
   ip domain-name cisco.net
   username cisco password cisco
   crypto key generate rsa
   1024
   line vty 0 4
   login local
   transport input ssh
   exit
   ```

2. **F2-Router** <br>
   `Same as 1`

3. **F3-Router** <br>
   `Same as 1`

4. **F4-Router** <br>
   `Same as 1`

---
---
---

### Router Serial Interfaces (HWIC-2T)
1. **F1-Router**
   ```
   int se0/1/0
   no sh
   clock rate 64000
   exit
   int se0/1/1
   no sh
   clock rate 64000
   exit
   int range gig0/0-2
   no sh
   exit
   ```
2. **F2-Router**
   ```
   int se0/1/0
   no sh
   clock rate 64000
   exit
   int se0/1/1
   no sh
   exit
   int range gig0/0-2
   no sh
   exit
   ```
3. **F3-Router**
   ```
   int se0/1/0
   no sh
   exit
   int se0/1/1
   no sh
   clock rate 64000
   exit
   int range gig0/0-2
   no sh
   exit
   ```
3. **F4-Router**
   ```
   int se0/1/0
   no sh
   exit
   int se0/1/1
   no sh
   exit
   int range gig0/0-2
   no sh
   exit
   ```
---
---
---
### Trunk Port, VLAN + Access Port, Port Security Config in L2 Switches
1. F1-Management
   ```
   config t

   int range fa0/1-2
   switchport mode trunk
   exit

   int range fa0/3-24
   switchport mode access
   switchport access vlan 120

   switchport port-security
   switchport port-security maximum 2
   switchport port-security mac-address sticky
   switchport port-security violation shutdown
   exit
   do wr
   ```
2. F1-Research <br>
   `switchport access vlan 20`

   `Rest same as 1`

*For all 16 switches, same config with respective VLAN number*

---
---
---
### Trunk in all L3 Switches
1. F1-Switch
   ```
   config t
   int range gig1/0/3-8
   switchport mode trunk
   switchport trunk allowed vlan 10
   exit
   do wr
   ```

*Same for all other L3 switches*

---
---
--- 
### Interface IP to L3 Switches
`IPs Planned as in the topology` <BR>
*Note: Configure ports of L3 switch connected to Router as ROUTED PORT*
1. F1-Switch
   ```
   int gig1/0/1
   no switchport
   ip address 10.10.10.1 255.255.255.252
   exit

   int gig1/0/2
   no switchport
   ip address 10.10.10.9 255.255.255.252
   exit
   ```
*Same way assign IPs to the interfaces of all other L3 Switched*

---
---
---
### Interface IP to Routers
`IPs Planned as in the topology` <BR>
1. F1-Router
   ```
   int gig0/0
   ip address 10.10.10.2 255.255.255.252
   ```
   
*Same way assign IPs to the all the GE and Serial interfaces of all other routers*

---
---
---
### OSPF on routers
1. F1-Router
   ```
   router ospf 10
   network 10.10.10.0 0.0.0.3 area 0
   network 10.10.10.4 0.0.0.3 area 0
   network 10.10.10.16 0.0.0.3 area 0
   network 10.10.10.28 0.0.0.3 area 0
   network 10.10.10.32 0.0.0.3 area 0
   ```
2. F2-Router
   ```
   router ospf 10
   network 10.10.10.8 0.0.0.3 area 0
   network 10.10.10.12 0.0.0.3 area 0
   network 10.10.10.16 0.0.0.3 area 0
   network 10.10.10.20 0.0.0.3 area 0
   network 10.10.10.24 0.0.0.3 area 0
   ```
3. F3-Router
   ```
   router ospf 10
   network 10.10.10.20 0.0.0.3 area 0
   network 10.10.10.32 0.0.0.3 area 0
   network 10.10.10.36 0.0.0.3 area 0
   network 10.10.10.40 0.0.0.3 area 0
   network 10.10.10.48 0.0.0.3 area 0
   ```
4. F4-Router
   ```
   router ospf 10
   network 10.10.10.24 0.0.0.3 area 0
   network 10.10.10.28 0.0.0.3 area 0
   network 10.10.10.36 0.0.0.3 area 0
   network 10.10.10.44 0.0.0.3 area 0
   network 10.10.10.52 0.0.0.3 area 0
   ```

---
---
---
### OSPF on Switches
1. F1-Switch
   ```
   ip routing 
   router ospf 10
   network 10.10.10.0  0.0.0.3 area 0
   network 10.10.10.8  0.0.0.3 area 0
   network 192.168.10.0 0.0.0.63 area 0
   network 192.168.10.64 0.0.0.63 area 0
   network 192.168.10.128 0.0.0.63 area 0
   network 192.168.10.192 0.0.0.63 area 0
   network 192.168.11.0 0.0.0.63 area 0
   network 192.168.11.64 0.0.0.63 area 0
   ```
2. F2-Switch
   ```
   ip routing
   router ospf 10
   network 10.10.10.4  0.0.0.3 area 0
   network 10.10.10.12  0.0.0.3 area 0
   network 192.168.10.0 0.0.0.63 area 0
   network 192.168.10.64 0.0.0.63 area 0
   network 192.168.10.128 0.0.0.63 area 0
   network 192.168.10.192 0.0.0.63 area 0
   network 192.168.11.0 0.0.0.63 area 0
   network 192.168.11.64 0.0.0.63 area 0
   ```
3. F3-Switch
   ```
   ip routing
   router ospf 10
   network 10.10.10.40  0.0.0.3 area 0
   network 10.10.10.44  0.0.0.3 area 0
   network 192.168.11.128 0.0.0.63 area 0
   network 192.168.11.192 0.0.0.63 area 0
   network 192.168.12.0 0.0.0.63 area 0
   network 192.168.12.64 0.0.0.63 area 0
   network 192.168.12.128 0.0.0.63 area 0
   network 192.168.12.192 0.0.0.63 area 0
   ```
4. F4-Switch
   ```
   ip routing
   router ospf 10
   network 10.10.10.48  0.0.0.3 area 0
   network 10.10.10.52    0.0.0.3 area 0
   network 192.168.11.128 0.0.0.63 area 0
   network 192.168.11.192 0.0.0.63 area 0
   network 192.168.12.0 0.0.0.63 area 0
   network 192.168.12.64 0.0.0.63 area 0
   network 192.168.12.128 0.0.0.63 area 0
   network 192.168.12.192 0.0.0.63 area 0
   ```

 ---
 ---
 ---  
### Static IP to server room devices
1. DHCP Server <br>
   IPv4 : `192.168.12.196` <br>
   Subnet: `/26` `255.255.255.192` <br>
   Gateway: `192.168.12.193`
2. EMAIL Server <br>
   IPv4 : `192.168.12.197` <br>
   Subnet: `/26` `255.255.255.192` <br>
   Gateway: `192.168.12.193`
3. HTTPS Server <br>
   IPv4 : `192.168.12.198` <br>
   Subnet: `/26` `255.255.255.192` <br>
   Gateway: `192.168.12.193`

---
---
---
### DHCP Server Config
`DHCP Server -> Services -> DHCP -> Turn on DCHP service` <br>
1.  Management-Pool
   Start IP: `192.168.10.6` <br>
   Subnet: `/26` `255.255.255.192` <br>
   Gateway: `192.168.10.1`

2. Research-Pool
   Start IP: `192.168.10.70` <br>
   Subnet: `/26` `255.255.255.192` <br>
   Gateway: `192.168.10.65`

*Similarly create pools for each VLAN*


### SVI in L3 Switches for Inter-VLAN Routing and IP Helper Address
1. F1-Switch, F2-Switch
   ```
   vlan 10
   vlan 20
   vlan 30
   vlan 40
   vlan 50
   vlan 60 

   int vlan 10
   ip address 192.168.10.1 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 20
   ip address 192.168.10.65 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 30
   ip address 192.168.10.129 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 40
   ip address 192.168.10.193 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 50
   ip address 192.168.11.1 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 60
   ip address 192.168.11.65  255.255.255.192
   no sh
   ip helper-address 192.168.12.196
   ```
2. F3-Switch, F4-Switch
   ```
   vlan 70
   vlan 80
   vlan 90
   vlan 100
   vlan 110
   vlan 120 

   int vlan 70
   ip address 192.168.11.129 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 80
   ip address 192.168.11.193 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 90
   ip address 192.168.12.1 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 100
   ip address 192.168.12.65 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 110
   ip address 192.168.12.129 255.255.255.192
   no sh
   ip helper-address 192.168.12.196

   int vlan 120
   ip address 192.168.12.193  255.255.255.192
   no sh 
   ```


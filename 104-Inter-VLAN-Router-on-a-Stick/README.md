# Inter-VLAN Routing using Router-on-a-Stick


Router-on-a-Stick:

```
        Router (gig0/0) --------------------- Switch (has multiple VLANs)
                               Trunk
                            Sub-interfaces

```
Commands: 
```
int gig0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

int gig0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
``` 

### Why skip assigning an IP to the physical interface?
Because:
- The physical interface acts only as a carrier for multiple subinterfaces.
- The physical interface itself is not participating directly in any Layer 3 communication.
- It functions in trunk mode, allowing tagged traffic from multiple VLANs.
- The subinterfaces handle all the IP routing and VLAN identification via 802.1Q encapsulation (encapsulation dot1Q).

### If you do assign an IP to the physical interface:
- It would conflict with the subinterfaces.
- The router might treat the physical interface as a standard Layer 3 interface, not a trunk.
- Inter-VLAN routing would not work correctly, because traffic wouldn’t be correctly tagged or recognized.

### Router-on-a-Stick
- The physical interface is made a trunk (on the switch side).
- On the router side, you create subinterfaces for each VLAN.
- Each subinterface:
    - Uses encapsulation dot1Q <VLAN_ID>
    - Gets an IP address to serve as the default gateway for that VLAN.

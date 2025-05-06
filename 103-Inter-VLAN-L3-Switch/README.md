# Inter-VLAN Routing using L3 Switch
A Layer 3 switch has routing capabilities, so it can act like a router inside the switch and route packets between VLANs using SVIs (Switched Virtual Interfaces).



## CONFIGURATION USING L3 SWITCH
🎯 Goal: Devices in VLAN 10, 20, and 30 should be able to ping each other

1. Connect all three VLANs using a Multilayer-L3 Switch(3560-24PS)
2. Create VLANs
In the Layer 3 switch:
    ```
    vlan 10
    name VLAN10
    vlan 20
    name VLAN20
    vlan 30
    name VLAN30
    ```

3. Assign VLANs to Access Ports <br>
Assign each port to the correct VLAN:
    ```
    int range fa0/1-2
    switchport mode access
    switchport access vlan 10

    interface fa0/3
    switchport mode access
    switchport access vlan 20

    interface fa0/4-6
    switchport mode access
    switchport access vlan 30
    ```

4. Enable Routing on the Switch <br>
    Layer 3 switches don’t route by default — so enable it:
    ```
    ip routing
    ```
    This tells the switch to route packets between different VLANs/SVIs.

5. Create SVIs (Switched Virtual Interfaces) for each VLAN <br>
    This gives each VLAN a gateway IP address:
    ```
    interface vlan 10
    ip address 192.168.10.1 255.255.255.0
    no shutdown

    interface vlan 20
    ip address 192.168.20.1 255.255.255.0
    no shutdown

    interface vlan 30
    ip address 192.168.30.1 255.255.255.0
    no shutdown
    ```
    These act like the “default gateway” for devices in each VLAN.
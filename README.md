# Router and Switch Configuration
## Objectives
* Understand how to configure routers and switches in a network
* Learn to set up interfaces, VLANs, and routing between subnets.
* Implement basic security using passwords, banner messages, and Access Control Lists (ACLs)
* Configure remote access (Telnet/SSH) for secure management.

## Network Topology Overview
Device Used:
* 3 Routers (R0, R1, R3) - For inter-network communication.
* 2 Switches (SW0, SW1) - To connect multiple devices within a network.
* 4 PCs (PC0, PC1, PC2, PC3) - Representing end-user devices.
* Cables:
  * Straight-through cables (for PC <-> Switch & Router <-> Switch connections).

## Step 1: Setting Up the Network
Physically connect the devices as follows:
* PC0 & PC1 -> SW0
* PC2 & PC3 -> SW1
* SW0 -> R0
* SW1 -> R1
* R0 <-> R1 <-> R2 are connected using **crossover cables**.

## Step 2: Configuring Basic Settings on Routers
```
Router> enable                                                # Enter privileged mode
Router# configure termianl                                    # Enter configuration mode
Router(config)# hostname R0                                   # Change hostname to R0
R0(config)# enable secret Cisco123                            # Set an encrypted password
R0(config)# banner motd "Unauthorized access prohibited!"     # Displays warning message
R0(# write memory                                             # Save the settings permanently
```
Repeat these steps for R1 and R2 (change hostnames accordingly).

## Step 3: Configuring Swtich VLANs
VLANs logically separate devices in the network.
Create VLANs:
```
Switch> enable
Switch# configure terminal
Switch(config)# vlan 10                                      # Create VLAN 10
Switch(config-vlan)# name Sales                              # Name it "Sales"
Switch(config)# vlan 20                                      # Create VLAN 20
Switch(config-vlan)# name HR                                 # Name it "HR"
```
Assign VLANs to ports (ex. PC0 on VLAN 10, PC1 on VLAN 20):
```
Switch(config)# interface FastEthernet0/1                    # Select PC0's port
Switch(config-if)# switchport mode access                    # Set as an access port
Switch(config-if)# switchport access vlan 10                 # Assign to VLAN 10
```
Repeat this for other ports (PC1 on VLAN 20, PC2 & PC3 on VLANs as needed).

## Step 4: Configuring Router Interfaces
This enables communication between different VLANs and networks.
```
R0(config)# interface GigabitEthernet0/0                    # Select interface connected to SW1
```
Assign an IP address:
```
R0(config-if)# ip address 192.168.1.1 255.255.255.0         # Set IP for VLAN 10
R0(config-if)# no shutdown                                  # Enable the interface
```
Repeat for other router interfaces (R1, R2)

## Step 5: Configure Routing Between Networks
Static routing ensures different subnets can communicate.
```
R0(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2     # Route to VLAN 20 via R1
R1(config)# ip route 192.168.1.0 255.255.255.0 10.0.0.1     # Route to VLAN 10 via R0
```

## Step 6: Configure Remote Access (Telnet/SSH)
This allows remote device management.
1. Enable Telnet & set a password:
```
R0(config)# line vty 0 4                                    # Select virtual terminal lines
R0(config-line)# password TelnetPass                        # Set Telnet password
R0(config-line)# login                                      # Enable login
```
2. Enable SSH & set up a user:
```
R0(config)# ip domain-name example.com                      # Required for SSH
R0(config)# crypto key generate rsa                         # Generate encryption key
R0(config)# username admin secret AdminPass                 # Create an SSH user
R0(config)# line vty 0 4
R0(config-line)# transport input ssh                        # Allow only SSH
```

## Step 7: Confgiure Port Security on Switches
Prevents unauthorized devices from connecting to the switch.
```
Switch(config)# interface FastEthernet0/1                       # Select PC0's port
Switch(config-if)# switchport mode access                       # Set port as access
Switch(config-if)# switchport port-security                     # Enable port security
Switch(config-if)# switchport port-security mac-address sticky  # Remember learned MAC
Switch(config-if)# switchport port-security violation restrict  # Restrict violations
```
Repeat this for each PC port on SW0 & SW1.

## Step 8: Configure ACL to Restrict Traffic
Controls which devices can communicate.
1. Deny PC0 from accessing PC2:
```
R0(config)# access-list 100 deny ip 192.168.1.10 0.0.0.0 192.168.2.10 0.0.0.0     # Block traffic from PC0 to PC2
R0(config)# access-list 100 permit ip any any                                     # Allow other traffic
R0(config)# interface GigabitEthernet0/0                                          # Select the interface where ACL should be applied
R0(config-if)# ip access-group 100 in                                             # Apply ACL 100 to inbound traffic on this interface
```

## Summary
* Configured VLANs, routing, remote access, security & ACLs.
* Test connectivity using ```ping``` between PCs.
* Save settings using ```write memory``` on all devices.

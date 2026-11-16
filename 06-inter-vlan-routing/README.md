# Lab 06: Inter-VLAN Routing (Router on a Stick)

## Objective
Allow PCs in different VLANs to communicate, using one router interface
split into subinterfaces.

## Topology and Verification
![topology](10.png)
![topology](3.png)
![topology](4.png)
![topology](2.png)
![topology](1.png)

Same as Lab 05, plus Router3 (2911). Router3 Gi0/0 connects to Switch9 Fa0/23.
Fa0/23 is a trunk.

## Configuration
    Router(config)# interface g0/0
    Router(config-if)# no shutdown
    Router(config)# interface g0/0.10
    Router(config-subif)# encapsulation dot1q 10
    Router(config-subif)# ip address 192.168.10.254 255.255.255.0
    Router(config)# interface g0/0.20
    Router(config-subif)# encapsulation dot1q 20
    Router(config-subif)# ip address 192.168.20.254 255.255.255.0

Default gateways: 192.168.10.254 (VLAN 10) and 192.168.20.254 (VLAN 20).

## Results
| Test | Result |
|---|---|
| PC2 to PC5 (VLAN 10 to VLAN 20) | 3 of 4 replies, TTL=127 (first ping lost to ARP) |
| tracert from PC2 to PC3 | (add result) |

## Analysis
- Before the router, VLAN 10 and VLAN 20 could not communicate, because
  they are different subnets.
- TTL=127 shows that the packet crossed one router.
- Frames between PCs on different VLANs cross the trunk to the router and
  back, so the Fa0/24 trunk carries them twice.

## What I learned
- Devices in different VLANs need a Layer 3 device (a router) to communicate.
- This design is called router on a stick: one trunk link, with one
  subinterface for each VLAN.
- A PC sends traffic for another network to its default gateway: the
  destination MAC address is the gateway's, and the destination IP
  address is the final host's.
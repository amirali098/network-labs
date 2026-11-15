# Lab 03: RSTP Convergence Compared with Classic STP

## Objective
(1 or 2 sentences: what you wanted to measure and why)

## Topology
![topology](Screenshot (1265).png)
![topology](Screenshot (1266).png)
![topology](Screenshot (1268).png)
![topology](Screenshot (1269).png)
![topology](Screenshot (1270).png)
![topology](Screenshot (1271).png)

| Device | Bridge ID MAC | Connected to |
|---|---|---|
| Switch0 | 000A.4166.E450 | Switch3, Switch7, PC0 |
| Switch3 | 000A.4128.AA5B | Switch0, Switch7 |
| Switch7 | 0090.21B2.0E6A | Switch0, Switch3, PC1 |

PC0: 192.168.1.10/24, PC1: 192.168.1.20/24. Switch7 is the Root Bridge (priority 24577).

## Configuration
    Switch(config)# spanning-tree mode rapid-pvst     (RSTP)
    Switch(config)# spanning-tree mode pvst           (classic STP)

## Test method
1. Run `ping -n 100 192.168.1.20` from PC0.
2. While it runs, shut down Fa0/2 on Switch0 (its Root Port).
3. Count the lost packets. Repeat in each mode.

## Results
| Mode | Packets lost |
|---|---|
| RSTP | (exact `Lost =` number) |
| Classic STP | (exact `Lost =` number) |


## Analysis
- In this test, the failed link was directly connected to Switch0, so
  classic STP did not need to wait for Max Age. The blocked port still
  had to pass through the Listening and Learning states (15 seconds each),
  so the network was disconnected for about 30 seconds.
- In RSTP, Fa0/1 on Switch0 was already an Alternate port. When the Root
  Port failed, it became the Root Port immediately, and the
  Proposal/Agreement handshake allowed it to forward without waiting for timers.
- During a 100-packet ping, RSTP lost about 1 packet and classic STP
  lost about 4 packets.
- Max Age is only needed in an indirect failure, where the local link stays
  up but BPDUs stop arriving.




## What I learned
- RSTP usually recovers faster than classic STP after a link failure.
- Classic STP has four port states: Blocking, Listening, Learning and
  Forwarding. Max Age is a timer, not a state.
- RSTP has three port states: Discarding, Learning and Forwarding.
- Classic STP has three port roles: Root, Designated and Non-designated.
  RSTP has four: Root, Designated, Alternate and Backup.
- After a direct failure, classic STP needed about 30 seconds
  (Listening + Learning). Max Age is only needed after an indirect failure.
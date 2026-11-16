# Lab 04: PortFast and BPDU Guard

## Objective
Show how PortFast and BPDU Guard protect access ports. I connect a second
switch (Switch8) to a PortFast port with BPDU Guard enabled, and check that
the port is shut down while the other ports keep working.

## Topology
![topology](topology.png)

| Device | Bridge ID MAC | Connections |
|---|---|---|
| Switch0 | 000A.4166.E450 | Fa0/1 to Switch3, Fa0/2 to Switch7, Fa0/3 to PC0, Fa0/4 to Switch8 |
| Switch3 | 000A.4128.AA5B | Fa0/1 to Switch0, Fa0/2 to Switch7 |
| Switch7 | 0090.21B2.0E6A | Fa0/1 to Switch3, Fa0/2 to Switch0, Fa0/3 to PC1 |
| Switch8 | (new switch) | Fa0/1 to Switch0 Fa0/4 |

Spanning Tree mode: RSTP (`rapid-pvst`). Switch7 is the Root Bridge (priority 24577).
PC0 is on Switch0 Fa0/3 and PC1 is on Switch7 Fa0/3. PC1 uses 192.168.1.20/24.

## Configuration
On Switch0, for both ports Fa0/3 and Fa0/4:

    Switch0(config)# interface range fastEthernet 0/3-4
    Switch0(config-if-range)# switchport mode access
    Switch0(config-if-range)# spanning-tree portfast
    Switch0(config-if-range)# spanning-tree bpduguard enable

## Test method
1. Connect Switch8 to Switch0 Fa0/4 with a Copper Cross-Over cable.
2. Check the log messages and run `show interfaces status`.
3. Ping from PC0 (Fa0/3) to PC1 to check that the other ports still work.

## Results
| Port | Device | State |
|---|---|---|
| Fa0/1 | Switch3 | connected |
| Fa0/2 | Switch7 | connected |
| Fa0/3 | PC0 | connected |
| Fa0/4 | Switch8 | **err-disabled** |

![err-disabled](err-disabled.png)

Switch0 logged that it received a BPDU on Fa0/4 with BPDU Guard enabled and
disabled the port. The port went into the err-disabled state.

## Analysis
- Switch8 is a switch, so it sends BPDUs. Fa0/4 has BPDU Guard enabled, so
  Switch0 shut the port down as soon as it received the first BPDU.
- BPDU Guard acts only on the port that received the BPDU. PC0 does not send
  BPDUs, so Fa0/3 stayed connected and forwarding.
- I ran `shutdown` and `no shutdown` on Fa0/4 while Switch8 was still
  connected, and the port went into err-disabled again. A port can only
  recover after the cause is removed.
- PortFast skips the Listening and Learning states, so it is only safe on
  ports to end devices. On a switch-to-switch link it can create a loop
  before STP detects it.

## What I learned
- BPDU Guard has to be enabled with its own command. PortFast alone does not
  protect the port, and the first time I forgot `spanning-tree bpduguard enable`.
- (Add one more bullet in your own words.)


# Lab 01: STP Basics

## Objective
Observe Root Bridge election and port roles in a 3-switch triangle with default priorities.

## Topology
![topology](topology.png)

| Switch | Bridge ID (MAC) |
|---|---|
| Switch0 | 000A.4166.E450 |
| Switch3 | 000A.4128.AA5B |
| Switch7 | 0090.21B2.0E6A |

## Results
- Root Bridge: Switch3 (lowest MAC, all priorities equal at 32769)
- Blocking port: Fa0/2 on Switch7
- Why: equal Root Path Cost (19) on the Switch0-Switch7 link, so the lower Bridge ID (Switch0) wins Designated.

## Verification
See `outputs/` for `show spanning-tree` from each switch.

## What I learned
(short notes, e.g. read Bridge ID, not Root ID, to identify a switch)
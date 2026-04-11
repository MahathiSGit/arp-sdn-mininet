# ARP Handling in SDN Networks

## Problem Statement
This project implements ARP request and reply handling using an SDN controller (Ryu) and Mininet simulation. The controller intercepts ARP packets, maintains an ARP table, generates ARP proxy replies, enables host discovery, and validates communication between hosts using OpenFlow 1.3.

## Project Structure
arp_sdn_project/
├── arp_controller.py   # Ryu SDN controller with ARP handling logic
├── topology.py         # Mininet topology (4 hosts, 1 switch)
└── README.md           # This file

## Requirements
- Ubuntu 20.04/22.04
- Python 3.9
- Mininet
- Ryu SDN Framework
- Open vSwitch (OVS)

## Setup & Installation

### Step 1 - Create Python 3.9 virtual environment:
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install python3.9 python3.9-venv python3.9-dev -y
python3.9 -m venv ~/ryu-env
source ~/ryu-env/bin/activate

### Step 2 - Install Ryu:
pip install setuptools==67.6.0
pip install ryu eventlet==0.30.2

### Step 3 - Install arping:
sudo apt install arping -y

## Execution Steps

### Terminal 1 - Start Ryu Controller:
source ~/ryu-env/bin/activate
cd ~/arp_sdn_project
ryu-manager --verbose arp_controller.py

### Terminal 2 - Start Mininet Topology:
source ~/ryu-env/bin/activate
cd ~/arp_sdn_project
sudo python3 topology.py

## Test Scenarios

### Scenario 1 - Normal Communication (Ping)
Command: h1 ping -c 4 h2
Expected: 4 packets transmitted, 4 received, 0% packet loss

### Scenario 2 - ARP Request and Reply
Command: h1 arping -c 3 10.0.0.3
Expected: 3 packets transmitted, 3 received, 0% unanswered

### Scenario 3 - All Hosts Reachability
Command: pingall
Expected: 0% dropped (12/12 received)

### Scenario 4 - View Flow Table
Command: sudo ovs-ofctl -O OpenFlow13 dump-flows s1
Expected: Flow rules installed for all host pairs + table-miss rule

## Expected Output

### Ryu Controller Log:
Switch 1 connected
ARP: 10.0.0.1 is at 00:00:00:00:00:01 (port 1)
ARP Proxy reply: 10.0.0.2 is at 00:00:00:00:00:02

### Flow Table:
priority=1, in_port=s1-eth1, dl_dst=00:00:00:00:00:02 actions=output:s1-eth2
priority=0 actions=CONTROLLER:65535

## Proof of Execution

### Screenshot 1 - Topology startup and h1 ping h2 (0% loss)
### Screenshot 2 - ARP request/reply (0% unanswered)
### Screenshot 3 - pingall (0% dropped, 12/12 received)
### Screenshot 4 - Flow table dump showing installed rules

## Controller Logic
- packet_in handler: Intercepts all unmatched packets
- ARP Proxy: Replies to ARP requests directly without flooding
- MAC Learning: Learns MAC-to-port mappings dynamically
- Flow Installation: Installs flow rules for learned paths
- Table-miss rule: Sends unknown packets to controller

## Tools Used
- Mininet: Network emulation
- Ryu: SDN Controller
- Open vSwitch: Software switch with OpenFlow 1.3
- arping: ARP testing tool
- ovs-ofctl: Flow table inspection

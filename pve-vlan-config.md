# PVE VLAN network configuration

The switch port on the PVE host can't be set to the management VLAN profile alone. That blocks traffic to services on other VLANs. So the port is set to **Default (VLAN 1)**, which passes all tagged traffic through to the host.

To handle that tagged traffic, the Linux bridge needs to be VLAN-aware:

```
auto vmbr0
iface vmbr0 inet manual
        bridge-ports nic0
        bridge-stp off
        bridge-fd 0
        bridge-vlan-aware yes
        bridge-vids 2-4094
```

The PVE host itself gets its management IP on a VLAN subinterface rather than on the bridge directly:

```
auto vmbr0.XX
iface vmbr0.XX inet static
        address 192.168.XX.YY/24
        gateway 192.168.XX.1
```

Where `XX` is the management VLAN ID and `YY` is the desired host IP.

This means a single cable carries all VLAN traffic into the host, the bridge routes it to the right containers, and the PVE host itself is only reachable on the management VLAN.

## Cluster joins

New nodes need this same setup before joining the cluster — the join token encodes the existing node's management address, which isn't reachable until the new node has an interface on the management VLAN.

## Risks
There is unfiltered access to the pve host. 
But its an acceptable risk for my network. All external traffic comes in through the DMZ which is isolated. Explcit allow firewall rules defined what traffic from DMZ can get TO services/IP's from the DMZ VLAN. If someone evil i outside the DMZ VLAN, thats another story. 

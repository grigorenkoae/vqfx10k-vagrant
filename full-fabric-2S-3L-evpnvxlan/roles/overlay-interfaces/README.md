
# 'overlay-interfaces' role

Generate the interfaces and vlans configuration to build an EVPN-VXLAN Fabric with iBGP overlay:
 - Physical ports configuration (members of AE bundle)
 - Interfaces configuration, including single-attached, aggregated and EVPN-multihomed (ESI), trunk and access interfaces
 - Vlans configuration, including VXLAN and IRB
 - IRB interfaces configuration, including EVPN-related parameters

Template can be found in [roles/overlay-interfaces/templates/main.conf.j2 ](templates/main.conf.j2)

## Variables Needed by this role

```yaml

overlay:
    vlans: # List of Vlans
      - name: vlan100 # Name of vlan
        id: 100 # Vlan-id
        vxlan: 100 # Vxlan-id (VNI) for this vlan
        irb: # IRB (l3-interface) for this vlan
          local_ip: "192.168.1.251/24" # IRB's local IP address with prefix length
          virtual_ip: "192.168.1.254" # IRB's virtual-gateway (VGW) IP address
          virtual_mac: "00:11:11:11:11:11" # IRB's virtual-gateway-v4-mac
      - name: vlan200
        id: 200
        vxlan: 200
        irb:
          local_ip: "192.168.2.251/24"
          virtual_ip: "192.168.2.254"
          virtual_mac: "00:22:22:22:22:22"

    bundle_ports: # List of AE child interfaces
      - name: xe-0/0/2 # Name of interface
        description: "server1 eth1" # Interface description
        bundle: ae1 # Name of parent bundle interface
      - name: xe-0/0/3
        description: "server2 eth1"
        bundle: ae2

    interfaces: # List of client-facing EVPN interfaces
      - name: ae1 # Name of interface
        description: "server1 bond0" # Interface description
        aggregate: True # Aggregate (LACP) interface
        multihomed: # EVPN-Multihomed (ESI Active-Active) interface
            lacp_id: "00:00:00:11:11:11" # LACP ID - should be the same for all interfaces in one segment
            esi: "00:00:00:00:00:11:11:11:11:11" # Ethernet Segment Identifier (ESI) - should be the same for all interfaces in one segment
        native_vlan: 100 # Native-vlan-id
        type: "trunk" # Trunk interface
        vlans: # List of vlan members
          - name: vlan100 # Name of vlan member
          - name: vlan200

      - name: ae2
        description: "server2 bond0"
        aggregate: True
        multihomed:
            lacp_id: "00:00:00:22:22:22"
            esi: "00:00:00:00:00:22:22:22:22:22"
        type: "access" # Access interface
        vlans:
          - name: vlan200

```

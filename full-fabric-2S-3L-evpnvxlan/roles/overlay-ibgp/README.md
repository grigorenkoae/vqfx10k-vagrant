
# 'overlay-ibgp' role

Generate the base configuration to build an EVPN-VXLAN Fabric with iBGP overlay:
 - "Protocols evpn" configuration
 - BGP group "overlay" configuration
 - Configuration of iBGP AS number in routing options stanza - required for auto-RT
 - "switch-options" configuration

Template can be found in [roles/overlay-ibgp/templates/main.conf.j2 ](templates/main.conf.j2)

## Variables Needed by this role

```yaml

overlay:
    local:
        asn: 65000 # AS number for iBGP overlay sessions
        ip: "1.1.1.11" # IP of lo0 interface, source of iBGP sessions
        rd: "1.1.1.11:1" # RD for EVPN
        rt: "65000:1" # RT for EVPN infrastructure prefixes

    bgp_neighbors: # list of iBGP overlay neighbors
      - name: leaf2 # name of iBGP overlay neighbor
        peer_ip: "1.1.1.12" # loopback IP of iBGP overlay neighbor
      - name: leaf3
        peer_ip: "1.1.1.13"

```

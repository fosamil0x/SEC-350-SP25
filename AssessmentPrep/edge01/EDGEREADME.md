# EDGE01

## Cabling in vCenter
| INTERFACE     | NETWORK | ADDRESS         |
|---------------|---------|-----------------|
| 1 eth0        | WAN     | 10.0.17.152/24  |
| 2 eth1        | DMZ     | 172.16.50.2/29  |
| 3 eth2        | LAN     | 172.16.150.2/24 |

### Make sure you are in configure mode
## Restore internet and SSH with [this](https://github.com/fosamil0x/SEC-350-SP25/blob/main/AssessmentPrep/edge01/bare_bones_edge01.txt)
```
set interfaces ethernet eth0 address '10.0.17.152/24'
set interfaces ethernet eth2 address '172.16.150.2/24'
set nat source rule 11 description 'NAT FROM LAN to WAN'
set nat source rule 11 outbound-interface 'eth0'
set nat source rule 11 source address '172.16.150.0/24'
set nat source rule 11 translation address 'masquerade'
set protocols static route 0.0.0.0/0 next-hop 10.0.17.2
set service dns forwarding allow-from '172.16.150.0/24'
set service dns forwarding listen-address '172.16.150.2'
set service dns forwarding system
set service ssh listen-address '172.16.150.2'
set system config-management commit-revisions '100'
set system host-name 'edge01-luc'
set system name-server '10.0.17.2'
```
### Take a snapshot if you can SSH from mgmt01
### SSH from mgmt01 and [this](https://github.com/fosamil0x/SEC-350-SP25/blob/main/AssessmentPrep/edge01/edge-config.txt) into the terminal in Configure mode.

# VPN Project

## Overview
This document records the firewall configuration changes and the setup for how I got Wiregaurd working in my environment. 
Please follow these instrucitons from top to bottom, starting with firewall configs.

## Part 1 - Firewall Configurations
### Edge01 - WireGuard Traffic Configuration
1. Set a rule on WAN-to-DMZ to allow the necessary port for wireguard
```
set firewall name WAN-to-DMZ rule 518 action accept
set firewall name WAN-to-DMZ rule 518 description "wireguard from WAN to jump"
set firewall name WAN-to-DMZ rule 518 destination port 51820
set firewall name WAN-to-DMZ rule 518 protocol udp
```

### Edge01 - WireGuard NAT Configuration
2. Set NAT rules to port forward from the WAN firewall on the port for Wireguard to the jump box
```
set nat destination rule 518 description 'wireguard to jump nat'
set nat destination rule 518 destination port '51820'
set nat destination rule 518 inbound-interface 'eth0'
set nat destination rule 518 protocol 'udp'
set nat destination rule 518 translation address '172.16.50.6'
set nat destination rule 518 translation port '51820'
```

### Edge01 - RDP Configuration
3. Set a firewall rule to allow RDP connections from the jump server to the management server:
```
set firewall name DMZ-to-LAN rule 20 action 'accept'
set firewall name DMZ-to-LAN rule 20 description 'RDP from jump to mgmt02'
set firewall name DMZ-to-LAN rule 20 destination address '172.16.200.11'
set firewall name DMZ-to-LAN rule 20 destination port '3389'
set firewall name DMZ-to-LAN rule 20 protocol 'tcp'
set firewall name DMZ-to-LAN rule 20 source address '172.16.50.6'
```

### fw-mgmt RDP Configuration
4. Add a firewall rule to allow RDP connections to the management server from jump:
```
set firewall name LAN-to-MGMT rule 20 action 'accept'
set firewall name LAN-to-MGMT rule 20 description RDP from jump to mgmt02
set firewall name LAN-to-MGMT rule 20 destination address '172.16.200.11'
set firewall name LAN-to-MGMT rule 20 destination port '3389'
set firewall name LAN-to-MGMT rule 20 protocol 'tcp'
set firewall name LAN-to-MGMT rule 20 source address '172.16.50.6'
```

## Part 2 - Wireguard Server Config
### Commands
This was the process that I followed for setting up wireguard on my ubuntu 22.04 jump server. I did all of these commands as root.

1. Install wireguard packages
```
apt install wireguard wireguard-tools -y
```
2. Make a directory for wireguard and cd to it
```
mkdir /etc/wireguard
cd /etc/wireguard
```
3. Create a config file for the name of your interface. I used wg0
```
nano /etc/wireguard/wg0.conf
```

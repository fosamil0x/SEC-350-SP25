# VPN Project

## Overview
This document records the firewall configuration changes and the setup for how I got Wiregaurd working in my environment. 
Please follow these instrucitons from top to bottom, starting with firewall configs.

## Part 1 - Firewall Configurations
### Edge01 - WireGuard Traffic Configuration
1. Set a rule on WAN-to-DMZ to allow the necessary port for Wireguard
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
commit
save
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
commit
save
```

## Part 2 - Wireguard Server Config
### jump - Commands and setup
This was the process that I followed for setting up Wireguard on my ubuntu 22.04 jump server. I did all of these commands as root.

1. Install Wireguard packages
```
apt install wireguard wireguard-tools -y
```
2. Make a directory for Wireguard and cd to it
```
mkdir /etc/wireguard
cd /etc/wireguard
```
3. Create a config file for the name of your interface. I used wg0
```
nano /etc/wireguard/wg0.conf
```
4. Add [this content](https://github.com/fosamil0x/SEC-350-SP25/blob/main/Project2/wg0.conf.txt) to the wg0.conf file.
5. Enable IP Forwarding
5.1 In /etc/sysctl.conf, add or uncomment a line for net.ipv4.ip_forward=1
5.2 Apply the change
```
sysctl -p # This should return "net.ipv4.ip_forward=1"
```
6. Pause here and move over to the machine you intend to use as a Wireguard client.

## Part 3 - Wireguard Client Config
### client - Setup
1. Install Wireguard from [their install page](https://www.wireguard.com/install/). I am on a Windows 10 machine, so that is what I installed.
2. Add an emtpy interface and edit the config to be similar to the one from [this image](https://github.com/fosamil0x/SEC-350-SP25/blob/main/Project2/wgClient.png)

## Part 4 - Activating and Testing wg0
### jump - Activate the interface
1. Activate the wireguard interface
```
wg-quick up wg0 # run this as root in the /etc/wireguard directory
```
### client - Activate the interface
2. Activate the interface if it isn't already active in the Wireguard GUI. You can test this by pinging the Wireguard Server (in my case, 10.10.10.1) from the client. If pings are successful, then things are all good!
3. If pings worked, attempt to RDP to 172.16.200.11. If things fail, check firewall rules, NAT forwarding, config files, RDP enabled on mgmt02, and the status of wg0 on both the client and the server

## Part 5 - Configuring the Firewall on jump
### jump - ufw configuration
This is recommended after testing RDP.
1. Set SSH, RDP, and Wireguard rules for the firewall on jump
```
ufw allow 51820/udp
ufw allow 22/tcp
ufw allow 3389/tcp
ufw allow in on wg0 from 10.0.0.0/24 to 172.16.200.0/28
ufw allow in on wg0 from 10.10.10.0/24 to 172.16.200.11 port 3389 proto tcp
ufw allow out on ens160 to 172.16.200.0/28
ufw allow out on ens160 to 172.16.200.11 port 3389 proto tcp
```
2.1 In /etc/default/ufw, set DEFAULT_FORWARD_POLICY="ACCEPT". This is likely set to DROP by default
2.2 Reload the firewall
```
ufw reload
```
3. Reactivate wg0 on both the server and the client, then test RDP from the client to mgmt02. If everything is set correctly, this will work :)

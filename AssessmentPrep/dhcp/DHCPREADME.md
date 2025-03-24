# DHCP01
 
 ## Networking - LAN in vCenter

 ## Set up netplan
 `sudo nano /etc/netplan/00-intstaller-config.yaml`
 ### Add [this content](https://github.com/fosamil0x/SEC-350-SP25/blob/main/AssessmentPrep/dhcp/dhcpnetplan.txt) to the netplan
 ## Apply the netplan, set a hostname, and set up a sudo user
 ```
 sudo netplan apply
 sudo hostnamectl set-hostname dhcp01-luc
 sudo useradd luc -m -G sudo -s /bin/bash
 sudo passwd luc
 ```
 ### You should be able to SSH from mgmt01. Be aware that you may need to get rid of the old SSH key from practicing.
 ## isc-dhcp-server configuration
 ```
 sudo apt update
 sudo apt install isc-dhcp-server
 sudo nano /etc/dhcp/dhcpd.conf
 ```
 ### Add [this](https://github.com/fosamil0x/SEC-350-SP25/blob/main/AssessmentPrep/dhcp/dhcpd.conf.txt) to the config file
 ### Restart the service
 ```
 sudo systemctl enable --now isc-dhcp-server
 sudo systemctl status isc-dhcp-server
 ```
 ## Wazuh instructions
 In the Wazuh Dashboard, navigate to `Agent > Deploy new agent` from the main menu.
 
 Choose the OS type, architecture, the wazuh server address(172.16.200.10), the name of the agent, and the desired group(linux, ubuntu=os, x86_64).

 Run the command on dhcp01. If you have no internet access on DHCP, configure the firewall to temporarily allow it. Disable this after installing the agent.
 ### Now on dhcp01, run:
 ```
 sudo systemctl daemon-reload
 sudo systemctl enable --now wazuh-agent
 ```

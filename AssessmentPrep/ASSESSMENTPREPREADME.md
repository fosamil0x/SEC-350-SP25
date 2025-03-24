# Welcome to my Assessment Prep Github!
### I created a README with what I did for each folder. I started with edge01, followed by nginx, traveller, and dhcp.
### Good luck!


### Issues during testing:

* If an ubuntu box (nginx, dhcp) gets stuck attempting to get a network connection during the first boot up, remove the ethernet adapter and re-install it in vCenter

* Be sure to cable things on the correct network
*   dhcp on LAN
*   nginx on DMZ
*   traveler on WAN
*   edge01 [interface1 eth0 WAN] [interface2 eth1 DMZ] [interface3 eth2 LAN]

* You may need to remove ssh keys from mgmt01. Use the following command if needed
* ssh-keygen -f "/home/luc/.ssh/known_hosts" -R "IP.ADDRESS.FOR.BOX"

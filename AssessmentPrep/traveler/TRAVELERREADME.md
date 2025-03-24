# TRAVELER

# Networking information
| ADDRESS             |        SUBNET         | Default Gateway + DNS |
|---------------------|-----------------------|-----------------------|
| 10.0.17.52          | 255.255.255.0         | 10.0.17.152           |

## Create a named user account, configure networking, and set a hostname. Be sure to actually set a hostname and not just a computer description.
```powershell
net user /add luc [password]
net localgroup administrators luc /add
ncpa.cpl
sysdm.cpl
```
### Restart traveler
## Set up passwordless ssh
```powershell
ssh-keygen
type C:\Users\luc\.ssh\id_rsa.pub | ssh luc@10.0.17.152 "cat >> key"
ssh 10.0.17.152
```

### Now on JUMP as luc, switch to root and move the key to luc-jump's authorized keys

```
sudo -i
cat /home/luc/key >> /home/luc-jump/.ssh/authorized_keys
chown -R luc-jump:luc-jump /home/luc-jump/.ssh
chmod 600 /home/luc-jump/.ssh/authorized_keys
chmod 700 /home/luc-jump/.ssh
exit # exits root, now you are luc@jump_box
exit # exits luc, back to luc@traveler_box
```
### Test by running `ssh luc-jump@10.0.17.152` from traveler

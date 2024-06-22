Server setup process: Ubuntu 23.04:
```
English
Update to new Installer
Done (Keyboard)
Done (type of Install)
Done (Internet)
Done (Proxy)
Done (Mirror)
Custom storage layout (Done)
```
Setup for storage on current system:
```
Set up RAID1 for mirrored drives
rule of thumb: Use Btrfs for data that needs preservation, xfs for partitions that aren't set sizes, ext4 for its journaling
md127: all btrfs /storage
local: 512G xfs /home
local: 256G xfs /var
local: remaining xfs /

Done (Storage Configuration)
```
Setup for user configuration:
```
Your Name: Rob Castagno
Your server's name: <hostname>
Pick a username <username>
Choose a password: <pass>
Confirm your password: <pass>

Done (User config)
```
Final bit of setup: 
```
Install OpenSSH server, import github ssh keys
Done (SSH Setup)

Done (Skip snaps)
Reboot Now (remove install drive)
```

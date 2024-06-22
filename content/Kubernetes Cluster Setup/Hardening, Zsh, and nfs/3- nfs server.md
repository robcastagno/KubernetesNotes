Install nfs server:
```
sudo apt install nfs-kernel-server -y
```

Configure the nfs server:
`sudo nano /etc/exports`
```
/storage  <KUBERNETES_NODE_IP>(rw,sync,no_root_squash,no_subtree_check)
```

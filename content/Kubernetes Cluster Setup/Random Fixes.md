Migrating drive to new system, internet is not up
`ip addr` grab interface name
`sudo ip link set <interface_name> up`
need to update host name in router device list:
`sudo dhclient -r && sudo dhclient`
Also need to change the netplan in `/etc/netplan/00-installer-config.yaml`

Turn off monitor on All in One system
`sudo nano /etc/default/grub`
edit line:
`GRUB_CMDLINE_LINUX_DEFAULT="consoleblank=60"`
save, close, reload grub with `sudo update-grub`

backup etcd
set up autodeployment of a cluster
worker nodes can also function as control planes
two control planes for redundancy. 
`kubeadm token create`

Use Ansible instead of terraform :c
# Accidentally joined a node incorrectly
(Don't do this if you can avoid it lol)
Drain the Node
`kubectl drain --ignore-daemonsets <hostname>`
Remove the node from the cluster
`kubectl delete node <hostname>`
Reset the node configuration (perform on the node itself)
`sudo kubeadm reset`
Clean up Remaining Kubernetes Artifacts:
```
rm -rf ~/.kube/
rm -rf /etc/cni/net.d
rm -rf /etc/kubernetes/
rm -rf /var/lib/etcd # Might not need to perform
rm -rf /var/lib/kubelet # Might not need to perform
```
Rejoin the Node as a worker:
```
kubeadm join k8scp:6443 --token <token> /
--discovery-token-ca-cert-hash sha256:<token sha256 hash> /
--certificate-key <certificate key> | tee kubeadm-join.out
```
This can be used in less secure environments to skip the has process
`--discovery-token-unsafe-skip-ca-verification`
Create new join command with:
`kubeadm token create --print-join-command`

raspberry pi resets ssh and hosts files on restart
`sudo nano /etc/cloud/cloud.cfg`
```
cloud_init_modules:
  - migrator
  - seed_random
  - bootcmd
  - write_files
  - growpart
  - resizefs
  - disk_setup
  - mounts
  - set_hostname
  - update_hostname
#  - update_etc_hosts
  - ca_certs
  - rsyslog
  - users_groups
#  - ssh
```
Calico installation for x86
```
cd /usr/local/bin/
sudo curl -o calicoctl -O -L https://github.com/projectcalico/calico/releases/download/v3.27.0/calicoctl-linux-amd64
sudo chmod +x calicoctl
```

ARM processors are fuckin wild bro
```
cd /usr/local/bin/
sudo curl -o calicoctl -O -L https://github.com/projectcalico/calico/releases/download/v3.27.0/calicoctl-linux-arm64
sudo chmod +x calicoctl
```

```
kubectl label
```

`sudo ufw allow 7946`

Route Static node requests to MetalLB ip address:
```
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.86.35:80
iptables -t nat -A PREROUTING -p tcp --dport 443 -j DNAT --to-destination 192.168.86.35:443
iptables -A FORWARD -p tcp -d 192.168.86.35 --dport 80 -j ACCEPT
iptables -A FORWARD -p tcp -d 192.168.86.35 --dport 443 -j ACCEPT
```
^This did not work :/
# Can't Delete a namespace (???)
Likely a resource with "finalizers" on it in the way
find all resource types in the cluster
```
kubectl api-resources --verbs=list --namespaced=true
```
ChatGPT suggests automating the next part:
```
for resource in $(kubectl api-resources --verbs=list --namespaced=true -o name); do
    echo "Resource: $resource"
    kubectl get $resource -n <namespace>
done
```
Check each resource within the namespace, if it exists, use `kubectl edit` to remove the finalizers. 
# 1. Install Prerequisites
Switch to root:
`sudo -i`
Load modules for next steps:
```
modprobe overlay
modprobe br_netfilter
```
Update kernel networking to allow necessary traffic:
```
cat << EOF | tee /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```
Install necessary key for containerd:
```
mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
Install containerd:
```
apt-get update && apt-get install containerd.io -y
containerd config default | tee /etc/containerd/config.toml
sed -e 's/SystemdCgroup = false/SystemdCgroup = true/g' -i /etc/containerd/config.toml
systemctl restart containerd
```
Add repo for Kubernetes and then install it (Might be outdated):
```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
apt-get update && apt-get install -y kubeadm=1.30.1-00 kubelet=1.30.1-00 kubectl=1.30.1-00
apt-mark hold kubelet kubeadm kubectl
```
Add the host IP address in the hosts file and tie it to k8scp:
`nano /etc/hosts`
# 2. Initialize the Cluster
**Must be logged into root account:**
Set up the kubeadm initialization script (Might be outdated):
`nano kubeadm-config.yaml`
```
apiVersion: kubeadm.k8s.io/v1beta3
kind: ClusterConfiguration
networking:
  serviceSubnet: "10.96.0.0/12" # Default range for services
  podSubnet: "10.244.0.0/16" # Custom range for Calico; ensure it doesn't conflict with your network
  dnsDomain: "cluster.local" # Default DNS domain; change if you have a specific need
controlPlaneEndpoint: "k8scp:6443" # Your control plane endpoint; matches your hosts file configuration
apiServer:
  extraArgs:
    advertise-address: "<Primary Node IP>" # Replace with your control plane's static IP
    allow-privileged: "true"
controllerManager:
  extraArgs: {}
scheduler:
  extraArgs: {}
kubeletConfiguration:
  baseConfig:
    resolvConf: "/run/systemd/resolve/resolv.conf"
---
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
mode: "ipvs"
```
Need to enable ipv4 forwarding:
`echo 1 > /proc/sys/net/ipv4/ip_forward`
Initiate the Kubernetes cl;uster:
`kubeadm init --config=kubeadm-config.yaml --upload-certs | tee kubeadm-init.out`
Exit root user and configure the node for the standard user account:
```
exit
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
sudo cp /root/calico.yaml .
kubectl apply -f calico.yaml
```
Install Helm:
```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update && sudo apt-get install helm
```

# 3. Join a Node to the Cluster
## Control Plane Setup
**Must be logged into root account:**
Need to enable ipv4 forwarding:
`echo 1 > /proc/sys/net/ipv4/ip_forward`
Initiate the Kubernetes node:
```
kubeadm join k8scp:6443 --token <token> /
--discovery-token-ca-cert-hash sha256:<token sha256 hash> /
--control-plane /
--certificate-key <certificate key> | tee kubeadm-join.out
```
Exit root user and configure the node for the standard user account:
```
exit
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
sudo cp /root/calico.yaml .
kubectl apply -f calico.yaml
```
(Optional) Remove taints from the node to allow scheduling workloads:
```
kubectl taint nodes <hostname> node-role.kubernetes.io/control-plane:NoSchedule-
```

(Optional) Install Helm:
```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update && sudo apt-get install helm
```
## Worker Node Setup
**Must be logged into root account:**
Need to enable ipv4 forwarding:
`echo 1 > /proc/sys/net/ipv4/ip_forward`
```
kubeadm join k8scp:6443 --token <token> /
--discovery-token-ca-cert-hash sha256:<token sha256 hash> /
--certificate-key <certificate key> | tee kubeadm-join.out
```

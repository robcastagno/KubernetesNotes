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


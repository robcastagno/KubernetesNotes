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
Need to enable ipv4 forwarding:
`echo 1 > /proc/sys/net/ipv4/ip_forward`
```
kubeadm join k8scp:6443 --token <token> /
--discovery-token-ca-cert-hash sha256:<token sha256 hash> /
--certificate-key <certificate key> | tee kubeadm-join.out
```

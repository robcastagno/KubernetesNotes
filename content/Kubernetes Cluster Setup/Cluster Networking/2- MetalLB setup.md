[Installation](https://metallb.universe.tf/installation/)
It's necessary to switch kube-proxy to strictARP in IPVS mods for this to work
`kubectl edit configmap -n kube-system kube-proxy`
```
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
mode: "ipvs"
  ipvs:
    strictARP: true
```
Apply the MetalLB manifest to install:
`kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.5/config/manifests/metallb-native.yaml`

Then create an IP pool and give it an L2Advertisement
```
---
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: <name>
  namespace: metallb-system
spec:
  addresses:
  - <IP Range>
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: <name>
  namespace: metallb-system
```
L2Advertisements will default to whatever IPAddressPools are available to advertise.

When configuring Services to utilize a LoadBalancer, if they need to/can share an IP address, make use of the `metallb.universe.tf/allow-shared-ip: "key"` annotation. MetalLB will "try" to colocate services to that IP. [Example in official Documentation](https://metallb.universe.tf/usage/)
This doesn't allow for two different services to use two different protocols on the same numbered port. 
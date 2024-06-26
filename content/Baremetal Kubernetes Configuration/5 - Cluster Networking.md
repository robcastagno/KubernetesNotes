# 1- Installing the CNI (Calico)
Install the Tigera Operator:
`
`kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/tigera-operator.yaml`
Download the custom resources:
`curl https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/custom-resources.yaml -O`
Create the manifest to install Calico:
`kubectl create -f custom-resources.yaml`

To update, just `kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v<version>/manifests/tigera-operator.yaml` and it will update itself.

Todo: Look into further configurations and controls provided by Calico
# 2- Setting up LoadBalancing (MetalLB)
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
# 3- Dynamic Provisioning with an nfs server (nfs-subdir-external-provisioner)
By default, Kubernetes doesn't support nfs servers as a dynamic storage backend...but...
There's a workaround, using this helm chart:
`helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/`
This should deploy the helm chart with most of the settings that really matter atm:
```
helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner /
--set nfs.server=<nfs IP> /
--set nfs.path=/<path on nfs> /
--set storageClass.defaultClass=true /
--set storageClass.reclaimPolicy=Retain /
--set storageClass.accessModes=ReadWriteMany /
-n nfs-subdir-external-provisioner
```
[Github](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner)
# 4- Automatic SSL Certificates (Cert Manager)
[Installation](https://cert-manager.io/docs/installation/kubectl/)
Just apply the manifest:
`kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.0/cert-manager.yaml`

Need to create an issuer, here's a template:
```
---
apiVersion: v1
kind: Secret
metadata:
  name: cloudflare-api-token-secret
  namespace: traefik
type: Opaque
stringData:
  api-token: <API-Token>
---
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: cloudflare-issuer
  namespace: traefik
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: <email>
    privateKeySecretRef:
      name: cloudflare-key
    solvers:
      - dns01:
          cloudflare:
            email: <email>
            apiTokenSecretRef:
              name: cloudflare-api-token-secret
              key: api-token
```
And after we have a resource that needs it, we can create a certificate:
```
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: wildcard-template-com
  namespace: traefik
spec:
  secretName: wildcard-template-com-tls
  dnsNames:
    - "template.com"
    - "*.template.com"
  issuerRef:
    name: cloudflare-issuer
    kind: Issuer
```
# 5- Ingress Controller (Traefik)
We use the helm chart for this one:
```
helm repo add traefik https://traefik.github.io/charts
helm repo update
helm install traefik traefik/traefik --namespace traefik --create-namespace
```
Gives a default installation of Traefik Ingress. We'll need to configure IngressRoutes to use it, here's a template:
```
apiVersion: traefik.io/v1alpha1
kind: IngressRoute
metadata:
  name: template-com-tls
  namespace: <namespace>
spec:
  entryPoints:
    - websecure
  routes:
  - match: Host(`<domain>.template.com`)
    kind: Rule
    middlewares:
    services:
    - name: <name>
      port: <port>
```
This will route anything matching the domain rule received through the websecure entrypoint (443) to the port provided by the service. 
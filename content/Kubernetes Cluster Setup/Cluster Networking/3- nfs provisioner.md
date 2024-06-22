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

Install the Tigera Operator:
`
`kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/tigera-operator.yaml`
Download the custom resources:
`curl https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/custom-resources.yaml -O`
Create the manifest to install Calico:
`kubectl create -f custom-resources.yaml`

To update, just `kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v<version>/manifests/tigera-operator.yaml` and it will update itself.

Todo: Look into further configurations and controls provided by Calico
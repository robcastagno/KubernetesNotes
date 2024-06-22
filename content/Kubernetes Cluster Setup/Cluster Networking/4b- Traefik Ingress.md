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
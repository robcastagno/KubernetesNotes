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
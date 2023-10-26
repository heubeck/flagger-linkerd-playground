# Certificate generation process

https://linkerd.io/2.14/tasks/generate-certificates/

## Root CA

```
# Trust anchor certificate
step certificate create root.linkerd.cluster.local ca.crt ca.key --profile root-ca --no-password --insecure --not-after 438000h
```

```
# Create ConfigMap holding the trust anchor certificate
kubectl create configmap -n linkerd linkerd-identity-trust-roots --from-file=ca-bundle.crt=ca.crt --dry-run=client -o yaml > linkerd-identity-trust-roots.yaml
```

### Identity issuer

```
# Issuer certificate and key
step certificate create identity.linkerd.cluster.local issuer.crt issuer.key --profile intermediate-ca --not-after 438000h --no-password --insecure --ca ca.crt --ca-key ca.key
```

```
# Create Secret holding identity issuer certificate
kubectl create secret tls -n linkerd linkerd-identity-issuer --cert=issuer.crt --key=issuer.key -o yaml --dry-run=client > linkerd-identity-issuer.yaml
```

### Proxy injector

```
# Proxy injector certificate and key
step certificate create linkerd-proxy-injector.linkerd.svc injector.crt injector.key --kty=RSA --profile leaf --not-after 438000h --no-password --insecure --ca ca.crt --ca-key ca.key
```

```
# Create tls Secret holding the injector certificate
kubectl create secret tls -n linkerd linkerd-proxy-injector-k8s-tls --cert=injector.crt --key=injector.key -o yaml --dry-run=client > linkerd-proxy-injector-k8s-tls.yaml
```

### Policy validator

```
# Policy validator certificate and key
step certificate create linkerd-policy-validator.linkerd.svc policy.crt policy.key --kty=RSA --profile leaf --not-after 438000h --no-password --insecure --ca ca.crt --ca-key ca.key
```

```
# Create tls Secret holding the injector certificate
kubectl create secret tls -n linkerd linkerd-policy-validator-k8s-tls --cert=policy.crt --key=policy.key -o yaml --dry-run=client > linkerd-policy-validator-k8s-tls.yaml
```

### SP validator

```
# SP validator certificate and key
step certificate create linkerd-sp-validator.linkerd.svc sp.crt sp.key --kty=RSA --profile leaf --not-after 438000h --no-password --insecure --ca ca.crt --ca-key ca.key
```

```
# Create tls Secret holding the injector certificate
kubectl create secret tls -n linkerd linkerd-sp-validator-k8s-tls --cert=sp.crt --key=sp.key -o yaml --dry-run=client > linkerd-sp-validator-k8s-tls.yaml
```

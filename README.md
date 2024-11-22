# labs-k8s-kind


k8s with Kind poc

## create cluster
```bash
kind create cluster --config kind-config.yaml
kubectl cluster-info --context kind-poc
```

## install metallb
```bash
metallb_url="https://raw.githubusercontent.com/metallb/metallb/v0.12.1/manifests/"
kubectl apply  \
    -f ${metallb_url}/namespace.yaml \
    -f ${metallb_url}/metallb.yaml
  295  
```

## configure metallb
```bash
# reserve ips on cidr of docker bridge network
docker network inspect -f '{{.IPAM.Config}}' kind 
kubectl apply -f k8s.kind.metallb.yml
```

# deploy app

```bash
kubectl apply -f k8s.echo.yml



openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out self-signed-tls.crt -keyout self-signed-tls.key \
    -subj "/CN=echo.local.antoniocaccamo.work" \
    -reqexts SAN \
    -extensions SAN \
    -config <(cat /etc/ssl/openssl.cnf \
        <(printf "[SAN]\nsubjectAltName=DNS:echo.local.antoniocaccamo.work,DNS:*.echo.local.antoniocaccamo.work"))


kubectl create secret tls self-signed-tls \
  --key self-signed-tls.key \
  --cert self-signed-tls.crt

kubectl get ingress,secret
# NAME                                     CLASS    HOSTS                            ADDRESS     PORTS     AGE
# ingress.networking.k8s.io/echo-ingress   public   echo.local.antoniocaccamo.work   127.0.0.1   80, 443   4h57m
# 
# NAME                     TYPE                DATA   AGE
# secret/self-signed-tls   kubernetes.io/tls   2      3m14s

curl -k https://$(hostname) -H 'Host: echo.local.antoniocaccamo.work' 
```
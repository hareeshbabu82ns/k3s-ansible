# traefik cert-manager let's encrypt

Documentation [here](https://docs.technotim.live/posts/kube-traefik-cert-manager-le/)

Video [here](https://www.youtube.com/watch?v=G4CmbYL9UPg)


```sh
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh


helm version

helm repo add traefik https://helm.traefik.io/traefik
helm repo update


kubectl create namespace traefik
kubectl get namespaces

# update `loadBalancerIP` in `values.yaml`
helm install --namespace=traefik traefik traefik/traefik --values=values.yaml
kubectl get svc --all-namespaces -o wide

kubectl get pods --namespace traefik

# create middleware
kubectl apply -f default-headers.yaml
kubectl get middleware

# generate password has for traefik dashboard
sudo apt-get update
sudo apt-get install apache2-utils

htpasswd -nb techno password | openssl base64

# update password in dashboard `secret-dashboard.yaml`
kubectl apply -f secret-dashboard.yaml
kubectl get secrets --namespace traefik

kubectl apply -f middleware.yaml

# create local dns entry `traefik.<domain>` to point to `<ingress-ip>`
kubectl apply -f ingress.yaml

```

* `cert-manager` install
```sh

kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.11.1/cert-manager.crds.yaml

helm repo add jetstack https://charts.jetstack.io
helm repo update

kubectl create namespace cert-manager
helm install cert-manager jetstack/cert-manager --namespace cert-manager --values=values.yaml --version v1.11.1


# create issuers ## Staging ##

## created cloudflare api `cf-kube-certs-mgr` with Zone-DNS-Edit and Zone-Zone-Read auths
kubectl apply -f secret-cf-token.yaml

## test with staging first
kubectl apply -f letsencrypt-staging.yaml

# create certificates

kubectl apply -f certificates/staging/local-example-com.yaml

kubectl get challenges

kubectl get orders

kubectl describe order kube-terabits-io-5wzz2-2897929864

# create issuers ## Production ##
kubectl apply -f issuers/letsencrypt-production.yaml

kubectl apply -f certificates/production/local-example-com.yaml
```
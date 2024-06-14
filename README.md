# eks-testing
## Terraform
Will create an EKS cluster, VPC, Subnets, etc with small node groups
You can run in the `terraform` repo to create everything
```
terraform apply  -var-file=terraform.tfvars
```

## Components
- Install Traefik
- Install Cert Manager
- Install ArgoCD
- Install WhoAmI app

### Installations
#### Traefik
```
# add repo
helm repo add traefik https://traefik.github.io/charts --force-update

# install chart
helm upgrade --namespace traefik traefik traefik/traefik --values v2/00-traefik-helm/traefik-custom.yaml

# install certs
kubectl apply -f v2/00-traefik-helm/traefik-certs.yaml

# alow access to dashboard
kubectl apply -f v2/00-traefik-helm/dashboard.yaml
```

#### Cert Manager
```
# add chart
helm repo add jetstack https://charts.jetstack.io --force-update

# install
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace  --version v1.15.0 --set crds.enabled=true

# add issuers
kubectl apply -f v2/01-cert-manager-helm/00cert-issuer.yaml
```

#### Install Who Am I App
```
kubectl apply -f v2/02whoami-app/00.yaml
```

#### Install ArgoCD
```
# add argocd helm chart
helm repo add argo https://argoproj.github.io/argo-helm --force-update

# install argocd
helm upgrade argocd argo/argo-cd -n argocd -f v2/03-argocd/00-helm-values.yaml

# add ingresses for argocd
kubectl apply -f v2/03-argocd/01-ingress.yaml
```

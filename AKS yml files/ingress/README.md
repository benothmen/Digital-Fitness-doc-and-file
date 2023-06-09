# Kubernetes Ingress with TLS/SSL  

This repo is demoing the configuration for Ingress and HTTPS/TLS/SSL in Kubernetes.  

In Kubernetes, we can expose the services publicly by choosing the type LoadBalancer. That will create a public IP address for each service. But, we want to reduce the number of IP adresses to make some saving. And we want to map a URL like mycompany.com/login and mycompany.com/products to the right service object.  
Well, this could be done through Kubernetes Ingress resources.  
```bash

# Create a namespace for the apps
kubectl apply -f app-namespace.yaml

# Deploy the 2 sample apps into Kubernetes
kubectl apply -f app1-deploy-svc.yaml 
kubectl apply -f app2-deploy-svc.yaml

# Get the 2 public IP addresses 
kubectl get services --namespace app

# Add the Helm chart for Nginx Ingress
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

# Install the Helm (v3) chart for nginx ingress controller
# (If using Bash instead of Powershell, replace ` with \)
helm install app-ingress ingress-nginx/ingress-nginx `
     --namespace ingress `
     --create-namespace `
     --set controller.replicaCount=2 `
     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux `
     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

# Get the Ingress Controller public IP address
kubectl get services --namespace ingress

# Update the service type to ClusterIP instead of LoadBalancer 
# in app-deploy.yaml file
# Delete and redeploy the service for the update to take effect

kubectl apply -f app1-deploy-svc.yaml 

# Deploy the Ingress resource into Kubernetes
kubectl apply -f app-ingress.yaml 

```
In this second part of the lab, we will enable HTTPS in Kubernetes using Cert Manager and Lets Encrypt.
The Cert Manager is used to automatically generate and configure Let's Encrypt certificates.

```bash

# Create a namespace for Cert Manager
kubectl create namespace cert-manager

# Get the Helm Chart for Cert Manager
helm repo add jetstack https://charts.jetstack.io
helm repo update

# Install Cert Manager using Helm charts
helm install cert-manager jetstack/cert-manager `
    --namespace cert-manager `
    --version v0.14.0 `
    --set installCRDs=true

# Check the created Pods
kubectl get pods --namespace cert-manager

# Install the Cluster Issuer
kubectl apply --namespace app -f ssl-tls-cluster-issuer.yaml

# Install the Ingress resource configured with TLS/SSL
kubectl apply --namespace app -f ssl-tls-ingress.yaml

# Verify that the certificate was issued
kubectl describe cert app-web-cert --namespace app

# Check the services
kubectl get services -n app

# Now test the app with HTTPS: https://frontend.<ip-address>.nip.io

# Cleanup resources
helm delete cert-manager --namespace cert-manager
kubectl delete namespace cert-manager
kubectl delete --namespace app -f ssl-tls-cluster-issuer.yaml
kubectl delete --namespace app -f ssl-tls-ingress.yaml

```

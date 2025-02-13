# Helm

## Configure kubeconfig for kubectl for Docker Desktop k8s Cluster
```t
# Verify kubectl version
kubectl version 
kubectl version --short
kubectl version --client --output=yaml

# List Config Contexts
kubectl config get-contexts

# Config Current Context
kubectl config current-context

# Config Use Context (Only if someother context is present in current-context output)
kubectl config use-context docker-desktop

# List Kubernetes Nodes
kubectl get nodes

# List k8s Deployments
kubectl get deploy

# List k8s pods
kubectl get pods

# List k8s Services
kubectl get svc

# Access Application
http://localhost:31300
```

## Step-07: Install Helm using Package Managers
- [Install Helm](https://helm.sh/docs/intro/install/)
```t
# From Chocolatey (Windows)
choco install kubernetes-helm

# From Scoop (Windows)
scoop install helm

# Verify Helm version
helm version

# Helm Environment variables
helm env
```

## Helm Basic Commands
We will use the following commands as part of this demo
- helm repo list
- helm repo add <DESIRED_NAME> <REPO_URL>
- helm search repo <CHART_NAME>
- helm search repo <CHART_NAME> --versions
- helm install <RELEASE_NAME> <CHART_NAME>
- helm repo update
- helm list
- helm uninstall <RELEASE_NAME>

```t
# List Helm Repositories
helm repo list

# Add Helm Repository
helm repo add <DESIRED-NAME> <REPO_URL>
helm repo add mybitnami https://charts.bitnami.com/bitnami

# Search Helm Repository
helm search repo <CHART_NAME>
helm search repo nginx

helm install <RELEASE-NAME> <DESIRED_NAME/CHART_NAME>
helm install mynginx mybitnami/nginx

# List Helm Releases (Default Table Output)
helm list
helm ls --output=yaml

# Helm resources are namespaced
helm ls --namespace=default
helm ls -n default

# Uninstall Helm Release
helm uninstall <RELEASE-NAME>
helm uninstall mynginx 
```
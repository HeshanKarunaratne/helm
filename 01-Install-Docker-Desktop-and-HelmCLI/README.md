# Install Docker Desktop and HELM CLI

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
# MacOS
brew install helm

# From Chocolatey (Windows)
choco install kubernetes-helm

# From Scoop (Windows)
scoop install helm

# Verify Helm version
helm version

# Helm Environment variables
helm env
```
## Step-08: Windows Install Helm CLI using package
```t
# Helm Releases - Download Windows amd64
https://github.com/helm/helm/releases

# Uzip the file
helm-v3.12.3-windows-amd64.zip

# Copy to C:\helm Drive
C:\ Drive

# Set Path
C:\helm\windows-amd64
```

## Additional Optional Step: Install kubectl (if not installed by default)
```t
# Download & Install kubectl
https://kubernetes.io/docs/tasks/tools/
MacOS kubectl Install: https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/
# Downlaod MacOS Intel (Update kubectl version)
curl -LO "https://dl.k8s.io/release/v1.27.2/bin/darwin/amd64/kubectl"
# Download MacOS Apple Silicon (Update kubectl version)
curl -LO "https://dl.k8s.io/release/v1.27.2/bin/darwin/arm64/kubectl"

# Make Binary executable
chmod +x ./kubectl

# Move the kubectl binary to a file location on your system PATH.
sudo mv ./kubectl /usr/local/bin/kubectl
ls -lrta /usr/local/bin/kubectl

# Verify kubectl version
kubectl version 
kubectl version --short
kubectl version --client --output=yaml
```
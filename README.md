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

### Add Custom Helm Repo
```t
# Add Helm Repository
helm repo add <DESIRED-NAME> <HELM-REPO-URL>
helm repo add stacksimplify https://stacksimplify.github.io/helm-charts/

# List Helm Repositories
helm repo list

# Search Helm Repository
helm search repo <KEY-WORD>
helm search repo mychart1 --versions

# Install myapp1 Helm Chart
helm install <RELEASE-NAME> <LOCAL_REPO_NAME/CHART_NAME>
helm install myapp1 stacksimplify/mychart1

# List Pods
kubectl get pods

# List Services 
kubectl get svc

# Access Application
http://localhost:<NODE-PORT>
http://localhost:31231

# Review the Docker Image Versions we are using
https://github.com/users/stacksimplify/packages/container/package/kubenginx
Image Tags: 1.0.0, 2.0.0, 3.0.0, 4.0.0

helm upgrade <RELEASE-NAME> <LOCAL_REPO_NAME/CHART_NAME> --set <OVERRIDE-VALUE_IN_values.yaml>
helm upgrade myapp1 stacksimplify/mychart1 --set "image.tag=2.0.0"

# In github you have values.yaml and Chart.yaml(appVersion will be here)
# image.tag will be empty in Chart.yaml and it will use the value of appVersion by default
helm upgrade myapp1 stacksimplify/mychart1 --set "image.tag=3.0.0"
helm upgrade myapp1 stacksimplify/mychart1 --set "replicaCount=5"

# helm history
helm history RELEASE_NAME
helm history myapp1

# Helm Status
helm status RELEASE_NAME
helm status myapp1

# Helm Status - Show Resources (display the resources of the named release)
helm status myapp1 --show-resources

# Uninstall Helm Release
helm uninstall myapp1
```

### Helm Upgrade with Chart versions
```t
# Search Helm Repo
helm search repo mychart2
Observation: Should display latest version of mychart2 from stacksimplify helm repo

# Search Helm Repo with --versions
helm search repo mychart2 --versions
Observation: Should display all versions of mychart2

# Search Helm Repo with --version
helm search repo mychart2 --version "0.2.0"
Observation: Should display specified version of helm chart

# Install Helm Chart by specifying Chart Version
helm install myapp101 stacksimplify/mychart2 --version "0.1.0"

# List Kubernetes Resources Deployed as part of this Helm Release
helm status myapp101 --show-resources

# Helm Upgrade using Chart Version
helm upgrade myapp101 stacksimplify/mychart2 --version "0.2.0"

# List Release History
helm history myapp101

# Helm Upgrade using without the Chart Version - upgrades to the latest version
helm upgrade myapp101 stacksimplify/mychart2

# Rollback to previous version
helm rollback RELEASE-NAME 
helm rollback myapp101
Observation: Rollbacks to "2.0.0" version

# Rollback to previous version
helm rollback RELEASE-NAME REVISION
helm rollback myapp101 1
```

### Helm Uninstall Keep History
- Uninstall Helm Release in a most effective way (best practice) so that we don't loose the history of our Helm Release

```t
# Uninstall Helm Release with --keep-history Flag
helm uninstall <RELEASE-NAME> --keep-history
helm uninstall myapp101 --keep-history

# List Helm Releases which are uninstalled
helm list --uninstalled
Observation:We should see uninstalled release

# Rollback Helm Uninstalled Release
# This is possible since we use the --keep-history flag
helm rollback <RELEASE> [REVISION] [flags]
helm rollback myapp101 3
Observation: It should rollback to specific revision number from revision history
# If you uninstall without the flag you cant rollback or see it in --unistalled section as well
```

- It is recommended to always use `--keep-history Flag` for following reasons
- Keeping Track of uninstalled releases
- Quick Rollback if that Release is required

### Helm Install with Generate Name Flag
- `--generate-name` flag for `helm install` is very very important option because if we want to generate the names of our releases without throwing duplicate release errors we can use this setting. 

```t
# Install helm with --generate-name flag
helm install <REPO_NAME_LOCAL/CHART_NAME> --generate-name
helm install stacksimplify/mychart1 --generate-name

# Uninstall Helm Release
helm uninstall <RELEASE-NAME>
helm uninstall mychart1-1689683948
```
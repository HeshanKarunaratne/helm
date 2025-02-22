# Helm

### Configure kubeconfig for kubectl for Docker Desktop k8s Cluster
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

### Install Helm using Package Managers
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

### Helm Basic Commands
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

### Helm Install Atomic Flag
- `--atomic` flag deletes the helm installation if any error occurs during the installation
- The `--wait` flag will be set automatically if `--atomic` is used
- `--wait` will wait until all Pods, PVCs, Services, and minimum number of Pods of a Deployment, StatefulSet, or ReplicaSet are in a ready state before marking the release as successful. It will wait for as long as `--timeout`
- `--timeout` defaults to 5mins

```t
# Install Helm Chart 
helm install dev101 stacksimplify/mychart1
Observation: dev101 in deployed status

# Access Application
http://localhost:31231

# Install Helm Chart 
helm install qa101 stacksimplify/mychart1
Observation: qa101 in failed status

# Install Helm Chart 
helm install qa101 stacksimplify/mychart1 --atomic
Observation: qa101 release is uninstalled 
```

### Helm with Kubernetes Namespaces
- Any resource we manage using HELM are specific to Kubernetes Namespace and by default all the resources we create belongs to default namespace
- In case if we want to deploy k8s resources to a namespace, we need to specify that using flag `--namespace` or `-n`
- We can also create a namespace during `helm install` using flags `--namespace` and `--create-namespace` 

```t
# List Kubernetes Namespaces 
kubectl get ns

# Install Helm Release by creating Kubernetes Namespace
helm install dev101 stacksimplify/mychart2 --version "0.1.0" --namespace dev --create-namespace

# Check the release installed in dev namespace
helm list -n dev
helm list --namespace dev

# Helm Status
helm status dev101 --show-resources --namespace dev

# List Kubernetes Pods
kubectl get pods --namespace dev

# List Services
kubectl get svc -n dev

# List Deployments
kubectl get deploy -n dev

# Helm Upgrade
helm upgrade dev101 stacksimplify/mychart2 --version "0.3.0" --namespace dev

# Uninstall Helm Release
helm uninstall dev101 --namespace dev

# Delete dev namespace
kubectl delete namespace dev
```

### Helm Override default values from values.yaml
- `--dry-run` will generate the templates it will create 
- `--debug` will give you more additional details on the installation

```t
# Helm Install with --dry-run command
helm install myapp101 stacksimplify/mychart1 --set service.nodePort=31240 --dry-run --debug
Observation: Templates will be generated with overridden values in the command
```

- We are going to override above release with below yaml file
```yaml
# change replicas from 1 to 2
replicaCount: 2

# Add tag as "2.0.0" which will override the default appversion "1.0.0" from our mychart1
image:
  repository: ghcr.io/stacksimplify/kubenginx
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: "2.0.0"

# Change nodePort from 31231 to 31250
service:
  type: NodePort
  port: 80
  nodePort: 31250
```

```t
# helm upgrade with --dry-run and --debug commands
helm upgrade myapp101 stacksimplify/mychart1 -f myvalues.yaml --dry-run --debug

# helm upgrade
helm upgrade myapp101 stacksimplify/mychart1 -f myvalues.yaml

# helm get values
helm get values RELEASE_NAME
helm get values myapp101
Observation: This command downloads a values file for a given release

# helm get values with --revision
helm get values RELEASE-NAME --revision int
helm get values myapp101 --revision 1

# helm get manifest
helm get manifest RELEASE-NAME
helm get manifest myapp101
Observation: This command fetches the generated manifest for a given release

# helm get manifest --revision
helm get manifest RELEASE-NAME --revision int
helm get manifest myapp101 --revision 1

# helm get all
helm get all RELEASE-NAME
helm get all myapp101
Observation: This command prints a human readable collection of information about the notes, hooks, supplied values, and generated manifest file of the given release

helm uninstall myapp101
```
### Values Hierarchy 
1. Sub chart `values.yaml` can be overriden by parents chart `values.yaml`
2. Parent charts `values.yaml` can be overriden by user-supplied value file `(-f myvalues.yaml)`
3. User-supplied value file `(-f myvalues.yaml)` can be overriden by `--set` parameters

- If you need to delete a key from the default values, you may override the value of the key to be null

```t
# Release: myapp101
helm install myapp101 stacksimplify/mychart1 --atomic

# Release: myapp102
# Option-1: Give desired port other than 31231
helm install myapp102 stacksimplify/mychart1 --set service.nodePort=31232

# Option-2: Pass null value to nodePort (service.nodePort=null)
helm install myapp102 stacksimplify/mychart1 --set service.nodePort=null
Observation: service.nodePort will get a value from 30000-32767 default value range

helm uninstall myapp101
helm uninstall myapp102
```

### Understand Helm Chart Folder Structure

```t
# Helm Create Chart
helm create <CHART-NAME>
helm create basechart
Observation: Below structure will be created

└── basechart
    ├── .helmignore
    ├── Chart.yaml
    ├── LICENSE
    ├── README.md
    ├── charts
    ├── templates
    │   ├── NOTES.txt
    │   ├── _helpers.tpl
    │   ├── deployment.yaml
    │   ├── hpa.yaml
    │   ├── ingress.yaml
    │   ├── service.yaml
    │   ├── serviceaccount.yaml
    │   └── tests
    │       └── test-connection.yaml
    └── values.yaml
```

- `Chart.yaml` contains all important chart information(metadata)
- `values.yaml` is the default configuration values for the chart
- `charts folder` is a directory containing any charts upon which the parent chart depends on
- `templates folder` is a directory of templates that, when combined with values, will generate valid Kubernetes manifest files. This is where helm finds the yaml manifests for our services, deployments and other k8s objects. If we have k8s resources for our application, we just need to convert them to helm equivalent and put them in the templates folder so that we can deploy them using `helm install` command.
- When you want to create reusable parts in your chart use `templates/_helpers.tpl`
- `templates/NOTES.txt` is an optional file which provides important information about our helm chart. The main difference is NOTES.txt rendered, its not sent to k8s cluster, but the output is displayed in the command line window
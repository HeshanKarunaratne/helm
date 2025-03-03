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

### Helm Builtin Objects

- Root object dot or Period(.)
    - .Release 
    - .Chart 
    - .Values 
    - .Capabilities 
    - .Template 
    - .Files 

- **Values Object:** Values passed into the template from the values.yaml file and from user-supplied files. By default, Values is empty.
- **Capabilities Object:** This provides information about what capabilities the Kubernetes cluster supports
- **Template Object:** Contains information about the current template that is being executed
- Put the below in `NOTES.txt` and test it

## Helm Development

### Template Action "{{ }}"

- Anything in between Template Action `{{ .Chart.Name }}` is called Action Element
- Anything in between Template Action `{{ .Chart.Name }}` will be rendered by helm template engine and replace necessary values

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx
  annotations:    
    annotations:    
    app.kubernetes.io/managed-by: {{ .Release.Service }}
    # quote function
    app.kubernetes.io/managed-by: {{ quote .Release.Service }} 
    # quote function with pipeline
    app.kubernetes.io/managed-by: {{ .Release.Service | quote }}
    # default Function
    app.kubernetes.io/name: {{ default "DEF" .Values.releaseName | lower }}
    # Controlling Leading and Trailing White spaces 
    leading-whitespace: "   {{- .Chart.Name }}    kalyan"
    trailing-whitespace: "   {{ .Chart.Name -}}    kalyan"
    leadtrail-whitespace: "   {{- .Chart.Name -}}    kalyan"
    # indent function
    indenttest: "  {{- .Chart.Name | indent 4 -}}  "
    # nindent function
    nindenttest: "  {{- .Chart.Name | nindent 4 -}}  "      
spec:
  replicas: {{ default 2 .Values.replicaCount }} 
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
        resources: 
        {{- toYaml .Values.resources | nindent 10}}
```

### Helm Development - Flow Controls
-  We can use `if/else` for creating conditional blocks in Helm Templates
- **eq:** For templates, the operators (eq, ne, lt, gt, and, or and so on) are all implemented as functions. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx
spec:
  {{- if eq .Values.myapp.env "prod" }}
  replicas: 4
  {{- else if eq .Values.myapp.env "dev" }}
  replicas: 2
  {{- else }}
  replicas: 1
  {{- end }}
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

### Flow Control If-Else with Boolean Check and "AND Function"

- **and:**  Returns the boolean AND of two or more arguments (the first empty argument, or the last argument).

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx
spec:
{{- if and .Values.myapp.retail.enableFeature (eq .Values.myapp.env "prod") }}
  replicas: 6
{{- else if eq .Values.myapp.env "prod" }}
  replicas: 4
{{- else if eq .Values.myapp.env "qa" }}
  replicas: 2
{{- else }}
  replicas: 1
{{- end }}
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

### Flow Control If-Else with OR Function

- **or:**  Returns the boolean OR of two or more arguments (the first non-empty argument, or the last argument).

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx
spec:
{{- if or (eq .Values.myapp.env "prod") (eq .Values.myapp.env "uat") }}
  replicas: 6
{{- else if eq .Values.myapp.env "qa" }}  
  replicas: 2
{{- else }}  
  replicas: 1  
{{- end }}
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

### Flow Control Function: not

- **not:**  Returns the boolean negation of its argument.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx
spec:
{{- if not (eq .Values.myapp.env "prod") }}
  replicas: 1
{{- else }}  
  replicas: 6
{{- end }}
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

### Flow Control With

- `with` action controls variable scoping. 
- `with` action can allow you to set the current scope (.) to a particular object.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx  
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
        appManagedBy: {{ $.Release.Service }}
        chartName: {{ $.Chart.Name }}
      {{- end }}
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

- `with` action statement sets the dot obejct "." to `.Values.podAnnotations` 
- Inside the `with` action block dot "." always refers to `.Values.podAnnotations` 
- Outside the `with` action block dot "." refers to Root Object
- To access Root Objects inside `with` action block we need to prepend that Root object with `$`

### Scope more detailed for "with" action block

- How to retrieve a single object from `.Values.myapps.data.config` ?
- What if there is only need for 1 or 2 values from `.Values.myapps.data.config` ?
- How to access each key value from `.Values.myapps.data.config` ?

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
data: 
{{- with .Values.myapps.data.config }}
  application-name: {{ .appName }}
  application-type: {{ .appType }}
{{- end}}
```

### Implement if-else for replicas with Boolean 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx
spec:
{{- with .Values.myapp }}
{{- if and .retail.enableFeature (eq .env "prod") }}
  replicas: 6
{{- else if eq .env "prod" }}
  replicas: 4
{{- else if eq .env "qa" }}
  replicas: 2
{{- else }}
  replicas: 1
{{- end }}
{{- end }}
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

### Helm Variables

```yaml
{{- $chartName := .Chart.Name | quote | upper -}}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: nginx  
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
        appManagedBy: {{ $.Release.Service }}
        chartName: {{ $chartName }}
      {{- end }}
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

### Flow Control Range Action with List

- Range in Helm is equivalent to for, foreach from prohramming languages
- In helm we can iterate over a collection using Range operator

### "Range Action" with "List of Values"

```yaml
{{- range .Values.namespaces}}
apiVersion: v1
kind: Namespace
metadata:
  name: {{ .name }}
---
{{- end }}  
```

### "Range Action" with "List of Values" with Variables

```yaml
{{- range $environment := .Values.environments }}
apiVersion: v1
kind: Namespace
metadata:
  name: {{ $environment.name }}
---  
{{- end }} 
```

### Range with Key Value pairs or Map or Dictionary 

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-configmap1
data: 
{{- range $key, $value := .Values.myapps.config1 }}
{{- $key | nindent 2}}: {{ $value }}
{{- end}}  
```

### Access Root Object in Range with Helm Variable

```yaml
{{- $chartname := .Chart.Name }}
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-configmap2
data:
{{- range $key, $value := .Values.myapps.config2 }}
{{- $key | nindent 2 }}: {{ $value }}-{{ $chartname }}
{{- end }}
```

### Named Templates

- Add dot "." (Root Object or period) at the end of template call to pass scope to template call
```yaml
{{/* Common Labels */}}
{{- define "helmbasics.labels" }}
    app: nginx
    chartname: {{ .Chart.Name }}
{{- end }}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-deployment
  labels:
    {{- template "helmbasics.labels" . }}
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

```yaml
{{/* Common Labels */}}
{{- define "helmbasics.labels" }}
    app: nginx
    chartname: {{ .Chart.Name }}
{{- end }}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}-deployment
  labels:
    {{- include "helmbasics.labels" . | upper }}
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```

- Move the named template `helmbasics.labels` to `_helpers.tpl` file
- But files whose name begins with an underscore (_) are assumed to not have a kubernetes manifest inside. 

### Helm Printf Function

```tpl
{{/* k8s Resource Name: String Concat with Hyphen */}}
{{- define "helmbasics.resourceName" }}
{{- printf "%s-%s" .Release.Name .Chart.Name }}
{{- end }}
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "helmbasics.resourceName" . }}-development
  labels:
  {{- include "helmbasics.labels" . | upper }}
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: ghcr.io/stacksimplify/kubenginx:4.0.0
        ports:
        - containerPort: 80
```
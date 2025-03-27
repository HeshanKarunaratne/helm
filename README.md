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

### Template in template

```tpl
{{/* Common Labels */}}
{{- define "helmbasics.labels"}}
    app: nginx
    chartname: {{ .Chart.Name }}
    template-in-template: {{ include "helmbasics.resourceName" . }}
{{- end }}

{{/* k8s Resource Name: String Concat with Hyphen */}}
{{- define "helmbasics.resourceName" }}
{{- printf "%s-%s" .Release.Name .Chart.Name }}
{{- end }}
```

### Create and package charts

```t
# Helm Create Chart
helm create <CHART-NAME>
helm create myfirstchart

# Helm Install
helm install myapp1v1 .

# Helm Status
helm status myapp1v1 --show-resources

# Helm Package - v1.0.0
helm package myfirstchart/ -d packages/
helm package myfirstchart/ --destination packages/

# Helm Install from package - v2.0.0
helm install myapp1v2 packages/myfirstchart-2.0.0.tgz --set service.nodePort=31232

# Packaging using commands and not changing Chart.yaml
helm package myfirstchart/ --app-version="3.0.0" --version="3.0.0" --destination packages/

# Helm Install from package - v3.0.0
helm install myapp1v3 packages/myfirstchart-3.0.0.tgz --set service.nodePort=31233 --atomic

# Uninstall all
helm uninstall myapp1v1
helm uninstall myapp1v2
helm uninstall myapp1v3
```

### Helm Show Commands

```t
# Helm Show Chart
helm show chart myfirstchart/
helm show chart packages/myfirstchart-2.0.0.tgz

# Helm Show Values
helm show values myfirstchart/
helm show values packages/myfirstchart-2.0.0.tgz

# Helm Show readme
helm show readme myfirstchart/

# Helm Show All
helm show all myfirstchart/
helm show all packages/myfirstchart-2.0.0.tgz
```

### Helm Dependency Command

- **helm dependency list:** List all of the dependencies declared in a chart.
- **helm dependency update:** update parent chart `charts/` folded based on the contents of file `Chart.yaml`

```t
# Create Parent Chart
helm create parentchart

# Helm Dependency List
helm dependency list
Observation: 
You should see status "missing" because we still didnt do helm dependency update

# Helm Dependency Update
helm dependency update
Observation: 
1. We should see both charts (mychart1-0.1.0.tgz, mychart2-0.4.0.tgz, mysql-9.9.0.tgz)downloaded to "parentchart/charts" folder
2. We should see "Chart.lock" file in "parentchart" folder
```

### Helm Dependency Chart Version Ranges

```t
Helm Chart Version Notation: Major.Minor.Patch 
MySQL Helm Chart Version: 9.10.8
Major: 9
Minor: 10
Patch: 8
```

- We can define the version constraints using basic comparison operators

```t
# Basic Comparison Operators
version: "= 9.10.8" 
version: "!= 9.10.8" 
version: ">= 9.10.8"
version: "<= 9.10.8"
version: "> 9.10.8"   
version: "< 9.10.8"
version: ">= 9.10.8 < 9.11.0"  
```

### For Range Comparison Major: Caret Symbol(ˆ)
- The caret (^) operator is for major level changes once a stable (1.0.0) release has occurred.

```t
^9.10.1  is equivalent to >= 9.10.1, < 10.0.0
^9.10.x  is equivalent to >= 9.10.0, < 10.0.0   
^9.10    is equivalent to >= 9.10, < 10
^9.x     is equivalent to >= 9.0.0, < 10        
```

### For Range Comparison Minor: Tilde Symbol(~)
- The tilde (~) operator is for 
  - patch level ranges when a minor version is specified 
  - major level changes when the minor number is missing. 

```t
~9.10.1  is equivalent to >= 9.10.1, < 9.11.0
~9.10    is equivalent to >= 9.10, < 9.11
~9       is equivalent to >= 9, < 10
^9.x     is equivalent to >= 9.0.0, < 10        
```

### Helm Dependency Build Command
- **helm dependency build:** rebuild the `charts/` directory based on the `Chart.lock` file
- In short `dep update` command will negotiate with version constraints defined in `Chart.yaml` where as `dep build` will try to build or download or update whatever version preset in `Chart.lock` file
- If no lock file is found, `helm dependency build` will mirror the behavior of `helm dependency update`.

### Helm Dependency Repository @REPO vs REPO-URL
- When we are using Helm with DevOps pipelines across environments "@REPO" approach is not recommended
- REPO-URL approach (repository: "https://charts.bitnami.com/bitnami") is always recommended

### Helm Dependency - Alias

```yaml
apiVersion: v2
name: parentchart
description: Learn Helm Dependency Concepts
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4dev
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4qa  
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart2
```

```t
# Helm Dependency Update
helm dep update parentchart/

# Helm Install
helm install myapp1 parentchart/ --atomic

# Helm Status
helm status myapp1 --show-resources

# Access Application
parentchart: http://localhost:<port-from-get-svc-output>
childchart4dev: http://localhost:<port-from-get-svc-output>
childchart4qa: http://localhost:<port-from-get-svc-output>
mychart2: http://localhost:31232

# Helm Uninstall
helm uninstall myapp1
```

### Helm Dependency - Condition

- Implement `Condition` for enabling or disabling Sub Charts or Child Charts
- Override subchart(child chart) values from parent chart
- By default, any chart is enabled

```yaml
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4
  condition: mychart4.enabled
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart2
  condition: mychart2.enabled
```

```yaml
mychart4:
  enabled: false
mychart2:
  enabled: false
```

```t
# Helm Dependency Update
helm dep update

# Helm Install
helm install myapp1 parentchart/ --atomic

# Helm Status
helm status myapp1 --show-resources

# Helm Uninstall
helm uninstall myapp1
```

### Helm Dependency - Condition with Alias
- If we have multiple dependencies with same chart name `mychart4` with different alias names like `childchart4dev` and `childchart4qa` in this case we need to define values.yaml with `alias names` for enabling or disabling those sub charts

```yaml
apiVersion: v2
name: parentchart
description: Learn Helm Dependency Concepts
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4dev
  condition: childchart4dev.enabled
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4qa
  condition: childchart4qa.enabled  
```

```yaml
# Values for Child Charts with Alias Name of Chart
childchart4dev:
  enabled: false 
childchart4qa:
  enabled: true   
```

### Helm Dependency Tags
- Instead of using `condition` we are going to use `tags`
- If we have more amount of subcharts that need to be divided in to groups then we need to use `tags` instead of `condition`

```yaml
apiVersion: v2
name: parentchart
description: Learn Helm Dependency Concepts
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart4dev
  tags: 
    - frontend
- name: mychart2
  version: "0.4.0"
  repository: "https://stacksimplify.github.io/helm-charts/"
  alias: childchart2
  tags:
    - backend
 ```

```yaml
tags:
  frontend: false
  backend: false
```

 ```t
# Helm Install
helm upgrade myapp1 parentchart/ --atomic --set tags.backend=true

# Helm Install
helm upgrade myapp1 parentchart/ --atomic --set tags.backend=true --set tags.frontend=true
```

### Use Global Values in Sub Charts

- Untar the packages and use it
```yaml
apiVersion: v2
name: parentchart
description: Learn Helm Dependency Concepts
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart4
  version: "0.1.0"
  repository: "file://charts/mychart4"
  alias: childchart4
  tags: 
    - frontend
- name: mychart2
  version: "0.4.0"
  repository: "file://charts/mychart2"
  alias: childchart2
  tags: 
    - backend
```

```t
# Helm Pull MyChart4
helm pull https://stacksimplify.github.io/helm-charts/mychart4-0.1.0.tgz --untar

# Helm Pull MyChart2
helm pull https://stacksimplify.github.io/helm-charts/mychart2-0.4.0.tgz --untar

# Helm Dependency list
helm dependency list
Observation: Packages are in unpacked status

# Helm Dependency Update / Build
helm dependency update
Observation: you should find *.tgz files for both charts

# Helm Dependency list
helm dependency list
Observation: Packages are in ok status
```

### Import Values Explicit

```yaml
exports:
  # mapped to mychart1Data
  mychart1Data:
    mychart1appInfo:
      appName: kapp1
      appType: MicroService
      appDescription: Used for listing products    
```

```yaml
- name: mychart1
  version: "0.1.0"
  repository: "file://charts/mychart1"
  alias: childchart1
  tags: 
    - frontend
  import-values:
      # Explicit Values Import Usecase
    - mychart1Data
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name:  {{ include "parentchart.fullname" . }}-import-explicit
data:
{{- toYaml .Values.mychart1appInfo | nindent 2 }}
```

### Import Values Implicit

```yaml
apiVersion: v2
name: parentchart
description: Learn Helm Dependency Concepts
type: application
version: 0.1.0
appVersion: "1.16.0"
dependencies:
- name: mychart1
  version: "0.1.0"
  repository: "file://charts/mychart1"
  alias: childchart1
  tags: 
    - frontend

- name: mychart2
  version: "0.4.0"
  repository: "file://charts/mychart2"
  alias: childchart2
  tags: 
    - backend
  import-values: # Implicit Values Usecase
    - child: service 
    # Make sure to give a different name to parent
      parent: mychart2service   
    - child: image 
      parent: mychart2image      
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name:  {{ include "parentchart.fullname" . }}-import-implicit
data:
  serviceType: {{ .Values.mychart2service.type }}
  servicePort: {{ .Values.mychart2service.port | quote}}
  servicenodePort: {{ .Values.mychart2service.nodePort | quote }}
  imageRepository: {{ .Values.mychart2image.repository }}
```

### Helm Starters
- A new developer don't need to start from scratch in your organization if you already have starter charts. He can use them and build on top of it. 
- We can also enforce certain resources that needs to be available in the created charts.
- Where do we place the starter charts?
  - We need to place starter charts in folder "$HELM_DATA_HOME/starters" folder
- Are there any drawbacks?
  - The `Chart.yaml` will be overwritten by the generator

### helm lint 
- Examine a chart for possible issues
- This command takes a path to a chart and runs a series of tests to verify that the chart is well-formed

- Replace "mystarterchart" with `<CHARTNAME>` in all files
1. _helpers.tpl
2. deployment.yaml
3. service.yaml
4. NOTES.txt
5. Chart.yaml
6. values.yaml (Here just in comment)

- Copy mystarterchart to HELM_DATA_HOME/starters/

```t
# Helm Create using starter chart
helm create mychart9 --starter=mystarterchart

# Review mychart9 files
1. Chart.yaml
- It should be regenerated and versions should be overided for both version and appversion to 0.1.0
2. "charts" directory: We should see "mychart4" should be present as packaged file "mychart4-0.1.0.tgz" even though in our starter chart we have it as UNZIPPED
```

### Helm Plugins

```t
# List Helm Plugins
helm plugin list

# Install Helm Starter-Plugins
helm plugin install https://github.com/salesforce/helm-starter.git

# Install Helm Dashboard Plugin
helm plugin install https://github.com/komodorio/helm-dashboard.git

# Run helm Dashboard in given port
helm dashboard --port=8090
```

### Build Helm Plugin

```t
# myplugin1
name: "myplugin1"
version: "0.1.0"
usage: "Prints Helm Environment Variables"
description: |-
  Prints Helm Environment Variables
command: "env"

# Install Helm Plugin
helm plugin install myplugin1/
```

### Helm Hooks

- Helm release Life cycle
  - Install
  - Upgrade
  - Uninstall
  - RollBack

```yaml
apiVersion: v1
kind: Pod
metadata: 
  name: myhook-preinstall
  annotations:
    "helm.sh/hook": "pre-install"
spec:
  restartPolicy: Never
  containers:
    - name: myhook-preinstall-container
      image: busybox
      imagePullPolicy: IfNotPresent
      command:  ['sh', '-c', 'echo Pre-install hook Pod is running && sleep 15']      
```

```t
# Insall Helm Release
helm install myapp101 . --atomic

# List Kubernetes Pods
kubectl get pods
Observation:
1. We should see "myhook-preinstall" pod which should be completed status

# Upgrade Helm Release
helm upgrade myapp101 . --set image.tag=0.2.0

# List Kubernetes Pods
kubectl get pods
Observation:
1. We should see "myhook-preupgrade" pod which should be completed status

# Uninstall/Delete Helm Release
helm list
helm uninstall myapp101 

# List Kubernetes Pods
kubectl get pods
Observation:
1. We should see "myhook-postdelete" pod which should be completed status
2. We should see all the 3 hook pods present even after deleting/uninstalling the release
```

1. The resources that a hook creates are currently not tracked or managed as part of the release. 
2. Once Helm verifies that the hook has reached its ready state, it will leave the hook resource alone.
3. In short, `helm uninstall` will not delete hook resources. 

### Helm Hooks Delete Policy

1. We can define when to delete the hook resources using Hook Deletion Policies
  - **before-hook-creation:** Delete the previous resource before a new hook is launched (default)
  - **hook-succeeded:** Delete the resource after the hook is successfully executed
  - **hook-failed:** Delete the resource if the hook failed during execution

```yaml
  "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded
```

4. Downside of using hook-failed 
  - **hook-failed:** Delete the resource if the hook failed during execution
  - The downside of this during Chart Development phase is, when our hook fails and its resource deleted, we will not have an option to troubleshoot.

### Helm Hook Weights

- Hook weights can be positive or negative numbers but must be represented as strings (in double quotes "8")
- When Helm starts the execution cycle of hooks of a particular Kind (Example: kind:pod) it will sort those hooks in ascending order.
```yaml
annotations:
  "helm.sh/hook-weight": "5"
```

```yaml
  annotations:
    "helm.sh/hook": "pre-install"
    "helm.sh/hook-delete-policy": before-hook-creation
    "helm.sh/hook-weight": "-2"

  annotations:
    "helm.sh/hook": "pre-install"
    "helm.sh/hook-delete-policy": before-hook-creation
    "helm.sh/hook-weight": "5"

  annotations:
    "helm.sh/hook": "pre-install"
    "helm.sh/hook-delete-policy": before-hook-creation
    "helm.sh/hook-weight": "6"
```

### Helm Tests
- Helm tests feature enables automated testing of k8s applications deployed using helm charts.
- These tests ensure that the deployed application is functioning as expected.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: "{{ include "mydemoapp.fullname" . }}-test-connection"
  labels:
    {{- include "mydemoapp.labels" . | nindent 4 }}
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args: ['{{ include "mydemoapp.fullname" . }}:{{ .Values.service.port }}']
  restartPolicy: Never
```

### Helm Resource Policies
- Sometimes there are resources that should not be uninstalled when Helm runs a helm uninstall 
- Chart developers can add an annotation to a resource to prevent it from being uninstalled.
- The annotation "helm.sh/resource-policy": keep instructs Helm to skip deleting this resource when a helm operation (such as helm uninstall, helm upgrade or helm rollback) would result in its deletion.
- Helm will no longer manage it in any way. 

```yaml
metadata:
  annotations:
    "helm.sh/resource-policy": keep
```

```t
# Uninstall Helm Release
helm uninstall myapp1
Observation:
1. We should see deployment should not be uninstalled
2. Its pods also should be in running state
```

### Helm Charts Sign and Verify
- Why do we need to sign Helm charts?
  1. Integrity - It prevents attackers from injecting malicious code
  2. Authentication
    - When a Helm chart is signed, it comes with a digital signature generated using a private key held by the chart maintainer
    - Users can verify the authenticity of the chart using the corresponding public key

- Sign Process
  1. Generate private/public keys with gpg
  2. Export the private key to gpg format
  3. Package and sign using `helm package --sign` command

- Verify Process
  1. Export the public key to gpg format
  2. Verify helm chart using `helm verify` command

```t
# Helm Verify
helm verify --keyring <PUBLIC_KEY> <RELEASE.TGZ>

# Helm Install
helm install <RELEASE_NAME> <RELEASE.TGZ> --verify --keyring <PUBLIC_KEY> --atomic

# Helm Upgrade
helm upgrade <RELEASE_NAME> <RELEASE.TGZ> --verify --keyring <PUBLIC_KEY> --atomic --set image.tag="0.2.0"
```

```t
# Install gnupg on WindowsOS
choco install gnupg

# Verify version
gpg --version

# List Keys
gpg --list-keys

# Generating Private/Public Keys with gpg
gpg --full-generate-key

# Helm Install with --verify 
helm install myapp1 myfirstchart-0.1.0.tgz --verify --keyring public-key/helmsigndemo1-publickey.gpg --atomic

# Helm Upgrade with --verify 
helm upgrade myapp1 myfirstchart-0.1.0.tgz --verify --keyring public-key/helmsigndemo1-publickey.gpg --atomic --set image.tag="0.2.0"
```

- Our helm repo url will be github-pages url

- **artifacthub-repo.yml**
```yaml
repositoryID: < The ID of the Artifact Hub repository>
owners: # (optional, used to claim repository ownership)
  - name: Heshan Karunaratne
    email: heshan947102@gmail.com
ignore: # (optional, packages that should not be indexed by Artifact Hub)
  - name: package1
  - name: package2 # Exact match
    version: beta # Regular expression (when omitted, all versions are ignored)
```

### Validate JSON schema

- Type validation
```json
"replicaCount": {
  "type": "number"
 }
```

- Range validation

- Requirement checks
```json
 "required": [
    "replicaCount",
    "image"
  ]
```
- Constraint validations
```json
 "pullPolicy": {
    "type": "string",
    "pattern": "^(Always|Never|IfNotPresent)$"
  }
```

## Step-07: Create new chart using Starter Chart
- [Docker Image: kubenginxhelm](https://github.com/users/stacksimplify/packages/container/package/kubenginxhelm)
```t
# Change Directory
cd MYCHARTS

# Helm Create using starter chart
helm create mychart9 --starter=mystarterchart

# Review mychart9 files
1. Chart.yaml
- It should be regenerated and versions should be overided for both version and appversion to 0.1.0
- Update the appVersion to "0.3.0" with quotes(it should be string in quotes) 
- Our Docker Image version is also "0.3.0" which matches our Chart appVersion so we are good. 
- https://github.com/users/stacksimplify/packages/container/package/kubenginxhelm
2. deployment.yaml - Review it
3. service.yaml - Review it
4. values.yaml - Review it
5. NOTES.txt - Review it
6. "charts" directory: We should see "mychart4" should be present as packaged file "mychart4-0.1.0.tgz" even though in our starter chart we have it as UNZIPPED
```

## Step-08: Create Helm Release from new chart created using starter chart
```t
# Change Directory
cd MYCHARTS/mychart9

# Helm Lint
helm lint 

# Install Helm Release
helm install myapp901 .

# List Pods and Services
kubectl get pods
kubectl get svc

# Access Application
Parent Chart: http://localhost:31239
mychart4 chart: http://localhost:<port-from-get-svc-output>

# Uninstall Helm Release
helm uninstall myapp901
```
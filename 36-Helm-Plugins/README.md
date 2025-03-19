

## Step-04: Play with Helm Plugin Commands
```t
# Update Helm Plugin
helm plugin list
helm plugin update PLUGIN-NAME
helm plugin update starter

# Uninstall Helm Plugin
helm plugin list
helm plugin uninstall PLUGIN-NAME
helm plugin uninstall starter
helm plugin list
```

## Step-05: Install Couple of Releases
```t
# Helm Rep Add
helm repo list
helm repo add stacksimplify https://stacksimplify.github.io/helm-charts/
helm repo list

# Helm Install dev101
helm install dev101 stacksimplify/mychart1 --atomic
helm upgrade dev101 stacksimplify/mychart1 --atomic --set replicaCount=2
helm upgrade dev101 stacksimplify/mychart1 --atomic --set replicaCount=3

# Helm Install dev102
helm install dev102 stacksimplify/mychart2 --atomic

# List Helm Releases
helm list
```

## Step-06: (Optional) Lets install Helm Dashboard Plugin
- [Helm Dashboard Plugin Git Repo](https://github.com/komodorio/helm-dashboard)
- [Helm Dashboard Plugin from Artifacthub](https://artifacthub.io/packages/helm-plugin/helm-dashboard/dashboard)
- [Review Helm Dashboard Plugin plugin.yaml](https://github.com/komodorio/helm-dashboard/blob/main/plugin.yaml)

```t
# List Helm Plugins
helm plugin list

# Install Helm Plugin
helm plugin install https://github.com/komodorio/helm-dashboard.git

# Start Helm Plugin: dashboard
helm dashboard

# Review Dashboard Concepts
1. Clusters
2. Installed Charts
    - Release: dev101 
        - Resources
        - Manifests
        - Values
        - Notes
    - Revision: 1, 2, 3 
    - Revision Differences
3. Repository
4. Logout 
```

## Step-07: Uninstall Releases
```t
# Helm Uninstall
helm uninstall dev101
helm uninstall dev102
```

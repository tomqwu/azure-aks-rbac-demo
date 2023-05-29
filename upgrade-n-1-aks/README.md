Day 2 operations refer to the tasks that come after the initial deployment of a Kubernetes cluster (Day 1 operations). These tasks include monitoring, updating, scaling, and maintaining the cluster. Upgrading the AKS (Azure Kubernetes Service) user pools to an N-1 version of Kubernetes (K8s) is one such task. This means upgrading the cluster to the most recent stable version minus one.

```bash
az aks show --resource-group aks --name azure-rbac --query 'kubernetesVersion'
az aks get-upgrades --resource-group aks --name azure-rbac --output table
az aks upgrade --resource-group aks --name azure-rbac --kubernetes-version <DesiredN-1Version>
az aks show --resource-group aks --name azure-rbac --query 'kubernetesVersion'
az aks get-credentials --resource-group aks --name azure-rbac --overwrite-existing
```

# AKS RBAC

## Azure RBAC

When you take advantage of the integrated authentication between Azure Active Directory, or Azure AD, and Azure Kubernetes Service, or AKS, you gain the ability to utilize Azure AD users, groups, or service principals as subjects within Kubernetes' role-based access control, often referred to as Kubernetes RBAC. This integration eliminates the need for managing user identities and credentials separately in Kubernetes. However, it's important to note that you'll still be required to set up and manage Azure RBAC and Kubernetes RBAC independently from one another.

```bash
az aks create -g aks -n azure-rbac --enable-aad --enable-azure-rbac
```

```bash
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

```bash
az role assignment create --role "Azure Kubernetes Service RBAC Reader" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

[Custom Role Definition](https://learn.microsoft.com/en-us/cli/azure/role/definition#az_role_definition_create)
```json
{
    "Name": "AKS Deployment Reader",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

## K8s RBAC

Azure Kubernetes Service (AKS) can be configured to use Azure Active Directory (Azure AD) for user authentication. In this configuration, you sign in to an AKS cluster using an Azure AD authentication token. Once authenticated, you can use the built-in Kubernetes role-based access control (Kubernetes RBAC) to manage access to namespaces and cluster resources based on a user's identity or group membership.

[K8s RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

- Create Groups in AAD
- Create Users in AAD
- Add Users into AAD Groups
- Create k8s resources for each group
- Login from each user group and launch tasks

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1
# This role binding allows "dave" to read secrets in the "development" namespace.
# You need to already have a ClusterRole named "secret-reader".
kind: RoleBinding
metadata:
  name: read-secrets
  #
  # The namespace of the RoleBinding determines where the permissions are granted.
  # This only grants permissions within the "development" namespace.
  namespace: development
subjects:
- kind: User
  name: dave # Name is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

## Azure RBAC with AAD:

### Pros:

- Centralized management: Azure RBAC enables centralized management of access control across various Azure resources, making it easier to manage permissions in a consistent manner.
- Familiarity: If you're already using Azure services, you might be familiar with Azure RBAC, making it easier to manage access control without learning Kubernetes RBAC.
- Granular control: Azure RBAC offers granular control over access to specific Azure resources, allowing you to define custom roles with fine-grained permissions.
- Audit and monitoring: Azure RBAC provides integration with Azure Monitor and Azure Activity Log for auditing and monitoring access control activities.

### Cons:

- Separate management from Kubernetes RBAC: You'll need to manage access control policies separately from Kubernetes RBAC, which can be cumbersome and may lead to inconsistencies.
- Limited to Azure resources: Azure RBAC is limited to managing access to Azure resources and does not extend to Kubernetes-specific resources.


## Kubernetes RBAC with AAD:

### Pros:

- Native to Kubernetes: Kubernetes RBAC is native to the Kubernetes ecosystem, providing a consistent and standardized way of managing access control across different Kubernetes clusters and platforms.
- Fine-grained control over Kubernetes resources: Kubernetes RBAC allows you to define roles and permissions specific to Kubernetes resources like Pods, Services, and ConfigMaps, offering fine-grained control over access.
- Extensibility: Kubernetes RBAC is extensible and can be integrated with custom resources or third-party applications that run on Kubernetes.

### Cons:

- Learning curve: If you're not familiar with Kubernetes RBAC, there may be a learning curve to understand and manage access control using this system.
- Separate management from Azure RBAC: Kubernetes RBAC requires separate management from Azure RBAC, which may lead to inconsistencies and duplication of efforts when managing access control policies.
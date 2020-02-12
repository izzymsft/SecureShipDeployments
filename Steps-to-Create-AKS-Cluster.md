
## Steps to Create the AKS Cluster

### Logout of Any Existing Session

```shell
az logout
```

### Login to your Azure Account

```shell
az login
```

### View the Subscriptions Linked to your Azure Account
```shell
az account list 
```

### Specify the Subscription Id You Want to Use

```shell
az account set --subscription abcdef1234
```

### See the Locations (Regions) Supported by this Subscription
```shell
az account list-locations
```

### Create a Service Principal (Skip Role Assignments for Later)
Please make a note of the Tenant Id, App ID (Client ID) and Password (Secret)
```shell
az ad sp create-for-rbac --name="http://SampleIdentity2020" --skip-assignment
```

### Assign the Contributor Role to the Service Principal
```shell
az role assignment create --assignee <client-id>  --role Contributor 
```

### Creating a Resource Group
```shell
az group create --name SampleGroup --location westus
```

### Create a Virtual Network and Subnet for the AKS Cluster
```shell
az network vnet create -g SampleGroup -n SampleVNet --address-prefix 10.0.0.0/16 --subnet-name AKS --subnet-prefix 10.0.0.0/17
```

### Create a Second Subnet for the Azure Application Gateway
```shell
az network vnet subnet create -g SampleGroup --vnet-name SampleVNet -n ApplicationGateway --address-prefixes 10.0.128.0/17
```

### Grab the Kubernetes version available for our desired region
```shell
az aks get-versions --location "West US" --query "orchestrators[].orchestratorVersion"
```

### Show all the Subnets in the VNet
```shell
az network vnet subnet list --resource-group SampleGroup --vnet-name SampleVNet -o json
```
### Create the AKS Cluster
```shell
az aks create --kubernetes-version 1.15.5 --resource-group SampleGroup --name SampleCluster --network-plugin azure --enable-addons monitoring --service-principal 26953cbf24a0 --client-secret 4a789cb70878 --node-count 3 --node-vm-size Standard_D4s_v3 --ssh-key-value ~/.ssh/data-workloads.pub --location "West US" --max-pods 75 --vnet-subnet-id /subscriptions/86d61f02-2fcf-4a37-bb6e-cc2c2db9f67d/resourceGroups/SampleGroup/providers/Microsoft.Network/virtualNetworks/VirtualNetworkSample/subnets/AKSubnet  --service-cidr 10.0.80.0/20 --dns-service-ip 10.0.80.2
```
### Install the Kube Control Client

```shell
az aks install cli
```

### Retrieve the Credentials
```shell
az aks get-credentials --resource-group SampleGroup --name SampleCluster 
```

### Set up Permissions for the Dashboard

```shell
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

### Open the Dashboard and Browse Cluster Resources
```shell
az aks browse --resource-group SampleGroup --name SampleCluster
```


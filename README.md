# Azure Cli をインストールする。
NOTE: https://docs.microsoft.com/ja-jp/cli/azure/install-azure-cli-linux?pivots=yum

~~~
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
~~~
~~~
echo -e "[azure-cli]
name=Azure CLI
baseurl=https://packages.microsoft.com/yumrepos/azure-cli
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc" | sudo tee /etc/yum.repos.d/azure-cli.repo
~~~
~~~
sudo yum install azure-cli
~~~


Azureにログインする。
~~~
az login
~~~

下記のメッセージに従い、Webブラウザでhttps://microsoft.com/deviceloginを表示し、キーを入力する。
~~~
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code H54AJQVYZ to authenticate.
~~~~

サブスクリプションを切り替える
~~~~
# 一覧表示
az account list --output table

# 切り替え
az account set --subscription "203a21ce-5bb9-4bcc-a15d-34ac3a4850d9"
~~~~



# kubectlをインストールする。
NOTE: https://docs.microsoft.com/ja-jp/azure/aks/tutorial-kubernetes-deploy-cluster#install-the-kubernetes-cli

~~~
sudo az aks install-cli
~~~


# AKS Clusterを作成する。
NOTE:
 https://docs.microsoft.com/ja-jp/azure/aks/private-clusters
 https://docs.microsoft.com/ja-jp/azure/aks/private-clusters#hub-and-spoke-with-custom-dns

~~~
az aks create \
    --resource-group newyear-aks \
    --name md-aks \
    --node-count 2 \
    --enable-addons monitoring \
    --enable-managed-identity \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id "/subscriptions/203a21ce-5bb9-4bcc-a15d-34ac3a4850d9/resourceGroups/newyear-aks/providers/Microsoft.Network/virtualNetworks/newyear-aks/subnets/aks-subnet" \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 192.168.1.10 \
    --service-cidr 192.168.1.0/24 \
    --generate-ssh-keys \
    --windows-admin-password P@ssw0rd12341234 \
    --windows-admin-username shinshin
~~~

下記の確認メッセージが表示される。内容を確認し、yを選ぶ。
~~~
SSH key files '/home/shinshin/.ssh/id_rsa' and '/home/shinshin/.ssh/id_rsa.pub' have been generated under ~/.ssh to allow SSH access to the VM. 
If using machines without permanent storage like Azure Cloud Shell without an attached file share, back up your keys to a safe location It is highly recommended to use USER assigned identity (option --assign-identity) when you want to bring your ownsubnet, which will have no latency for the role assignment to take effect. When using SYSTEM assigned identity, azure-cli will grant Network Contributor role to the system assigned identity after the cluster is created, and the role assignment will take some time to take effect, see https://docs.microsoft.com/en-us/azure/aks/use-managed-identity, proceed to create cluster with system assigned identity? (y/N): y
~~~

作成完了後に構成情報が出力される。
~~~
AAD role propagation done[############################################]  100.0000%{
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/203a21ce-5bb9-4bcc-a15d-34ac3a4850d9/resourcegroups/defaultresourcegroup-se/providers/microsoft.operationalinsights/workspaces/defaultworkspace-203a21ce-5bb9-4bcc-a15d-34ac3a4850d9-se"
      },
      "enabled": true,
      "identity": {
        "clientId": "315217f1-075d-4895-a695-3b4adb5eaba8",
        "objectId": "8ef4b14c-5c4d-4e7c-8b66-b3b720a904e6",
        "resourceId": "/subscriptions/203a21ce-5bb9-4bcc-a15d-34ac3a4850d9/resourcegroups/MC_newyear-aks_md-aks_koreacentral/providers/Microsoft.ManagedIdentity/userAssignedIdentities/omsagent-md-aks"
      }
    }
  },
  "agentPoolProfiles": [
    {
      "availabilityZones": null,
      "count": 2,
      "enableAutoScaling": null,
      "enableNodePublicIp": false,
      "maxCount": null,
      "maxPods": 30,
      "minCount": null,
      "mode": "System",
      "name": "nodepool1",
      "nodeImageVersion": "AKSUbuntu-1804-2020.12.15",
      "nodeLabels": {},
      "nodeTaints": null,
      "orchestratorVersion": "1.18.10",
      "osDiskSizeGb": 128,
      "osDiskType": "Managed",
      "osType": "Linux",
      "powerState": {
        "code": "Running"
      },
      "provisioningState": "Succeeded",
      "proximityPlacementGroupId": null,
      "scaleSetEvictionPolicy": null,
      "scaleSetPriority": null,
      "spotMaxPrice": null,
      "tags": null,
      "type": "VirtualMachineScaleSets",
      "upgradeSettings": null,
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": "/subscriptions/203a21ce-5bb9-4bcc-a15d-34ac3a4850d9/resourceGroups/newyear-aks/providers/Microsoft.Network/virtualNetworks/newyear-aks/subnets/aks-subnet"
    }
  ],
  "apiServerAccessProfile": {
    "authorizedIpRanges": null,
    "enablePrivateCluster": true,
    "privateDNSZone": "system"
  },
  "autoScalerProfile": null,
  "diskEncryptionSetId": null,
  "dnsPrefix": "md-aks-newyear-aks-203a21",
  "enablePodSecurityPolicy": null,
  "enableRbac": true,
  "fqdn": null,
  "id": "/subscriptions/203a21ce-5bb9-4bcc-a15d-34ac3a4850d9/resourcegroups/newyear-aks/providers/Microsoft.ContainerService/managedClusters/md-aks",
  "identity": {
    "principalId": "5034eb6a-97fb-4d96-8e2f-2d75a2e898a0",
    "tenantId": "b36fe5e6-3c3d-44a1-b607-7b9a862ff87d",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "bc0ceae8-df50-4c44-b232-985a9a2de672",
      "objectId": "04ca6339-a550-4ecd-8a20-718a99cee167",
      "resourceId": "/subscriptions/203a21ce-5bb9-4bcc-a15d-34ac3a4850d9/resourcegroups/MC_newyear-aks_md-aks_koreacentral/providers/Microsoft.ManagedIdentity/userAssignedIdentities/md-aks-agentpool"
    }
  },
  "kubernetesVersion": "1.18.10",
  "linuxProfile": {
    "adminUsername": "azureuser",
    "ssh": {
      "publicKeys": [
        {
          "keyData": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCzTAMPsglntnx5MLuxkwiiM8ymGFJ6nSnEb6grTES6u133KW2xQ1/H0ImooIgSA9J2tqsax08pjicd0wLI3NrnJHhZUmnd/X0VH9zwnvkoG1mkq7NGKTbeiEupPgsNy8YvVPHrBrtAfNU6rS4l5qC5ToTauO4GOMi35YDqR5Cd9TFh7l3sj9S0D9pEW9D2Z3n1vqi9d1FAisOf9sH7+i4pTjUXUuqZDy7tf+P6lSk4zX+YZqGaQqeypF0PMbIf5bY9hYYPTrrdveR89uW3mvRZQPc6W7GCgtSA0pipUxJrjRzX6EzJclX+ZJomEFeTYpihO74iD5PSFTm7B3xHZBIV"
        }
      ]
    }
  },
  "location": "koreacentral",
  "maxAgentPools": 10,
  "name": "md-aks",
  "networkProfile": {
    "dnsServiceIp": "192.168.1.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerProfile": {
      "allocatedOutboundPorts": null,
      "effectiveOutboundIps": [
        {
          "id": "/subscriptions/203a21ce-5bb9-4bcc-a15d-34ac3a4850d9/resourceGroups/MC_newyear-aks_md-aks_koreacentral/providers/Microsoft.Network/publicIPAddresses/c62b2af7-fa6c-4d02-be95-d61f39aee703",
          "resourceGroup": "MC_newyear-aks_md-aks_koreacentral"
        }
      ],
      "idleTimeoutInMinutes": null,
      "managedOutboundIps": {
        "count": 1
      },
      "outboundIpPrefixes": null,
      "outboundIps": null
    },
    "loadBalancerSku": "Standard",
    "networkMode": null,
    "networkPlugin": "azure",
    "networkPolicy": null,
    "outboundType": "loadBalancer",
    "podCidr": null,
    "serviceCidr": "192.168.1.0/24"
  },
  "nodeResourceGroup": "MC_newyear-aks_md-aks_koreacentral",
  "powerState": {
    "code": "Running"
  },
  "privateFqdn": "md-aks-newyear-aks-203a21-7811b382.b18e5f22-763a-4224-b597-e8ef257dfb92.privatelink.koreacentral.azmk8s.io",
  "provisioningState": "Succeeded",
  "resourceGroup": "newyear-aks",
  "servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  },
  "sku": {
    "name": "Basic",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": {
    "adminPassword": null,
    "adminUsername": "shinshin",
    "licenseType": null
  }
}
~~~~


# cluster作成後に生成されるリソース群
命名規則: MC_＜cluster resource group name＞_＜cluster name＞_＜cluster region＞


作成されるリソース一覧を確認
~~~
az resource list --resource-group MC_newyear-aks_md-aks_koreacentral --output table
~~~
結果
~~~
Name                                                                                                        ResourceGroup                       Location      Type                                                   Status
----------------------------------------------------------------------------------------------------------  ----------------------------------  ------------  -----------------------------------------------------  --------
aks-nodepool1-23689242-vmss                                                                                 MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Compute/virtualMachineScaleSets
md-aks-agentpool                                                                                            MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.ManagedIdentity/userAssignedIdentities
omsagent-md-aks                                                                                             MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.ManagedIdentity/userAssignedIdentities
kubernetes                                                                                                  MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/loadBalancers
kube-apiserver.nic.2c8b1083-7a06-48f7-8ca8-547891347657                                                     MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/networkInterfaces
aks-agentpool-23689242-nsg                                                                                  MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/networkSecurityGroups
b18e5f22-763a-4224-b597-e8ef257dfb92.privatelink.koreacentral.azmk8s.io                                     MC_newyear-aks_md-aks_koreacentral  global        Microsoft.Network/privateDnsZones
b18e5f22-763a-4224-b597-e8ef257dfb92.privatelink.koreacentral.azmk8s.io/md-aks-newyear-aks-203a21-7811b382  MC_newyear-aks_md-aks_koreacentral  global        Microsoft.Network/privateDnsZones/virtualNetworkLinks
kube-apiserver                                                                                              MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/privateEndpoints
c62b2af7-fa6c-4d02-be95-d61f39aee703                                                                        MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/publicIPAddresses
~~~

# ノードプールを追加する。

~~~
az aks nodepool add \
    --resource-group newyear-aks \
     --cluster-name md-aks \
    --os-type Windows \
    --name npwin \
    --node-vm-size Standard_D3_v2 \
    --node-count 2
~~~

MCリソースグループに追加したノードプールのvmssが表示されることを確認する。
~~~
Name                                                                                                        ResourceGroup                       Location      Type                                                   Status
----------------------------------------------------------------------------------------------------------  ----------------------------------  ------------  -----------------------------------------------------  --------
aks-nodepool1-23689242-vmss                                                                                 MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Compute/virtualMachineScaleSets
aksnpwin                                                                                                    MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Compute/virtualMachineScaleSets
md-aks-agentpool                                                                                            MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.ManagedIdentity/userAssignedIdentities
omsagent-md-aks                                                                                             MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.ManagedIdentity/userAssignedIdentities
kubernetes                                                                                                  MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/loadBalancers
kube-apiserver.nic.2c8b1083-7a06-48f7-8ca8-547891347657                                                     MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/networkInterfaces
aks-agentpool-23689242-nsg                                                                                  MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/networkSecurityGroups
b18e5f22-763a-4224-b597-e8ef257dfb92.privatelink.koreacentral.azmk8s.io                                     MC_newyear-aks_md-aks_koreacentral  global        Microsoft.Network/privateDnsZones
b18e5f22-763a-4224-b597-e8ef257dfb92.privatelink.koreacentral.azmk8s.io/md-aks-newyear-aks-203a21-7811b382  MC_newyear-aks_md-aks_koreacentral  global        Microsoft.Network/privateDnsZones/virtualNetworkLinks
kube-apiserver                                                                                              MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/privateEndpoints
c62b2af7-fa6c-4d02-be95-d61f39aee703                                                                        MC_newyear-aks_md-aks_koreacentral  koreacentral  Microsoft.Network/publicIPAddresses
~~~~

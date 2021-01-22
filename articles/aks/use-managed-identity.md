---
title: 在 Azure Kubernetes Service 中使用受控識別
description: '瞭解如何在 Azure Kubernetes Service (AKS 中使用受控識別) '
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: e991f7313bae5aa67478043b4f9306dbc274e1e7
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659983"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes Service 中使用受控識別

目前，Azure Kubernetes Service (AKS) 叢集 (明確地說，Kubernetes 雲端提供者) 需要身分識別，才能在 Azure 中建立額外的資源，例如負載平衡器和受控磁片。 此身分識別可以是 *受控識別* 或 *服務主體*。 如果您使用 [服務主體](kubernetes-service-principal.md)，您必須提供一個或 AKS 代表您建立一個。 如果您使用受控識別，則會自動 AKS 為您建立。 使用服務主體的叢集最後會達到必須更新服務主體以保持叢集運作的狀態。 管理服務主體會增加複雜度，因此更容易使用受控識別。 相同的許可權需求適用于服務主體和受控識別。

*受控* 識別本質上是服務主體的包裝函式，讓其管理更為簡單。 MI 的認證輪替會根據 Azure Active Directory 預設，每隔46天自動進行。 AKS 會使用系統指派的和使用者指派的受控識別類型。 這些身分識別目前是不可變的。 若要深入瞭解，請參閱 [適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。

## <a name="before-you-begin"></a>開始之前

您必須安裝下列資源：

- Azure CLI 版本2.15.1 或更新版本

## <a name="limitations"></a>限制

* 不支援租使用者移動/遷移受控識別啟用的叢集。
* 如果叢集已 `aad-pod-identity` 啟用，Node-Managed 身分識別 (NMI) pod 會修改節點的 iptables 來攔截對 Azure 實例中繼資料端點的呼叫。 這項設定表示即使 pod 不使用，對中繼資料端點所提出的任何要求都會被 NMI 攔截 `aad-pod-identity` 。 您可以設定 AzurePodIdentityException .CRD，以通知 `aad-pod-identity` 來自符合 .crd 中所定義標籤之中繼資料端點的任何要求，都應該是 proxy，而不需要在 NMI 中處理。 在 `kubernetes.azure.com/managedby: aks` _kube_ 系統命名空間中具有標籤的系統 pod，應設定 `aad-pod-identity` AzurePodIdentityException .crd 來排除。 如需詳細資訊，請參閱 [停用 aad-pod-特定 pod 或應用程式](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)的身分識別。
  若要設定例外狀況，請安裝 [mic 例外狀況 YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)。

## <a name="summary-of-managed-identities"></a>受控識別的摘要

AKS 針對內建服務和附加元件使用了數個受控識別。

| 身分識別                       | Name    | 使用案例 | 預設許可權 | 攜帶您自己的身分識別
|----------------------------|-----------|----------|
| 控制平面 | 看不到 | 由 AKS 控制平面元件用來管理叢集資源，包括輸入負載平衡器、AKS 受控公用 Ip 和叢集自動調整程式作業 | 節點資源群組的參與者角色 | 支援
| Kubelet | AKS 叢集名稱-agentpool | 使用 Azure Container Registry (ACR) 進行驗證 | NA 適用于 kubernetes v 1.15 +) 的 ( | 目前不支援
| 附加元件 | AzureNPM | 不需要身分識別 | NA | No
| 附加元件 | AzureCNI 網路監視 | 不需要身分識別 | NA | No
| 附加元件 | azure-原則 (閘道管理員)  | 不需要身分識別 | NA | No
| 附加元件 | azure-原則 | 不需要身分識別 | NA | No
| 附加元件 | Calico | 不需要身分識別 | NA | No
| 附加元件 | 儀表板 | 不需要身分識別 | NA | No
| 附加元件 | HTTPApplicationRouting | 管理必要的網路資源 | 節點資源群組的讀取者角色，DNS 區域的參與者角色 | No
| 附加元件 | 輸入應用程式閘道 | 管理必要的網路資源| 節點資源群組的參與者角色 | No
| 附加元件 | omsagent | 用來將 AKS 計量傳送給 Azure 監視器 | 監視計量發行者角色 | No
| 附加元件 | Virtual-Node (ACIConnector)  | 管理 Azure 容器實例 (ACI) 所需的網路資源 | 節點資源群組的參與者角色 | No
| OSS 專案 | aad-pod-身分識別 | 可讓應用程式使用 Azure Active Directory (AAD 來安全地存取雲端資源)  | NA | 授與許可權的步驟 https://github.com/Azure/aad-pod-identity#role-assignment 。

## <a name="create-an-aks-cluster-with-managed-identities"></a>使用受控識別建立 AKS 叢集

您現在可以使用下列 CLI 命令，建立具有受控識別的 AKS 叢集。

首先，建立 Azure 資源群組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然後，建立 AKS 叢集：

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

使用受控識別成功建立叢集時，會包含此服務主體設定檔資訊：

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

使用下列命令來查詢控制平面受控識別的 objectid：

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

結果看起來應該像這樣：

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

一旦建立叢集之後，您就可以將應用程式工作負載部署到新的叢集，並與服務主體型 AKS 叢集一樣地與其互動。

> [!NOTE]
> 若要建立及使用您自己的 VNet、靜態 IP 位址，或連結到背景工作節點資源群組外部資源的 Azure 磁片，請使用叢集系統指派的受控識別 PrincipalID 來執行角色指派。 如需角色指派的詳細資訊，請參閱 [將存取權委派給其他 Azure 資源](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。
>
> 授與 Azure 雲端提供者所使用叢集受控識別的許可權，可能需要60分鐘的時間才能填入。

最後，取得存取叢集的認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>將 AKS 叢集更新為受控識別 (預覽版) 

您現在可以使用下列 CLI 命令，更新目前正在使用服務主體的 AKS 叢集，以使用受控識別。

首先，為系統指派的身分識別註冊功能旗標：

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

更新系統指派的身分識別：

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

註冊使用者指派之身分識別的功能旗標：

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

更新使用者指派的身分識別：

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> 當系統指派或使用者指派的身分識別更新為受控識別之後，請 `az aks nodepool upgrade --node-image-only` 在您的節點上執行，以完成受控識別的更新。

## <a name="bring-your-own-control-plane-mi"></a>攜帶您自己的控制平面 MI
自訂控制平面身分識別可讓您在建立叢集之前，將存取權授與現有的身分識別。 這項功能可讓您使用自訂 VNET 或 UDR outboundType 搭配預先建立的受控識別等案例。

您必須安裝 Azure CLI 2.15.1 版或更新版本。

### <a name="limitations"></a>限制
* 目前不支援 Azure Government。
* 目前不支援 Azure 中國的世紀。

如果您還沒有受控識別，您應該繼續建立一個範例，例如使用 [az IDENTITY CLI][az-identity-create]。

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
結果看起來應該像這樣：

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

如果您的受控識別是訂用帳戶的一部分，您可以使用 [az IDENTITY CLI 命令][az-identity-list] 來進行查詢。  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

現在您可以使用下列命令，以現有的身分識別建立您的叢集：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

使用您自己的受控識別成功建立叢集時，會包含此 userAssignedIdentities 設定檔資訊：

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>下一步
* 使用 [Azure Resource Manager (ARM) 範本 ][aks-arm-template] 來建立受控識別啟用的叢集。

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true

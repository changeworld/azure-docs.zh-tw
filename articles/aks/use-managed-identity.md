---
title: 在 Azure Kubernetes Service 中使用受控識別
description: '瞭解如何在 Azure Kubernetes Service (AKS 中使用受控識別) '
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 8c5c4a6e5d8b2997d80c7263ba17a705d3846ed8
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987387"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes Service 中使用受控識別

目前，Azure Kubernetes Service (AKS) 叢集 (明確地說，Kubernetes 雲端提供者) 需要身分識別，才能在 Azure 中建立額外的資源，例如負載平衡器和受控磁片。 這個識別可以是*受控識別*或*服務主體*。 如果您使用[服務主體](kubernetes-service-principal.md)，則必須提供一個或 AKS 代表您建立一個。 如果您使用受控識別，則會自動 AKS 來為您建立。 使用服務主體的叢集最終會到達必須更新服務主體的狀態，才能讓叢集正常運作。 管理服務主體會增加複雜性，這也是為什麼較容易使用受控識別的原因。 相同的許可權需求同時適用于服務主體和受控識別。

*受控*識別基本上是服務主體的包裝函式，讓其管理變得更簡單。 MI 的認證輪替會根據 Azure Active Directory 預設值，每隔46天自動執行一次。 AKS 會同時使用系統指派和使用者指派的受控識別類型。 這些身分識別目前是不可變的。 若要深入瞭解，請參閱[Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。

## <a name="before-you-begin"></a>開始之前

您必須安裝下列資源：

- Azure CLI，2.8.0 或更新版本

## <a name="limitations"></a>限制

* 只有在叢集建立期間，才可以啟用具有受控識別的 AKS 叢集。
* 現有的 AKS 叢集無法遷移至受控識別。
* 在叢集**升級**作業期間，受控識別暫時無法使用。
* 不支援租使用者移動/遷移已啟用受控識別的叢集。

## <a name="summary-of-managed-identities"></a>受控識別的摘要

AKS 會針對內建服務和附加元件使用數個受控識別。

| 身分識別                       | 名稱    | 使用案例 | 預設許可權 | 攜帶您自己的身分識別
|----------------------------|-----------|----------|
| 控制平面 | 不可見 | 由 AKS 用於受控網路資源，包括輸入負載平衡器和 AKS 受控公用 Ip | 節點資源群組的參與者角色 | 預覽
| Kubelet | AKS 叢集名稱-agentpool | 使用 Azure Container Registry (ACR 進行驗證)  | 節點資源群組的讀取者角色 | 目前不支援
| 附加元件 | AzureNPM | 不需要身分識別 | NA | 否
| 附加元件 | AzureCNI 網路監視 | 不需要身分識別 | NA | 否
| 附加元件 | azurepolicy (閘道管理員)  | 不需要身分識別 | NA | 否
| 附加元件 | azurepolicy | 不需要身分識別 | NA | 否
| 附加元件 | Calico | 不需要身分識別 | NA | 否
| 附加元件 | 儀表板 | 不需要身分識別 | NA | 否
| 附加元件 | HTTPApplicationRouting | 管理所需的網路資源 | 節點資源群組的讀取者角色，DNS 區域的參與者角色 | 否
| 附加元件 | 輸入應用程式閘道 | 管理所需的網路資源| 節點資源群組的參與者角色 | 否
| 附加元件 | omsagent | 用來將 AKS 計量傳送至 Azure 監視器 | 監視計量發行者角色 | 否
| 附加元件 | 虛擬節點 (ACIConnector)  | 管理 (ACI) 的 Azure 容器實例所需的網路資源 | 節點資源群組的參與者角色 | 否


## <a name="create-an-aks-cluster-with-managed-identities"></a>建立具有受控識別的 AKS 叢集

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

使用受控識別成功建立叢集包含此服務主體設定檔資訊：

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

使用下列命令來查詢控制平面受控識別的 objectid：

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

結果應該如下所示：

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

建立叢集之後，您就可以將應用程式工作負載部署到新的叢集，並與其互動，就像您使用以服務主體為基礎的 AKS 叢集所做的一樣。

> [!NOTE]
> 若要建立及使用您自己的 VNet、靜態 IP 位址，或連結的 Azure 磁片（其中資源位於背景工作節點資源群組以外），請使用叢集系統指派的受控識別 PrincipalID 來執行角色指派。 如需角色指派的詳細資訊，請參閱[委派其他 Azure 資源的存取權](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。
>
> 授與 Azure 雲端提供者所使用叢集受控識別的許可權，可能需要60分鐘的時間來填入。

最後，取得用來存取叢集的認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>讓您自己的控制平面 MI (預覽) 
自訂控制項平面身分識別可讓您在建立叢集之前，將存取權授與現有的身分識別。 這可讓您使用自訂 VNET 或具有受控識別的 UDR outboundType 等案例。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

您必須先安裝下列資源：
- Azure CLI，2.9.0 版或更新版本
- Aks-preview 0.4.57 延伸模組

整合您自己的控制平面 MI (Preview) 的限制：
* 目前不支援 Azure Government。
* 目前不支援 Azure 中國的世紀。

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

如果您還沒有受控識別，您應該繼續建立一個範例，例如使用[az IDENTITY CLI][az-identity-create]。

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
結果應該如下所示：

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

如果您的受控識別屬於您的訂用帳戶，您可以使用[az IDENTITY CLI 命令][az-identity-list]來進行查詢。  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

現在您可以使用下列命令，以現有的身分識別來建立叢集：

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

使用您自己的受控識別建立成功的叢集，包含此 userAssignedIdentities 設定檔資訊：

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

## <a name="next-steps"></a>後續步驟
* 使用[Azure Resource Manager (ARM) 範本][aks-arm-template]來建立受控識別啟用的叢集。

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list

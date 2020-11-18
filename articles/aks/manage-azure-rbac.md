---
title: 從 Azure 管理 Kubernetes 中的 Azure RBAC
titleSuffix: Azure Kubernetes Service
description: 瞭解如何使用適用于 Kubernetes 授權的 Azure RBAC 搭配 Azure Kubernetes Service (AKS) 。
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a2a385b2be4e1005a7aabd76261b3190ecd2a506
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684214"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>使用適用於 Kubernetes 的 Azure RBAC 授權 (預覽)

現在您可以在 [Azure Active Directory (Azure AD) 和 AKS 之間運用整合式驗證](managed-aad.md)。 啟用時，這項整合可讓客戶使用 Azure AD 使用者、群組或服務主體作為 Kubernetes RBAC 的主旨，請參閱 [這裡](azure-ad-rbac.md)的詳細資訊。
這項功能可讓您不必針對 Kubernetes 個別管理使用者身分識別和認證。 不過，您仍然必須個別設定及管理 Azure RBAC 和 Kubernetes RBAC。 如需在 AKS 上使用 RBAC 進行驗證和授權的詳細資訊，請參閱 [這裡](concepts-identity.md)。

本檔涵蓋新的方法，可讓您跨 Azure 資源、AKS 和 Kubernetes 資源進行統一的管理和存取控制。

## <a name="before-you-begin"></a>開始之前

從 Azure 管理 Kubernetes 資源之 RBAC 的功能，可讓您選擇使用 Azure 或原生 Kubernetes 機制來管理叢集資源的 RBAC。 啟用時，Azure AD 主體會由 Azure RBAC 獨佔驗證，而一般 Kubernetes 使用者和服務帳戶則由 Kubernetes RBAC 獨佔驗證。 如需在 AKS 上使用 RBAC 進行驗證和授權的詳細資訊，請參閱 [這裡](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>先決條件 
- 確定您有 Azure CLI 2.9.0 版版或更新版本
- 確定已 `EnableAzureRBACPreview` 啟用功能旗標。
- 確定您已安裝 `aks-preview` [CLI 擴充][az-extension-add] 功能 v 0.4.55 或更高版本
- 確定您已安裝 [kubectl v 1.18.3 +][az-aks-install-cli]。

#### <a name="register-enableazurerbacpreview-preview-feature"></a>註冊 `EnableAzureRBACPreview` 預覽功能

若要建立使用 Azure RBAC 進行 Kubernetes 授權的 AKS 叢集，您必須 `EnableAzureRBACPreview` 在訂用帳戶上啟用功能旗標。

`EnableAzureRBACPreview`使用[az feature register][az-feature-register]命令註冊功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

準備好時，請使用 [az provider register] [az-provider-register] 命令重新整理 *>microsoft.containerservice* 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 延伸模組

若要建立使用 Azure RBAC 的 AKS 叢集，您需要0.4.55 或更高版本的 *AKS-preview* CLI 擴充功能。 使用 [az extension add][az-extension-add]命令安裝 *aks-preview* Azure CLI 擴充功能，或使用 [az extension update][az-extension-update]命令安裝任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

- 需要 [受控 Azure AD 整合](managed-aad.md)。
- 在預覽期間，您無法將適用于 Kubernetes 授權的 Azure RBAC 整合至現有的叢集，但您將可以在正式運作的 (GA) 正式推出。
- 使用 [kubectl v 1.18.3 +][az-aks-install-cli]。
- 如果您有 CRDs，而且正在進行自訂角色定義，則今天要介紹的 CRDs 唯一方法是提供 `Microsoft.ContainerService/managedClusters/*/read` 。 AKS 正在努力為 CRDs 提供更細微的許可權。 針對其餘的物件，您可以使用特定的 API 群組，例如： `Microsoft.ContainerService/apps/deployments/read` 。
- 新的角色指派最多可能需要5分鐘，才能傳播和更新授權伺服器。
- 要求設定要進行驗證的 Azure AD 租使用者與保留 AKS 叢集之訂用帳戶的租使用者相同。 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>使用 Azure RBAC 和受控 Azure AD 整合建立新的叢集

使用下列 CLI 命令來建立 AKS 叢集。

建立 Azure 資源群組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

使用受控 Azure AD 整合和適用于 Kubernetes 授權的 Azure RBAC 來建立 AKS 叢集。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

使用 Azure AD 整合和 Azure RBAC for Kubernetes 授權成功建立叢集時，會在回應主體中提供下列區段：

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>建立使用者的角色指派以存取叢集

AKS 提供下列四個內建角色：


| 角色                                | 描述  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC 檢視器  | 允許唯讀存取，以查看命名空間中的大部分物件。 它不允許查看角色或角色系結。 此角色不允許 `Secrets` 進行查看，因為讀取秘密的內容可讓您存取命名空間中的 ServiceAccount 認證，這會允許 API 存取做為命名空間中的任何 ServiceAccount (形式的許可權擴大)   |
| Azure Kubernetes Service RBAC 寫入器 | 允許對命名空間中大部分物件的讀取/寫入存取。 此角色不允許查看或修改角色或角色系結。 不過，此角色可讓您存取和執行 pod `Secrets` 作為命名空間中的任何 ServiceAccount，因此可以用來取得命名空間中任何 ServiceAccount 的 API 存取層級。 |
| Azure Kubernetes Service RBAC 管理員  | 允許系統管理員存取權，可在命名空間中授與。 允許對命名空間中大部分資源的讀取/寫入存取 (或叢集範圍) ，包括在命名空間內建立角色和角色系結的能力。 此角色不允許寫入資源配額或命名空間本身的存取權。 |
| Azure Kubernetes Service RBAC 叢集管理員  | 允許超級使用者存取在任何資源上執行任何動作。 它可讓您完整控制叢集中的每個資源，以及所有命名空間中的資源。 |


範圍為 **整個 AKS** 叢集的角色指派可以在 Azure 入口網站上叢集資源的存取控制 (IAM) 分頁，或使用 Azure CLI 命令來完成，如下所示：

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

其中 `<AAD-ENTITY-ID>` 可以是使用者名稱 (例如， user@contoso.com) 或甚至是服務主體的 ClientID。

您也可以建立範圍為叢集中特定 **命名空間** 的角色指派：

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

目前，必須透過 Azure CLI 設定命名空間範圍內的角色指派。


### <a name="create-custom-roles-definitions"></a>建立自訂角色定義

（選擇性）您可以選擇建立自己的角色定義，然後依上述方式指派。

以下是角色定義的範例，可讓使用者唯讀取部署，而不是其他任何專案。 您可以在 [此](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)查看可能動作的完整清單。


將下列 json 複製到名為的檔案中 `deploy-view.json` 。

```json
{
    "Name": "AKS Deployment Viewer",
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

以 `<YOUR SUBSCRIPTION ID>` 您的訂用帳戶中的識別碼取代，您可以藉由執行下列動作來取得：

```azurecli-interactive
az account show --query id -o tsv
```


現在，我們可以在您儲存的資料夾中執行下列命令，以建立角色定義 `deploy-view.json` ：

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

現在您已有角色定義，您可以執行下列動作，將它指派給使用者或其他身分識別：

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>搭配使用 Azure RBAC 與 Kubernetes 授權 `kubectl`

> [!NOTE]
> 藉由執行下列命令，確定您有最新的 kubectl：
>
> ```azurecli-interactive
> az aks install-cli
> ```
> 您可能需要使用許可權來執行它 `sudo` 。 

現在您已指派所需的角色和許可權。 您可以開始呼叫 Kubernetes API，例如，從 `kubectl` 。

基於這個目的，讓我們先使用下列命令來取得叢集的 kubeconfig：

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> 您將需要 [Azure Kubernetes Service Cluster 使用者](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) 內建角色，才能執行上述步驟。

您現在可以使用 kubectl，例如，列出叢集中的節點。 當您第一次執行時，您必須登入，後續的命令將會使用個別的存取權杖。

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>搭配使用 Azure RBAC 與 Kubernetes 授權 `kubelogin`

若要解除封鎖其他案例，例如非互動式登入、較舊的 `kubectl` 版本，或跨多個叢集利用 SSO，而不需要登入新的叢集，請將您的權杖視為有效，AKS 建立了一個名為的 exec 外掛程式 [`kubelogin`](https://github.com/Azure/kubelogin) 。

您可以藉由執行下列程式來使用它：

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

第一次，您必須以互動方式使用一般 kubectl 登入，但之後您將不再需要，即使是新的 Azure AD 叢集 (，只要您的權杖仍然有效) 。

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>清理

### <a name="clean-role-assignment"></a>清除角色指派

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
從您執行的所有指派複製識別碼或識別碼，然後再複製。

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>清除角色定義

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>刪除叢集和資源群組

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>後續步驟

- 請 [在這裡](concepts-identity.md)閱讀更多有關 AKS Authentication、Authorization、Kubernetes Rbac 和 Azure RBAC 的資訊。
- [在此](../role-based-access-control/overview.md)閱讀更多有關 Azure RBAC 的資訊。
- 深入瞭解您可以用來在 [此](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)為 Kubernetes 授權定義自訂 Azure 角色的所有動作。


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true

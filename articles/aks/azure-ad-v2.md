---
title: 在 Azure 庫伯奈斯服務中使用 Azure AD
description: 瞭解如何在 Azure 庫伯奈斯服務 （AKS） 中使用 Azure AD
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 1ca4b70139ed5e0a136f6f5f2b0382b8c1688983
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389404"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>在 Azure 庫伯奈斯服務中集成 Azure AD（預覽版）

> [!Note]
> 具有 AD 集成的現有 AKS v1 群集不受新的 AKS v2 體驗的影響。

Azure AD 與 AKS v2 集成旨在簡化 Azure AD 與 AKS v1 體驗的集成，其中使用者需要創建用戶端應用、伺服器應用，並要求 Azure AD 租戶授予目錄讀取權限。 在新版本中，AKS 資來源提供者為您管理用戶端和伺服器應用。

## <a name="limitations"></a>限制

* 當前無法將啟用 Azure AD 的 AKS v1 群集升級到 v2 體驗。

> [!IMPORTANT]
> AKS 預覽功能提供自助服務、加入宣告功能。 預覽版提供"根據"和"可用"，並且不在服務等級協定和有限保修中。 客戶支援會盡力而為，部分涵蓋 AKS 預覽。 因此，這些功能不適合生產用途。 有關詳細資訊，請參閱以下支援文章：
>
> - [AKS 支援策略](support-policies.md)
> - [Azure 支援常見問題集](faq.md)

## <a name="before-you-begin"></a>開始之前

您必須安裝以下資源：

- Azure CLI，版本 2.2.0 或更高版本
- aks 預覽 0.4.38 擴展
- 庫布克特爾最低版本為[1.18 Beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

要安裝/更新 aks 預覽擴展或更高版本，請使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

要安裝 kubectl，請使用以下
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

[將這些說明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)用於其他作業系統。

> [!CAUTION]
> 在訂閱上註冊功能後，當前無法取消註冊該功能。 啟用某些預覽功能時，可能會將預設值用於訂閱中以後創建的所有 AKS 群集。 不要在生產訂閱上啟用預覽功能。 相反，請使用單獨的訂閱來測試預覽功能並收集回饋。

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

狀態可能需要幾分鐘才能顯示為 **"已註冊**"。 您可以使用[az 要素清單](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 az`Microsoft.ContainerService`提供程式寄存器命令刷新資來源提供者[的註冊](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>創建啟用 Azure AD 的 AKS 群集

現在，您可以使用以下 CLI 命令創建 AKS 群集。

首先，創建 Azure 資源組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

然後，創建 AKS 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
上述命令創建一個三個節點 AKS 群集，但預設情況下創建群集的使用者不是有權訪問此群集的組的成員。 此使用者需要創建 Azure AD 組，將自己添加為組的成員，然後按如下所示更新群集。 在此處[按照說明](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)操作

創建組並添加自己作為成員（和其他人）後，可以使用以下命令使用 Azure AD 組更新群集

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
或者，如果首先創建組並添加成員，則可以使用以下命令在創建時間啟用 Azure AD 組，

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

成功創建 Azure AD v2 群集在回應正文中具有以下部分
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

群集在幾分鐘內創建。

## <a name="access-an-azure-ad-enabled-cluster"></a>訪問啟用 Azure AD 的群集
要獲取訪問群集的管理員憑據，可以執行以下操作：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
現在使用 kubectl 獲取節點命令查看群集中的節點：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

要獲取訪問群集的使用者憑據，可以：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
按照說明登錄。

您收到：**您必須登錄到伺服器（未經授權）**

上述使用者會收到錯誤，因為使用者不是有權訪問群集的組的一部分。

## <a name="next-steps"></a>後續步驟

瞭解[Azure AD 角色存取控制][azure-ad-rbac]。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md

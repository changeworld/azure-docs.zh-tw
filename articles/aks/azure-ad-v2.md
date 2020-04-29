---
title: 在 Azure Kubernetes Service 中使用 Azure AD
description: 瞭解如何使用 Azure Kubernetes Service 中的 Azure AD （AKS）
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114652"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>整合 Azure Kubernetes Service 中的 Azure AD （預覽）

> [!Note]
> 具有 AD 整合的現有 AKS v1 叢集不會受到新 AKS v2 體驗的影響。

Azure AD 與 AKS v2 整合的設計，是為了簡化與 AKS v1 體驗的 Azure AD 整合，其中使用者需要建立用戶端應用程式、伺服器應用程式，並要求 Azure AD 租使用者授與目錄讀取權限。 在新版本中，AKS 資源提供者會為您管理用戶端和伺服器應用程式。

## <a name="limitations"></a>限制

* 您目前無法將現有 Azure AD 啟用的 AKS v1 叢集升級為 v2 體驗。

> [!IMPORTANT]
> AKS preview 功能可在自助服務上自行選擇。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需詳細資訊，請參閱下列支援文章：
>
> - [AKS 支援原則](support-policies.md)
> - [Azure 支援常見問題集](faq.md)

## <a name="before-you-begin"></a>開始之前

您必須安裝下列資源：

- Azure CLI，2.2.0 或更新版本
- Aks-preview 0.4.38 延伸模組
- 最低版本為[1.18 Beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)的 Kubectl

若要安裝/更新 aks-preview 擴充功能或更新版本，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

若要安裝 kubectl，請使用下列各項：

```azurecli
sudo az aks install-cli
kubectl version --client
```

請使用[這些指示](https://kubernetes.io/docs/tasks/tools/install-kubectl/)來進行其他作業系統。

> [!CAUTION]
> 在訂用帳戶上註冊功能之後，您目前無法取消註冊該功能。 當您啟用某些預覽功能時，預設值可能會用於在訂用帳戶中建立的所有 AKS 叢集。 請勿在生產訂用帳戶上啟用預覽功能。 相反地，請使用個別的訂用帳戶來測試預覽功能，並收集意見反應。

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為**已註冊**。 您可以使用[az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令來檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

當 [狀態] 顯示為 [已註冊] 時， `Microsoft.ContainerService`請使用[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)命令重新整理資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>建立已啟用 Azure AD 的 AKS 叢集

您現在可以使用下列 CLI 命令來建立 AKS 叢集。

首先，建立 Azure 資源群組：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

然後，建立 AKS 叢集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
上述命令會建立三個節點的 AKS 叢集，但建立叢集的使用者預設不是具有此叢集存取權之群組的成員。 此使用者必須建立 Azure AD 群組、將其本身新增為群組的成員，然後更新叢集，如下所示。 請遵循[這裡](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)的指示

建立群組並將自己（和其他）新增為成員之後，您可以使用下列命令，以 Azure AD 群組來更新叢集

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
或者，如果您第一次建立群組並新增成員，您可以在建立時使用下列命令來啟用 Azure AD 群組：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

成功建立 Azure AD v2 叢集在回應本文中會有下列區段
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

叢集會在幾分鐘內建立。

## <a name="access-an-azure-ad-enabled-cluster"></a>存取已啟用 Azure AD 的叢集
若要取得系統管理員認證以存取叢集：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
現在，請使用 kubectl get 節點命令來查看叢集中的節點：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

若要取得用來存取叢集的使用者認證：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
依照指示進行登入。

您會收到：**您必須登入伺服器（未經授權）**

上述使用者會收到錯誤，因為使用者不屬於可存取叢集的群組。

## <a name="next-steps"></a>後續步驟

瞭解[Azure AD 以角色為基礎的存取控制][azure-ad-rbac]。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md

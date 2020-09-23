---
title: '啟動和停止 Azure Kubernetes Service (AKS) '
description: 瞭解如何停止或啟動 Azure Kubernetes Service (AKS) 叢集。
services: container-service
ms.topic: article
ms.date: 09/18/2020
author: palma21
ms.openlocfilehash: a743a6c30d5ce8bcaf275bf1a658f8343de4d4fb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933995"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>停止並啟動 Azure Kubernetes Service (AKS) 叢集 (預覽) 

您的 AKS 工作負載可能不需要持續執行，例如只在上班時間使用的開發叢集。 這會導致您的 Azure Kubernetes Service (AKS) 叢集可能閒置，執行的時間不超過系統元件。 您可以將所有節點集區[調整 `User` 為 0](scale-cluster.md#scale-user-node-pools-to-0)，以降低叢集的使用量，但在叢集執行時，仍需要您的[ `System` 集](use-system-pools.md)區來執行系統元件。 若要在這段期間內進一步將成本優化，您可以完全關閉 (停止) 叢集。 此動作會完全停止您的控制平面和代理程式節點，讓您可以儲存所有計算成本，同時維持您在重新開機時儲存的所有物件和叢集狀態。 如此一來，您就可以在週末之後挑選，或只在執行 batch 作業時才執行您的叢集。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

### <a name="install-the-aks-preview-azure-cli"></a>安裝 `aks-preview` Azure CLI 

您也需要 *aks-preview* Azure CLI 擴充功能版本0.4.64 或更新版本。 使用[az extension add][az-extension-add]命令安裝*aks-preview* Azure CLI 擴充功能。 或使用 [az extension update][az-extension-update] 命令安裝任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

> [!WARNING]
> 已停止 AKS 叢集的叢集狀態最多會保留12個月。 如果您的叢集已停止超過12個月，則無法復原叢集狀態。 如需詳細資訊，請參閱 [AKS 支援原則](support-policies.md)。
> 您只能啟動或刪除已停止的 AKS 叢集。 若要執行任何作業（例如調整規模或升級），請先啟動您的叢集。


### <a name="register-the-startstoppreview-preview-feature"></a>註冊 `StartStopPreview` 預覽功能

若要使用 [啟動/停止叢集] 功能，您必須 `StartStopPreview` 在訂用帳戶上啟用功能旗標。

`StartStopPreview`使用[az feature register][az-feature-register]命令註冊功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

狀態需要幾分鐘的時間才會顯示「已註冊」**。 使用 [az feature list][az-feature-list] 命令來確認註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

當您準備好時，請使用[az provider register][az-provider-register]命令重新整理 *>microsoft.containerservice*資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-cluster"></a>停止 AKS 叢集

您可以使用 `az aks stop` 命令來停止正在執行的 AKS 叢集節點和控制平面。 下列範例會停止名為 *myAKSCluster*的叢集：

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

您可以使用 [az aks show] [az-aks-show] 命令來確認何時停止叢集，並確認 `powerState` 顯示如下 `Stopped` 輸出：

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

如果 `provisioningState` 顯示 `Stopping` ，表示您的叢集尚未完全停止。

> [!IMPORTANT]
> 如果您使用 [Pod 中斷預算](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) ，則停止作業可能需要較長的時間，因為清空程式需要更多時間才能完成。


## <a name="start-an-aks-cluster"></a>啟動 AKS 叢集

您可以使用 `az aks start` 命令來啟動已停止的 AKS 叢集節點和控制平面。 重新開機叢集時，會使用先前的控制平面狀態和代理程式節點的數目。  
下列範例會啟動名為 *myAKSCluster*的叢集：

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

您可以使用 [az aks show] [az-aks-show] 命令來確認您的叢集是否已啟動，並確認 `powerState` 顯示 `Running` 如下輸出：

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

如果 `provisioningState` 顯示 `Starting` ，表示您的叢集尚未完全啟動。


## <a name="next-steps"></a>下一步

- 若要瞭解如何將集區調整 `User` 為0，請參閱將集區 [調整 `User` 為 0](scale-cluster.md#scale-user-node-pools-to-0)。
- 若要瞭解如何使用「找不到」實例來節省成本，請參閱 [將點節點集區新增至 AKS](spot-node-pool.md)。
- 若要深入瞭解 AKS 支援原則，請參閱 [AKS 支援原則](support-policies.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
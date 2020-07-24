---
title: 使用鄰近放置群組來減少 Azure Kubernetes Service （AKS）叢集的延遲
description: 瞭解如何使用鄰近放置群組來減少 AKS 叢集工作負載的延遲。
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/10/2020
author: jluk
ms.openlocfilehash: f6cb370d258a79420b03baf17ec964b091cdebb7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056585"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>使用鄰近放置群組來減少延遲（預覽）

> [!Note]
> 在 AKS 上使用鄰近放置群組時，共置僅適用于代理程式節點。 節點對節點和對應的主控 pod 至 pod 的延遲已獲得改善。 共置不會影響叢集控制平面的位置。

在 Azure 中部署您的應用程式時，將虛擬機器（VM）實例分散到不同區域或可用性區域會建立網路延遲，這可能會影響應用程式的整體效能。 鄰近放置群組是一種邏輯群組，用來確保 Azure 計算資源實際位於彼此接近的位置。 某些應用程式（例如遊戲、工程模擬和高頻率交易（HFT））需要快速完成的低延遲和工作。 針對高效能運算（HPC）案例，例如，請考慮針對您的叢集節點集區使用[鄰近放置群組](../virtual-machines/linux/co-location.md#proximity-placement-groups)（PPG）。

## <a name="limitations"></a>限制

* 近接位置群組最多可以對應到一個可用性區域。
* 節點集區必須使用虛擬機器擴展集來關聯鄰近放置群組。
* 節點集區只能在節點集區建立時間建立鄰近放置群組的關聯。

> [!IMPORTANT]
> AKS preview 功能可在自助服務上自行選擇。 預覽會以「原樣」和「可用」的方式提供，並從服務等級協定中排除，並享有有限擔保。 AKS 預覽的部分是由客戶支援，以最大的方式來涵蓋。 因此，這些功能並不適用于生產環境使用。 如需詳細資訊，請參閱下列支援文章：
>
> - [AKS 支援原則](support-policies.md)
> - [Azure 支援常見問題集](faq.md)

## <a name="before-you-begin"></a>開始之前

您必須先安裝下列資源：

- Aks-preview 0.4.53 延伸模組

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>設定鄰近放置群組的預覽功能

> [!IMPORTANT]
> 使用 AKS 節點集區的鄰近放置群組時，共置僅適用于代理程式節點。 節點對節點和對應的主控 pod 至 pod 的延遲已獲得改善。 共置不會影響叢集控制平面的位置。

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

註冊可能需要幾分鐘的時間。 使用下列命令來確認已註冊功能：

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

在預覽期間，您需要*aks-preview* CLI 擴充功能，才能使用鄰近放置群組。 使用[az extension add][az-extension-add]命令，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="node-pools-and-proximity-placement-groups"></a>節點集區和鄰近放置群組

您使用鄰近放置群組部署的第一個資源會附加至特定的資料中心。 使用相同的鄰近放置群組部署的其他資源，會共置於相同的資料中心。 當使用鄰近放置群組的所有資源都已停止（解除配置）或刪除之後，就不再附加。

* 許多節點集區可以與單一的鄰近放置群組建立關聯。
* 節點集區只能與單一鄰近放置群組相關聯。

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>設定具有可用性區域的鄰近放置群組

> [!NOTE]
> 雖然鄰近放置群組需要節點集區使用最多一個可用性區域，但[99.9% 的基準 AZURE VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)仍會在單一區域中的 vm 上生效。

鄰近放置群組是節點集區概念，並與每個個別節點集區相關聯。 使用 PPG 資源不會影響 AKS 控制平面的可用性。 這可能會影響叢集設計區域的方式。 若要確保叢集會散佈到多個區域，建議使用下列設計。

* 使用3個區域的第一個系統集區布建叢集，且沒有相關聯的鄰近放置群組。 這可確保系統 pod 落在專用節點集區中，並分散到多個區域。
* 使用與每個集區相關聯的唯一區域和鄰近位置群組，來新增額外的使用者節點集區。 其中一個範例是區域1和 PPG1 中的 nodepool1、區域2中的 nodepool2 和 PPG2，nodepool3 在區域3中有 PPG3。 這可確保在叢集層級中，節點會分散到多個區域，而每個個別節點集區會在具有專用 PPG 資源的指定區域中共存。

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>使用鄰近放置群組建立新的 AKS 叢集

下列範例會使用[az group create][az-group-create]命令，在*centralus*區域中建立名為*myResourceGroup*的資源群組。 然後使用[az AKS create][az-aks-create]命令來建立名為*myAKSCluster*的 AKS 叢集。

加速網路可大幅提升虛擬機器的網路效能。 在理想的情況下，請將鄰近放置群組與加速網路搭配使用。 根據預設，AKS 會在支援的[虛擬機器實例](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)上使用加速網路，其中包括具有兩個或多個個 vcpu 的大部分 Azure 虛擬機器。

使用與第一個系統節點集區相關聯的鄰近放置群組，建立新的 AKS 叢集：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
執行下列命令，並儲存傳回的識別碼：

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

命令會產生輸出，其中包括即將推出的 CLI 命令所需的*識別碼*值：

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

在下列命令中，使用*myPPGResourceID*值的鄰近放置群組資源識別碼：

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>將鄰近放置群組新增至現有的叢集

您可以藉由建立新的節點集區，將鄰近放置群組新增至現有的叢集。 接著，您可以選擇性地將現有的工作負載遷移至新的節點集區，然後刪除原始的節點集區。

使用您稍早建立的相同鄰近放置群組，這可確保 AKS 叢集中兩個節點集區中的代理程式節點實際上位於相同的資料中心。

使用您稍早建立的鄰近放置群組中的資源識別碼，然後使用命令新增節點集區 [`az aks nodepool add`][az-aks-nodepool-add] ：

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>清理

若要刪除叢集，請使用 [`az group delete`][az-group-delete] 命令來刪除 AKS 資源群組：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解[鄰近放置群組][proximity-placement-groups]。

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete

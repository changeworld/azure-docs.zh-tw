---
title: 使用鄰近位置群組來降低 Azure Kubernetes Service (AKS) 叢集的延遲
description: 瞭解如何使用鄰近放置群組來減少 AKS 叢集工作負載的延遲。
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/10/2020
author: jluk
ms.openlocfilehash: 5b3dc3803cfb89f4a74d082b5913e69df1d03a00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87986707"
---
# <a name="reduce-latency-with-proximity-placement-groups-preview"></a>使用鄰近位置群組 (預覽版來降低延遲) 

> [!Note]
> 在 AKS 上使用鄰近位置群組時，共置只會套用至代理程式節點。 節點對節點和對應的託管 pod 至 pod 延遲已獲得改善。 共置不會影響叢集控制平面的放置。

在 Azure 中部署您的應用程式時，跨區域或可用性區域將虛擬機器 (VM) 實例分散會建立網路延遲，這可能會影響應用程式的整體效能。 近接位置群組是用來確保 Azure 計算資源實際位於彼此附近的邏輯群組。 某些應用程式（像是遊戲、工程模擬和高頻率交易 (HFT) 需要低延遲和快速完成的工作。 針對 (HPC) 案例的高效能運算，請考慮針對叢集的節點集區使用 [鄰近放置群組](../virtual-machines/linux/co-location.md#proximity-placement-groups) (PPG) 。

## <a name="limitations"></a>限制

* 近接位置群組最多可以對應到一個可用性區域。
* 節點集區必須使用虛擬機器擴展集來建立鄰近放置群組的關聯。
* 節點集區只能在節點集區建立時建立鄰近位置群組的關聯。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始之前

您必須先安裝下列資源：

- Aks-preview 0.4.53 延伸模組

### <a name="set-up-the-preview-feature-for-proximity-placement-groups"></a>設定鄰近放置群組的預覽功能

> [!IMPORTANT]
> 使用具有 AKS 節點集區的近接位置群組時，共置只會套用至代理程式節點。 節點對節點和對應的託管 pod 至 pod 延遲已獲得改善。 共置不會影響叢集控制平面的放置。

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "ProximityPlacementGroupPreview"
```

註冊可能需要幾分鐘的時間。 使用下列命令來確認已註冊功能：

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/ProximityPlacementGroupPreview')].{Name:name,State:properties.state}"
```

在預覽期間，您需要 *aks-preview* CLI 延伸模組，才能使用鄰近放置群組。 使用 [az extension add][az-extension-add] 命令，然後使用 [az extension update][az-extension-update] 命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="node-pools-and-proximity-placement-groups"></a>節點集區和鄰近位置群組

您使用鄰近位置群組部署的第一個資源會附加至特定資料中心。 使用相同鄰近放置群組部署的其他資源會在相同的資料中心內共存。 一旦使用鄰近放置群組的所有資源都已停止 (取消配置) 或刪除，就不會再附加。

* 許多節點集區都可以與單一鄰近放置群組相關聯。
* 節點集區只能與單一鄰近放置群組相關聯。

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>使用可用性區域設定鄰近位置群組

> [!NOTE]
> 雖然近接位置群組需要節點集區才能使用最多一個可用性區域，但在單一區域中的 Vm 的 [基準 AZURE VM SLA （99.9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) ）仍會有效。

鄰近位置群組是節點集區概念，並與每個個別節點集區相關聯。 使用 PPG 資源並不會影響 AKS 控制平面的可用性。 這可能會影響如何使用區域來設計叢集。 為了確保叢集分散到多個區域，建議使用下列設計。

* 使用3個區域布建具有第一個系統集區的叢集，且沒有相關聯的鄰近位置群組。 這可確保系統 pod 會進入專用節點集區，以分散到多個區域。
* 使用與每個集區相關聯的唯一區域和鄰近位置群組，新增額外的使用者節點集區。 例如，區域1和 PPG1 中的 nodepool1、區域2中的 nodepool2、區域2中的，以及區域3中使用 NODEPOOL3 的 PPG2、PPG3。 這可確保在叢集層級，節點會分散到多個區域，而每個個別節點集區會在具有專用 PPG 資源的指定區域中共存。

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>使用鄰近位置群組建立新的 AKS 叢集

下列範例會使用[az group create][az-group-create]命令，在*centralus*區域中建立名為*myResourceGroup*的資源群組。 接著會使用[az AKS create][az-aks-create]命令來建立名為*myAKSCluster*的 AKS 叢集。

加速網路可大幅提升虛擬機器的網路效能。 在理想的情況下，請使用鄰近放置群組搭配加速網路。 根據預設，AKS 會在支援的 [虛擬機器實例](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)上使用加速網路，其中包含具有兩個或多個個 vcpu 的大部分 Azure 虛擬機器。

使用與第一個系統節點集區相關聯的鄰近位置群組來建立新的 AKS 叢集：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
執行下列命令，並儲存傳回的識別碼：

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

此命令會產生輸出，其中包含即將推出的 CLI 命令所需的 *識別碼* 值：

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

在下列命令中，使用 *myPPGResourceID* 值的鄰近位置群組資源識別碼：

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>將鄰近位置群組新增至現有的叢集

您可以藉由建立新的節點集區，將鄰近放置群組新增至現有的叢集。 然後，您可以選擇性地將現有的工作負載遷移至新的節點集區，然後刪除原始節點集區。

使用您稍早建立的相同鄰近位置群組，這可確保 AKS 叢集中兩個節點集區中的代理程式節點實際上位於相同的資料中心。

使用您稍早建立的鄰近放置群組中的資源識別碼，並使用下列命令來新增節點集區 [`az aks nodepool add`][az-aks-nodepool-add] ：

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>清除

若要刪除叢集，請使用 [`az group delete`][az-group-delete] 命令來刪除 AKS 資源群組：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [鄰近放置群組][proximity-placement-groups]。

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

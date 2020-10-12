---
title: 預覽-將點節點集區新增至 Azure Kubernetes Service (AKS) 叢集
description: 瞭解如何將點節點集區新增至 Azure Kubernetes Service (AKS) 叢集。
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: dbb003c287a18810c2c14c4f2ea401fa55cca427
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87987285"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>預覽-將點節點集區新增至 Azure Kubernetes Service (AKS) 叢集

點節點集區是由 [點虛擬機器擴展集][vmss-spot]所支援的節點集區。 針對具有 AKS 叢集的節點使用現成的 Vm，可讓您以大幅節省成本的方式在 Azure 中利用未運用容量。 可用的未運用容量數量會根據許多因素而有所不同，包括節點大小、區域和當日時間。

部署點節點集區時，如果有可用的容量，Azure 就會配置點節點。 但點節點沒有 SLA。 支援點節點集區的點擴展集會部署在單一容錯網域中，且不提供高可用性保證。 當 Azure 需要取回容量時，Azure 基礎結構將會收回點節點。

點節點非常適合可處理中斷、提早終止或收回的工作負載。 例如，批次處理工作、開發和測試環境，以及大型計算工作負載等工作負載，可能是在點節點集區上排程的絕佳候選項目。

在本文中，您會將次要點節點集區新增至現有的 Azure Kubernetes Service (AKS) 叢集。

本文假設您對 Kubernetes 和 Azure Load Balancer 概念有基本瞭解。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

此功能目前為預覽狀態。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

當您建立叢集以使用點節點集區時，該叢集也必須使用節點集區和 *標準* SKU 負載平衡器的虛擬機器擴展集。 您也必須在建立叢集之後新增額外的節點集區，以使用點節點集區。 稍後的步驟中會涵蓋新增額外的節點集區，但您必須先啟用預覽功能。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-spotpoolpreview-preview-feature"></a>註冊 spotpoolpreview 預覽功能

若要建立使用點節點集區的 AKS 叢集，您必須在您的訂用帳戶上啟用 *spotpoolpreview* 功能旗標。 這項功能會在設定叢集時提供最新的服務增強功能。

使用[az feature register][az-feature-register]命令註冊*spotpoolpreview*功能旗標，如下列範例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

狀態需要幾分鐘的時間才會顯示「已註冊」**。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

當您準備好時，請使用[az provider register][az-provider-register]命令重新整理 *>microsoft.containerservice*資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 延伸模組

若要建立使用點節點集區的 AKS 叢集，您需要0.4.32 或更高版本的 *AKS-preview* CLI 擴充功能。 請使用 [az extension add][az-extension-add] 命令安裝 aks-preview Azure CLI 擴充功能，然後使用 [az extension update][az-extension-update] 命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

當您使用點節點集區建立和管理 AKS 叢集時，適用下列限制：

* 點節點集區不可以是叢集的預設節點集區。 點節點集區只能用於次要集區。
* 因為現成節點集區無法保證隔離並清空，所以您無法升級點節點集區。 您必須將現有的點節點集區取代為新的節點集區，以進行作業（例如升級 Kubernetes 版本）。 若要取代點節點集區，請建立具有不同 Kubernetes 版本的新點節點集區，等待其狀態為 [ *就緒*]，然後移除舊的節點集區。
* 控制平面和節點集區無法同時升級。 您必須個別進行升級，或移除點節點集區，以同時升級控制平面和剩餘的節點集區。
* 點節點集區必須使用虛擬機器擴展集。
* 建立之後，您就無法變更 ScaleSetPriority 或 SpotMaxPrice。
* 設定 SpotMaxPrice 時，值必須是-1 或具有最多五個小數位數的正數值。
* 點節點集區會有標籤 *kubernetes.azure.com/scalesetpriority:spot*、污點 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*和系統 pod 會有反親和性。
* 您必須新增 [對應的 >toleration][spot-toleration] ，以排程點節點集區上的工作負載。

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>將現成節點集區新增至 AKS 叢集

您必須將點節點集區新增至已啟用多個節點集區的現有叢集。 [這裡][use-multiple-node-pools]提供使用多個節點集區建立 AKS 叢集的詳細資料。

使用 [az aks nodepool add][az-aks-nodepool-add]建立節點集區。
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

依預設，當您建立具有多個節點集區的叢集時，您會在 AKS 叢集中建立具有*一般**優先順序*的節點集區。 上述命令會將輔助節點集區新增至具有*點**優先權*的現有 AKS 叢集。 *點*的*優先順序*讓節點集區成為點節點集區。 在上述範例中，收回 *原則* 參數設定為 [ *刪除* ]，這是預設值。 當您將收回 [原則][eviction-policy] 設定為 [ *刪除*] 時，會在節點集區的基礎擴展集中刪除節點。 您也可以將收回原則設定為 [ *解除配置*]。 當您將收回原則設定為 [ *解除配置*] 時，基礎擴展集中的節點會在收回時設為已停止的已解除配置狀態。 針對您的計算配額，已停止解除配置狀態計數的節點，可能會導致叢集調整或升級時發生問題。 *優先順序*和收回*原則*值只能在建立節點集區期間設定。 這些值稍後無法更新。

此命令也會啟用叢集 [自動調整程式][cluster-autoscaler]，建議搭配使用點節點集區。 叢集自動調整程式會根據叢集中執行的工作負載，擴大並縮小節點集區中的節點數目。 針對點節點集區，如果仍然需要額外的節點，叢集自動調整程式會在收回後擴大節點數目。 如果您變更節點集區可以擁有的節點數目上限，您也需要調整 `maxCount` 與叢集自動調整程式相關聯的值。 如果您未使用叢集自動調整程式，則在收回時，點集區最後會減少為零，且需要手動作業才能接收任何其他的點節點。

> [!Important]
> 只排程可處理中斷的點節點集區上的工作負載，例如批次處理作業和測試環境。 建議您在您的點節點集區上設定 [污點和容差][taints-tolerations] ，以確保只有可處理節點收回的工作負載會排定在點節點集區上。 例如，上述的命令 ny 預設會新增 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* 的污點，因此只會在此節點上排程具有對應 >toleration 的 pod。

## <a name="verify-the-spot-node-pool"></a>確認點節點集區

若要確認您的節點集區已新增為點節點集區：

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

確認*scaleSetPriority*是*現成的。*

若要將 pod 排程在點節點上執行，請新增對應至您的點節點所套用之污點的 >toleration。 下列範例會顯示 yaml 檔的一部分，其中定義對應于上一個步驟中所使用之 *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* 污點的 >toleration。

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

當部署具有此 >toleration 的 pod 時，Kubernetes 可以在套用污點的節點上成功排程 pod。

## <a name="max-price-for-a-spot-pool"></a>點集區的最大價格
[點實例的定價是][pricing-spot]根據區域和 SKU 的變數。 如需詳細資訊，請參閱 [Linux][pricing-linux] 和 [Windows][pricing-windows]的定價。

有了變數定價，您可以選擇使用最多5個小數位數，以美元為單位來設定最大價格（以美元為單位） (USD) 。 例如，值 *0.98765* 的最大價格為每小時 $0.98765 美元。 如果您將最大價格設定為 *-1*，將不會根據價格來收回實例。 如果有可用的容量和配額，實例的價格將會是標準實例的目前價格或價格（以較小者為准）。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何將點節點集區新增至 AKS 叢集。 如需如何跨節點集區控制 pod 的詳細資訊，請參閱 [AKS 中先進排程器功能的最佳做法][operator-best-practices-advanced-scheduler]。

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md
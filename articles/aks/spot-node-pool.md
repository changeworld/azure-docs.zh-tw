---
title: 預覽 - 將點節點池添加到 Azure 庫伯奈斯服務 （AKS） 群集
description: 瞭解如何將點節點池添加到 Azure 庫伯奈斯服務 （AKS） 群集。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622049"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>預覽 - 將點節點池添加到 Azure 庫伯奈斯服務 （AKS） 群集

點節點池是由[現場虛擬機器規模集][vmss-spot]支援的節點池。 將現場 VM 用於 AKS 群集的節點，使您能夠在顯著節省成本時利用 Azure 中的未利用容量。 可用未用容量的數量將因許多因素而異，包括節點大小、區域和一天中的時間。

部署點節點池時，Azure 將分配存在可用容量的點節點。 但是沒有用於點節點的 SLA。 支援點節點池的點標縮放集部署在單個容錯域中，並且不提供高可用性保證。 在 Azure 需要返回容量的任何時候，Azure 基礎結構將驅逐點節點。

Spot 節點非常適合處理中斷、早期終止或逐出的工作負載。 例如，批次處理作業、開發和測試環境以及大型計算工作負載等工作負載可能是在現場節點池上安排的候選工作。

在本文中，您將輔助點節點池添加到現有的 Azure 庫伯奈斯服務 （AKS） 群集。

本文假定對庫伯奈斯和 Azure 負載等化器概念的基本理解。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

此功能目前為預覽狀態。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>開始之前

創建群集以使用點節點池時，該群集還必須對節點池和*標準*SKU 負載等化器使用虛擬機器縮放集。 在創建群集以使用點節點池後，還必須添加其他節點池。 稍後的步驟將介紹添加其他節點池，但首先需要啟用預覽功能。

> [!IMPORTANT]
> AKS 預覽功能是自助服務、加入宣告功能。 提供它們是為了收集我們社區的回饋和錯誤。 在預覽版中，這些功能不用於生產。 公共預覽版中的功能屬於"盡力而為"支援。 AKS 技術支援小組僅在工作時間提供太平洋時區 （PST） 的説明。 有關詳細資訊，請參閱以下支援文章：
>
> * [AKS 支援策略][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>註冊專時池預覽預覽功能

要創建使用即點節點池的 AKS 群集，必須在訂閱上啟用*spotpool 預覽*功能標誌。 此功能在配置群集時提供一組最新的服務增強功能。

> [!CAUTION]
> 在訂閱上註冊功能時，當前無法取消註冊該功能。 啟用某些預覽功能後，可能會將預設值用於訂閱中創建的所有 AKS 群集。 不要在生產訂閱上啟用預覽功能。 使用單獨的訂閱測試預覽功能並收集回饋。

使用[az 要素寄存器][az-feature-register]命令註冊*spotpool 預覽*功能標誌，如以下示例所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

狀態需要幾分鐘的時間才會顯示「已註冊」**。 您可以使用 [az feature list][az-feature-list] 命令檢查註冊狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

準備就緒時，使用 [az provider register][az-provider-register] 命令重新整理 *Microsoft.ContainerService* 資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

要創建使用點節點池的 AKS 群集，您需要*aks 預覽*CLI 擴展版本 0.4.32 或更高版本。 使用[az 擴展添加][az-extension-add]命令安裝*aks 預覽*Azure CLI 擴展，然後使用[az 擴展更新][az-extension-update]命令檢查任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

當您使用點節點池創建和管理 AKS 群集時，以下限制適用：

* 點節點池不能是群集的預設節點池。 點節點池只能用於輔助池。
* 無法升級點節點池，因為點節點池不能保證警戒線和排空。 您必須用新的點節點池替換現有點節點池，以便執行升級 Kubernetes 版本等操作。 要替換點節點池，請使用不同版本的 Kubernetes 創建新的點節點池，等待其狀態*為"就緒"，* 然後刪除舊節點池。
* 不能同時升級控制平面和節點池。 您必須單獨升級它們或刪除點節點池，以便同時升級控制平面和剩餘節點池。
* 點節點池必須使用虛擬機器縮放集。
* 創建後，您不能更改 ScaleSet 優先順序或 SpotMaxPrice。
* 設置 SpotMaxPrice 時，該值必須為 -1 或最多五位小數位的正值。
* 點節點池將具有*kubernetes.azure.com/scalesetpriority:spot*標籤，污染*kubernetes.azure.com/scalesetpriority=spot:NoSchedule，* 系統窗格將具有抗親和力。
* 您必須添加[相應的容忍，][spot-toleration]以在點節點池上計畫工作負載。

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>將點節點池添加到 AKS 群集

您必須將點節點池添加到啟用了多個節點池的現有群集。 有關創建具有多個節點池的 AKS 群集的更多詳細資訊[，請在此處][use-multiple-node-pools]獲取。

使用[az aks 節點池添加][az-aks-nodepool-add]創建節點池。
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

預設情況下，在創建具有多個節點池的群集時，在 AKS 群集中創建*優先順序*為 *"常規"* 的節點池。 上述命令將輔助節點池添加到具有*Spot**優先順序*的現有 AKS 群集。 *Spot*的*優先順序*使節點池成為點節點池。 驅逐*策略*參數設置為*刪除在上面的*示例中，這是預設值。 將[逐出策略][eviction-policy]設置為 *"刪除*"時，節點池的基礎規模集中的節點在被逐出時將被刪除。 您還可以將逐出策略設置為 *"去分配*"。 將逐出策略設置為 *"去分配"* 時，基礎比例集中的節點在逐出時設置為停止分配狀態。 停止處理位置狀態的節點會計入計算配額，並可能導致群集縮放或升級問題。 只能在節點池創建期間設置*優先順序*和*逐出策略*值。 這些值以後無法更新。

該命令還啟用[群集自動縮放器][cluster-autoscaler]，建議該自動縮放器與點節點池一起使用。 根據群集中運行的工作負載，群集自動縮放器可擴展並縮小節點池中的節點數。 對於點節點池，如果仍需要額外的節點，群集自動縮放器將在逐出後放大節點數。 如果更改節點池可以具有的最大節點數，則還需要調整與群集自動縮放器關聯的`maxCount`值。 如果不使用群集自動縮放器，在逐出時，即期池最終將減少到零，並且需要手動操作才能接收任何其他點節點。

> [!Important]
> 僅計畫可處理中斷的現場節點池上的工作負載，例如批次處理作業和測試環境。 建議您在點節點池上設置[污點和節要][taints-tolerations]，以確保僅將只能處理節點逐出的工作負載安排在現場節點池上。 例如，上述命令 ny 預設添加*kubernetes.azure.com/scalesetpriority=spot:NoSchedule*的污點，因此僅在此節點上計畫具有相應容忍的 pod。

## <a name="verify-the-spot-node-pool"></a>驗證點節點池

要驗證節點池已添加為點節點池，請進行以下檢查：

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

確認*比例設定優先權*是*Spot*。

要安排在點節點上運行的 pod，請添加與應用於點節點的污點對應的容忍。 下面的示例顯示了 yaml 檔的一部分，該檔定義了對應于上一步中使用的*kubernetes.azure.com/scalesetpriority=spot:NoSchedule*污點的容忍。

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

部署具有此容忍的 pod 時，Kubernetes 可以成功將 pod 安排在應用了污點的節點上。

## <a name="max-price-for-a-spot-pool"></a>現貨池的最高價格
[現貨實例的定價是可變][pricing-spot]的，基於區域和 SKU。 有關詳細資訊，請參閱[Linux][pricing-linux]和[Windows][pricing-windows]的定價。

使用可變定價，您可以選擇使用最多 5 個小數位設置最高價格（美元 （USD）。 例如，值*0.98765*將是每小時 0.98765 美元的最高價格。 如果將最高價格設置為 *-1，* 則實例不會根據價格被逐出。 實例的價格將是 Spot 的當前價格或標準實例的價格，以較低者為准，只要有容量和配額可用。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何將點節點池添加到 AKS 群集。 有關如何跨節點池控制 pod 的詳細資訊，請參閱[AKS 中高級計畫程式功能的最佳做法][operator-best-practices-advanced-scheduler]。

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
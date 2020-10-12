---
title: 使用 Azure Kubernetes Service (AKS) 中的叢集自動調整程式
description: 了解如何使用叢集自動調整程式，根據 Azure Kubernetes Service (AKS) 叢集中的應用程式需求，自動調整叢集。
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 9f1dcc64569e9822e3703312740450e2528479dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88257520"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>自動調整叢集以符合 Azure Kubernetes Service (AKS) 的應用程式需求

為了符合 Azure Kubernetes Service (AKS) 中的應用程式需求，您可能需要調整執行工作負載的節點數目。 叢集自動調整程式元件可以監看叢集中由於資源限制而無法調度的 Pod。 偵測到問題時，節點集區中的節點數目會增加以符合應用程式需求。 也會定期檢查節點是否缺少執行的 Pod，然後視需要減少節點數目。 自動擴大或縮減 AKS 叢集中節點數目的功能，可讓您執行有效率、符合成本效益的叢集。

本文示範如何啟用和管理 AKS 叢集中的叢集自動調整程式。

## <a name="before-you-begin"></a>開始之前

本文需要您執行 Azure CLI 2.0.76 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="about-the-cluster-autoscaler"></a>關於叢集自動調整程式

為了適應不斷變化的應用程式需求，例如工作日和晚上之間或在週末，叢集通常需要一種自動調整的方法。 AKS 叢集可以透過下列兩種方式之一進行縮減：

* **叢集自動調整程式**會監看由於資源限制而無法在節點上進行排程的 Pod。 叢集接著會自動增加節點數目。
* **水平 Pod 自動調整程式**會在 Kubernetes 叢集中使用計量伺服器，以監視 Pod 的資源需求。 如果應用程式需要更多資源，Pod 數目會自動增加以符合需求。

![叢集自動調整程式和水平 Pod 自動調整程式通常會共同運作，以支援所需的應用程式需求](media/autoscaler/cluster-autoscaler.png)

然後，水平 Pod 自動調整程式和叢集自動調整程式皆可視需要減少 Pod 和節點數目。 當一段時間內有未使用的容量時，叢集自動調整程式會減少節點數目。 叢集自動調整程式所要移除的節點，其上的 Pod 會安全地排程在叢集中的其他位置。 如果 Pod 無法移動，叢集自動調整程式可能無法縮小，如下列情況所示：

* Pod 會直接建立，而且不受控制器物件支援，例如部署或複本集。
* Pod 中斷預算 (PDB) 限制太多，而且不允許低於特定閾值的 Pod 數目。
* 如果排程在不同節點上，則 Pod 會使用節點選取器或無法接受的反親和性。

如需更多叢集自動調整程式可能無法縮小的相關資訊，請參閱[哪些類型的 Pod 可以防止叢集自動調整程式移除節點？][autoscaler-scaledown](英文)。

叢集自動調整程式會使用啟動參數來處理調整事件和資源閾值之間的時間間隔。 如需有關叢集自動調整程式使用哪些參數的詳細資訊，請參閱 [使用自動調整程式設定檔](#using-the-autoscaler-profile)。

叢集和水平 Pod 自動調整程式可以搭配運作，而且通常會同時部署在一個叢集中。 當兩者組合時，水平 Pod 自動調整程式著重於執行符合應用程式需求的 Pod 數目。 叢集自動調整程式著重於執行支援排程 Pod 所需的節點數目。

> [!NOTE]
> 當您使用叢集自動調整程式時，會停用手動調整。 可讓叢集自動調整程式判斷所需的節點數目。 如果您想要手動調整叢集，[請停用叢集自動調整程式](#disable-the-cluster-autoscaler)。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>建立 AKS 叢集並啟用叢集自動調整程式

如果您需要建立 AKS 叢集，請使用 [az aks create][az-aks-create] 命令。 若要在叢集的節點集區上啟用和設定叢集自動調整程式，請使用 --enable-cluster-autoscaler 參數，並指定節點 --min-count 和 --max-count。

> [!IMPORTANT]
> 叢集自動調整程式是一項 Kubernetes 元件。 雖然 AKS 叢集會將虛擬機器擴展集用於節點，但請勿手動在 Azure 入口網站中或使用 Azure CLI 啟用或編輯擴展集自動調整的設定。 請讓 Kubernetes 叢集自動調整程式管理所需的調整設定。 如需詳細資訊，請參閱[我可以在節點資源群組中修改 AKS 資源嗎？][aks-faq-node-resource-group]

下列範例會建立 AKS 叢集，其中包含虛擬機器擴展集所支援的單一節點集區。 範例中也會啟用叢集節點集區上的叢集自動調整程式，並設定最少 1 個、最多 3 個節點：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

建立叢集和設定叢集自動調整程式設定需要幾分鐘的時間。

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>更新現有的 AKS 叢集以啟用叢集自動調整程式

使用 [az aks update][az-aks-update] 命令，在節點集區上啟用和設定現有叢集的叢集自動調整程式。 使用 *--enable-自動調整程式* 參數，並指定節點 *--最小計數* 和 *--最大計數*。

> [!IMPORTANT]
> 叢集自動調整程式是一項 Kubernetes 元件。 雖然 AKS 叢集會將虛擬機器擴展集用於節點，但請勿手動在 Azure 入口網站中或使用 Azure CLI 啟用或編輯擴展集自動調整的設定。 請讓 Kubernetes 叢集自動調整程式管理所需的調整設定。 如需詳細資訊，請參閱[我可以在節點資源群組中修改 AKS 資源嗎？][aks-faq-node-resource-group]

下列範例會更新現有的 AKS 叢集，以啟用叢集節點集區上的叢集自動調整程式，並設定最少 *1* 個和最多 *3* 個節點：

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

需要幾分鐘的時間來更新叢集，並設定叢集自動調整程式設定。

## <a name="change-the-cluster-autoscaler-settings"></a>變更叢集自動調整程式設定

> [!IMPORTANT]
> 如果您的 AKS 叢集中有多個節點集區，請跳至[自動調整具有多個代理程式集區的叢集](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)一節。 具有多個代理程式集區的叢集需要使用 `az aks nodepool` 命令集來變更節點集區特定屬性，而不是 `az aks`。

在建立 AKS 叢集或更新現有節點集區的先前步驟中，叢集自動調整程式最小節點計數已設為 1，最大節點計數已設為 3。 隨著應用程式需求的變化，您可能需要調整叢集自動調整程式節點計數。

若要變更節點計數，請使用 [az aks update][az-aks-update] 命令。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上述範例會將 myAKSCluster 中單一節點集區上的叢集自動調整程式更新為最少 1 個、最多 5 個節點。

> [!NOTE]
> 叢集自動調整程式會根據每個節點集區上設定的最小和最大計數來做出調整決策，但不會在更新最小或最大計數之後強制執行。 例如，當目前節點計數為3時，將最小計數設為5，將不會立即將集區調整為5。 如果節點集區的最小計數值高於目前的節點數目，則當有足夠的設無法排程 pod 需要2個新的額外節點，並觸發自動調整程式事件時，將會遵守新的 min 或 max 設定。 在調整事件之後，會遵守新的計數限制。

監視應用程式和服務的效能，並調整叢集自動調整程式節點計數，以符合所需的效能。

## <a name="using-the-autoscaler-profile"></a>使用自動調整程式設定檔

您也可以變更整個叢集的自動調整程式設定檔的預設值，以此方式設定叢集自動調整程式的細部詳細資料。 例如，在節點使用率過低 10 分鐘後之後，就會發生縮小事件。 如果有每隔 15 分鐘執行一次的工作負載，建議您變更自動調整程式設定檔，以便在節點使用量過低 15 或 20 分鐘後將之縮小。 啟用叢集自動調整程式時，除非指定不同的設定，否則會使用預設設定檔。 叢集自動調整程式設定檔具有下列可供您更新的設定：

| 設定                          | 描述                                                                              | 預設值 |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| scan-interval                    | 重新評估叢集以擴大或縮小的頻率                                    | 10 秒    |
| scale-down-delay-after-add       | 擴大後再繼續進行縮小評估的時間長度                               | 10 分鐘    |
| scale-down-delay-after-delete    | 節點刪除後再繼續進行縮小評估的時間長度                          | scan-interval |
| scale-down-delay-after-failure   | 縮小失敗後再繼續進行縮小評估的時間長度                     | 3 分鐘     |
| scale-down-unneeded-time         | 節點在符合縮小條件前，處於不受需要狀態的時間長度                  | 10 分鐘    |
| scale-down-unready-time          | 未就緒節點在符合縮小條件前，處於不受需要狀態的時間長度         | 20 分鐘    |
| scale-down-utilization-threshold | 節點使用率層級 (其定義是所要求資源總和除以容量)，低於此等級可考慮將節點縮小 | 0.5 |
| max-graceful-termination-sec     | 嘗試縮小節點時，叢集自動調整程式等待 Pod 終止的秒數上限。 | 600 秒   |
| balance-similar-node-groups | 偵測相似的節點集區，並平衡其間的節點數目 | false |

> [!IMPORTANT]
> 叢集自動調整程式設定檔會影響所有使用叢集自動調整程式的節點集區。 無法為每個節點集區設定一個自動調整程式設定檔。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要設定叢集自動調整程式設定設定檔，您需要 aks-preview CLI 擴充功能版本 0.4.30 或以後版本。 請使用 [az extension add][az-extension-add] 命令安裝 aks-preview Azure CLI 擴充功能，然後使用 [az extension update][az-extension-update] 命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>在現有的 AKS 叢集上設定叢集自動調整程式設定檔

請使用 [az aks update][az-aks-update-preview] 命令搭配 cluster-autoscaler-profile 參數，在您的叢集上設定叢集自動調整程式設定檔。 下列範例會在設定檔中將掃描間隔設為 30 秒。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

您在叢集中的節點集區上啟用叢集自動調整程式時，這些叢集也會使用叢集自動調整程式設定檔。 例如：

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> 您設定叢集自動調整程式設定檔時，任何已啟用叢集自動調整程式的現有節點集區，都會立即開始使用設定檔。

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>在建立 AKS 叢集時設定叢集自動調整程式設定檔

建立叢集時，您也可以使用 cluster-autoscaler-profile 參數。 例如：

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

上述命令會建立 AKS 叢集，並針對整個叢集的自動調整程式設定檔，將掃描間隔定義為 30 秒。 此命令也會啟用初始節點集區上的叢集自動調整程式，將最小節點計數設定為 1，並將節點計數上限設定為 3。

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>將叢集自動調整程式設定檔重設定為預設值

請使用 [az aks update][az-aks-update-preview] 命令，在叢集上重設叢集自動調整程式設定檔。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>停用叢集自動調整程式

如果您不想繼續使用叢集自動調整程式，可以使用[az aks update][az-aks-update-preview]命令來停用該程式，並指定 *--disable-cluster-autoscaler*參數。 當叢集自動調整程式停用時，不會移除節點。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

您可以使用 [az aks scale][az-aks-scale] 命令，在停用叢集自動調整程式之後手動調整叢集。 如果您使用水平 Pod 自動調整程式，該功能會在停用叢集自動調整程式的情況下繼續執行，但如果所有節點資源皆在使用中，Pod 最後可能會無法排程。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>重新啟用已停用的叢集自動調整程式

如果您想要在現有叢集上重新啟用叢集自動調整程式，您可以使用 [az aks update][az-aks-update-preview] 命令重新啟用，並指定 --enable-cluster-autoscaler、--min-count 和 --max count 參數。

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>取出叢集自動調整程式記錄和狀態

若要診斷自動調整程式事件並偵錯，可以從自動調整程式附加元件擷取記錄和狀態。

AKS 會代表您管理叢集自動調整程式，並在受控控制平面中執行。 主要節點記錄必須設定為可視為結果。

若要將記錄設為從叢集自動調整程式推送至 Log Analytics，請按下列步驟操作。

1. 設定資源記錄規則，將叢集自動調整程式記錄推送至 Log Analytics。 [這裡提供了詳細說明][aks-view-master-logs]，請務必在選取 [記錄] 選項時，勾選 `cluster-autoscaler` 的核取方塊。
1. 透過 Azure 入口網站，按一下您叢集上的 [記錄] 區段。
1. 將下列範例查詢輸入至 Log Analytics：

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

只要有要可供擷取的記錄，應會看到類似下列範例的記錄。

![Log Analytics 記錄](media/autoscaler/autoscaler-logs.png)

叢集自動調整程式也會將健全狀態寫出至名為 `cluster-autoscaler-status` 的 configmap。 若要取出這些記錄，請執行下列 `kubectl` 命令。 以叢集自動調整程式設定的各個節點集區，會報告健全狀況狀態。

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

若要深入瞭解自動調整程式中記錄的內容，請參閱 [Kubernetes/自動調整程式 GitHub 專案][kubernetes-faq]的常見問題集 (英文)。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>使用已啟用多個節點集區的叢集自動調整程式

可在啟用[多個節點集區][aks-multiple-node-pools]的狀態下，搭配使用叢集自動調整程式。 繼續閱讀該文件可瞭解如何啟用多個節點集區，並將其他節點集區新增至現有的叢集。 同時使用這兩個功能時，需在叢集中各個節點集區上啟用叢集自動調整程式，而且能將唯一的自動調整規則傳遞給每個節點集區。

下列命令預設您已按照本文前段的[初始指示](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)操作，而您想要將現有節點集區的最大計數從 3 更新為 5。 使用 [az aks nodepool update][az-aks-nodepool-update] 命令更新現有節點集區的設定。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

使用 [az aks nodepool update][az-aks-nodepool-update] 並傳遞 `--disable-cluster-autoscaler` 參數，即可停用叢集自動調整程式。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

如果您想要在現有叢集上重新啟用叢集自動調整程式，您可以使用 [az aks nodepool update][az-aks-nodepool-update] 命令重新啟用，並指定 --enable-cluster-autoscaler、--min-count和 -max count *-* 參數。

## <a name="next-steps"></a>後續步驟

本文示範如何自動調整 AKS 節點數目。 您也可以使用水平 Pod 自動調整程式，自動調整執行應用程式的 Pod 數目。 如需使用水平 Pod 自動調整程式的步驟，請參閱[調整 AKS 中的應用程式][aks-scale-apps]。

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why

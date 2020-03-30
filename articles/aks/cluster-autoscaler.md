---
title: 使用 Azure Kubernetes Service (AKS) 中的叢集自動調整程式
description: 了解如何使用叢集自動調整程式，根據 Azure Kubernetes Service (AKS) 叢集中的應用程式需求，自動調整叢集。
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 0b94865d81afc56c24d470012c668662f003a1b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596244"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>自動調整叢集以符合 Azure Kubernetes Service (AKS) 的應用程式需求

為了符合 Azure Kubernetes Service (AKS) 中的應用程式需求，您可能需要調整執行工作負載的節點數目。 叢集自動調整程式元件可以監看叢集中由於資源限制而無法調度的 Pod。 檢測到問題時，節點池中的節點數會增加以滿足應用程式需求。 也會定期檢查節點是否缺少執行的 Pod，然後視需要減少節點數目。 自動相應增加或相應減少 AKS 叢集中節點數目的功能，可讓您執行有效率、符合成本效益的叢集。

本文示範如何啟用和管理 AKS 叢集中的叢集自動調整程式。

## <a name="before-you-begin"></a>開始之前

本文要求您運行 Azure CLI 版本 2.0.76 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="limitations"></a>限制

創建和管理使用群集自動縮放器的 AKS 群集時，以下限制適用：

* 不能使用 HTTP 應用程式路由載入項。

## <a name="about-the-cluster-autoscaler"></a>關於叢集自動調整程式

為了適應不斷變化的應用程式需求，例如工作日和晚上之間或在週末，叢集通常需要一種自動調整的方法。 AKS 叢集可以透過下列兩種方式之一進行擴充：

* **叢集自動調整程式**會監看由於資源限制而無法在節點上進行排程的 Pod。 然後，群集會自動增加節點數。
* **水平 Pod 自動調整程式**會在 Kubernetes 叢集中使用計量伺服器，以監視 Pod 的資源需求。 如果應用程式需要更多資源，則會自動增加 pod 的數量以滿足需求。

![叢集自動調整程式和水平 Pod 自動調整程式通常會共同運作，以支援所需的應用程式需求](media/autoscaler/cluster-autoscaler.png)

水準窗格自動縮放器和群集自動縮放器還可以根據需要減少 pod 和節點的數量。 當一段時間內有未使用的容量時，叢集自動調整程式會減少節點數目。 叢集自動調整程式所要移除的節點，其上的 Pod 會安全地排程在叢集中的其他位置。 如果 Pod 無法移動，叢集自動調整程式可能無法相應減少，如下列情況所示：

* 窗格是直接創建的，並且不由控制器物件（如部署或複本集）支援。
* Pod 中斷預算 (PDB) 限制太多，而且不允許低於特定閾值的 Pod 數目。
* 如果排程在不同節點上，則 Pod 會使用節點選取器或無法接受的反親和性。

如需更多叢集自動調整程式可能無法相應減少的相關資訊，請參閱[哪些類型的 Pod 可以防止叢集自動調整程式移除節點？][autoscaler-scaledown]

叢集自動調整程式會使用啟動參數來處理調整事件和資源閾值之間的時間間隔。 這些參數由 Azure 平台定義，而且目前未公開供您調整。 如需叢集自動調整程式使用哪些參數的相關資訊，請參閱[什麼是叢集自動調整程式參數？][autoscaler-parameters]。

群集和水準窗格自動縮放器可以協同工作，並且通常都部署在群集中。 當兩者組合時，水平 Pod 自動調整程式著重於執行符合應用程式需求的 Pod 數目。 叢集自動調整程式著重於執行支援排程 Pod 所需的節點數目。

> [!NOTE]
> 當您使用叢集自動調整程式時，會停用手動調整。 可讓叢集自動調整程式判斷所需的節點數目。 如果您想要手動調整叢集，[請停用叢集自動調整程式](#disable-the-cluster-autoscaler)。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>建立 AKS 叢集並啟用叢集自動調整程式

如果您需要建立 AKS 叢集，請使用 [az aks create][az-aks-create] 命令。 要在群集的節點池上啟用和配置群集自動縮放器，請使用 *--啟用群集自動縮放器*參數，並指定一個節點 -*最小計數*和 *-最大值計數*。

> [!IMPORTANT]
> 叢集自動調整程式是一項 Kubernetes 元件。 雖然 AKS 叢集會將虛擬機器擴展集用於節點，但請勿手動在 Azure 入口網站中或使用 Azure CLI 啟用或編輯擴展集自動調整的設定。 請讓 Kubernetes 叢集自動調整程式管理所需的調整設定。 有關詳細資訊，請參閱[是否可以修改節點資源組中的 AKS 資源？](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

下面的示例創建一個 AKS 群集，該群集由虛擬機器規模集支援單個節點池。 它還啟用叢集節點池上的群集自動縮放器，並至少設置*1*個節點，最多*設置 3 個*節點：

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

## <a name="change-the-cluster-autoscaler-settings"></a>變更叢集自動調整程式設定

> [!IMPORTANT]
> 如果 AKS 群集中有多個節點池，請跳至[具有多個代理池部分的自動縮放](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。 具有多個代理池的群集需要使用`az aks nodepool`命令集來更改節點池特定屬性，而不是`az aks`。

在創建 AKS 群集或更新現有節點池的上一步中，群集自動縮放器最小節點計數設置為*1*，最大節點計數設置為*3*。 隨著應用程式需求的變化，您可能需要調整叢集自動調整程式節點計數。

要更改節點計數，請使用[az aks 更新][az-aks-update]命令。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上述示例將*myAKSCluster*中單個節點池上的群集自動縮放器更新為至少*1*個節點，最多*5*個節點。

> [!NOTE]
> 不能設置比當前為節點池設置的最小節點計數更高的最小節點計數。 例如，如果您目前的最小計數已設為 *1*，則無法將最小計數更新為 *3*。

監視應用程式和服務的效能，並調整叢集自動調整程式節點計數，以符合所需的效能。

## <a name="using-the-autoscaler-profile"></a>使用自動縮放器設定檔

還可以通過更改群集範圍自動縮放器設定檔中的預設值來配置群集自動縮放器的更精細的詳細資訊。 例如，在 10 分鐘後節點未充分利用後發生縮減事件。 如果工作負載每 15 分鐘運行一次，則可能需要更改自動縮放器設定檔，在 15 或 20 分鐘後縮小未充分利用的節點。 啟用群集自動縮放器時，除非指定不同的設置，否則將使用預設設定檔。 群集自動縮放器設定檔具有您可以更新的以下設置：

| 設定                          | 描述                                                                              | 預設值 |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| 掃描間隔                    | 重新計算向上或向下擴展的群集的頻率                                    | 10 秒    |
| 放大縮小字體功能 放大縮小字體功能       | 縮減規模後多久，縮減評估恢復                               | 10 分鐘    |
| 縮小-延遲-刪除後    | 縮減向下評估的節點刪除後多長時間恢復                          | 掃描間隔 |
| 縮小-延遲後故障   | 縮減縮減評估恢復的失敗後多久                     | 3 分鐘     |
| 縮小-不需要的時間         | 節點在有資格縮小之前應不需要多長時間                  | 10 分鐘    |
| 縮小-未就緒時間          | 未就緒節點在有資格縮減之前應需要多長時間         | 20 分鐘    |
| 放大縮小字體功能 放大縮小字體功能 | 節點利用率級別，定義為請求的資源除以容量的總和，在此級別下可考慮節點向下縮放 | 0.5 |
| 最大-正常終止-秒     | 群集自動縮放器在嘗試縮小節點時等待 pod 終止的最大秒數。 | 600 秒   |

> [!IMPORTANT]
> 群集自動縮放器設定檔會影響使用群集自動縮放器的所有節點池。 無法根據每個節點池設置自動縮放器設定檔。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

要設置群集自動縮放器設置設定檔，您需要*aks 預覽*CLI 擴展版本 0.4.30 或更高版本。 使用[az 擴展添加][az-extension-add]命令安裝*aks 預覽*Azure CLI 擴展，然後使用[az 擴展更新][az-extension-update]命令檢查任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>在現有 AKS 群集上設置群集自動縮放器設定檔

將[az aks 更新][az-aks-update]命令與*群集自動縮放器設定檔*參數一起使用，以在群集上設置群集自動縮放器設定檔。 下面的示例將掃描間隔設置配置為設定檔中的 30s。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

在群集中的節點池上啟用群集自動縮放器時，這些群集還將使用群集自動縮放器設定檔。 例如：

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
> 設置群集自動縮放器設定檔時，啟用群集自動縮放器的任何現有節點池都將立即開始使用該設定檔。

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>創建 AKS 群集時設置群集自動縮放器設定檔

創建群集時，還可以使用*群集自動縮放設定檔*參數。 例如：

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

上述命令創建 AKS 群集，並將掃描間隔定義為群集範圍自動縮放器設定檔的 30 秒。 該命令還啟用初始節點池上的群集自動縮放器，將最小節點計數設置為 1，最大節點計數為 3。

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>將群集自動縮放器設定檔重置為預設值

使用[az aks 更新][az-aks-update]命令重置群集上的群集自動縮放器設定檔。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>停用叢集自動調整程式

如果不再希望使用群集自動縮放器，則可以使用[az aks 更新][az-aks-update]命令禁用它，指定 *--禁用群集自動縮放器*參數。 當叢集自動調整程式停用時，不會移除節點。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

使用[az aks 縮放][az-aks-scale]命令禁用群集自動縮放器後，可以手動縮放群集。 如果使用水準窗格自動縮放器，該功能將繼續在禁用群集自動縮放器時運行，但如果所有節點資源都在使用中，則 pod 最終可能無法計畫。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>重新啟用禁用的群集自動縮放器

如果要在現有群集上重新啟用群集自動縮放器，可以使用[az aks 更新][az-aks-update]命令重新啟用它，指定 *--啟用群集自動縮放器**、-最小計數*和 *-最大值計數*參數。

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>檢索群集自動縮放器日誌和狀態

要診斷和調試自動縮放程式事件，可以從自動縮放程式載入項中檢索日誌和狀態。

AKS 代表您管理群集自動縮放器，並在託管控制平面中運行它。 必須將主節點日誌配置為結果查看。

要將要從群集自動縮放器推送到日誌分析的日誌，請按照以下步驟操作。

1. 設置診斷日誌的規則，將群集自動縮放器日誌推送到日誌分析。 [此處詳細介紹了說明](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)，請確保在選擇"日誌"選項`cluster-autoscaler`時選中該核取方塊。
1. 通過 Azure 門戶按一下群集上的"日誌"部分。
1. 將以下依例查詢輸入到日誌分析中：

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

只要有要檢索的日誌，就應該看到類似于以下示例的日誌。

![日誌分析日誌](media/autoscaler/autoscaler-logs.png)

群集自動縮放器還將將運行狀況寫入名為 的`cluster-autoscaler-status`配置檔案對應。 若要檢索這些日誌，請執行以下`kubectl`命令。 對於配置群集自動縮放器的每個節點池，將報告運行狀況狀態。

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

要瞭解有關從自動縮放器記錄的內容的更多內容，請閱讀[Kubernetes/自動縮放器 GitHub 專案的](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)常見問題解答。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>使用啟用了多個節點池的群集自動縮放器

群集自動縮放器可以同時使用，同時啟用[多個節點池](use-multiple-node-pools.md)。 請按照該文檔瞭解如何啟用多個節點池並將其他節點池添加到現有群集。 同時使用這兩個功能時，可以在群集中的每個節點池上啟用群集自動縮放器，並可以為每個節點傳遞唯一的自動縮放規則。

以下命令假定您遵循本文檔前面[的初始說明](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)，並且希望將現有節點池的最大計數從*3*更新到*5*。 使用[az aks 節點池更新][az-aks-nodepool-update]命令更新現有節點池的設置。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

可以使用[az aks 節點池更新][az-aks-nodepool-update]來禁用群集自動縮放器並`--disable-cluster-autoscaler`傳遞參數。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

如果要在現有群集上重新啟用群集自動縮放器，可以使用[az aks 節點池更新][az-aks-nodepool-update]命令重新啟用它，指定 *--啟用群集自動縮放器**、-最小計數*和 *-最大值計數*參數。

## <a name="next-steps"></a>後續步驟

本文示範如何自動調整 AKS 節點數目。 您也可以使用水平 Pod 自動調整程式，自動調整執行應用程式的 Pod 數目。 如需使用水平 Pod 自動調整程式的步驟，請參閱 [在 AKS 中調整應用程式][aks-scale-apps]。

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca

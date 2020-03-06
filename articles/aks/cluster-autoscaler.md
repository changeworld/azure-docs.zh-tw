---
title: 使用 Azure Kubernetes Service (AKS) 中的叢集自動調整程式
description: 了解如何使用叢集自動調整程式，根據 Azure Kubernetes Service (AKS) 叢集中的應用程式需求，自動調整叢集。
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 0b94865d81afc56c24d470012c668662f003a1b8
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596244"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>自動調整叢集以符合 Azure Kubernetes Service (AKS) 的應用程式需求

為了符合 Azure Kubernetes Service (AKS) 中的應用程式需求，您可能需要調整執行工作負載的節點數目。 叢集自動調整程式元件可以監看叢集中由於資源限制而無法調度的 Pod。 當偵測到問題時，節點集區中的節點數目會增加，以符合應用程式需求。 也會定期檢查節點是否缺少執行的 Pod，然後視需要減少節點數目。 自動相應增加或相應減少 AKS 叢集中節點數目的功能，可讓您執行有效率、符合成本效益的叢集。

本文示範如何啟用和管理 AKS 叢集中的叢集自動調整程式。

## <a name="before-you-begin"></a>開始之前

本文會要求您執行 Azure CLI 版本2.0.76 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="limitations"></a>限制

當您建立和管理使用叢集自動調整程式的 AKS 叢集時，適用下列限制：

* 無法使用 HTTP 應用程式路由附加元件。

## <a name="about-the-cluster-autoscaler"></a>關於叢集自動調整程式

為了適應不斷變化的應用程式需求，例如工作日和晚上之間或在週末，叢集通常需要一種自動調整的方法。 AKS 叢集可以透過下列兩種方式之一進行縮減：

* **叢集自動調整程式**會監看由於資源限制而無法在節點上進行排程的 Pod。 然後，叢集會自動增加節點的數目。
* **水平 Pod 自動調整程式**會在 Kubernetes 叢集中使用計量伺服器，以監視 Pod 的資源需求。 如果應用程式需要更多資源，pod 數目會自動增加以符合需求。

![叢集自動調整程式和水平 Pod 自動調整程式通常會共同運作，以支援所需的應用程式需求](media/autoscaler/cluster-autoscaler.png)

水準 pod 自動調整程式和叢集自動調整程式也可以視需要減少 pod 和節點的數目。 當一段時間內有未使用的容量時，叢集自動調整程式會減少節點數目。 叢集自動調整程式所要移除的節點，其上的 Pod 會安全地排程在叢集中的其他位置。 如果 Pod 無法移動，叢集自動調整程式可能無法相應減少，如下列情況所示：

* Pod 會直接建立，而且不受控制器物件的支援，例如部署或複本集。
* Pod 中斷預算 (PDB) 限制太多，而且不允許低於特定閾值的 Pod 數目。
* 如果排程在不同節點上，則 Pod 會使用節點選取器或無法接受的反親和性。

如需叢集自動調整程式如何無法相應減少的詳細資訊，請參閱[哪些類型的 pod 可能會讓叢集自動調整程式無法移除節點？][autoscaler-scaledown]

叢集自動調整程式會使用啟動參數來處理調整事件和資源閾值之間的時間間隔。 這些參數由 Azure 平台定義，而且目前未公開供您調整。 如需叢集自動調整程式所使用之參數的詳細資訊，請參閱[什麼是叢集自動調整程式參數？][autoscaler-parameters]。

叢集和水準 pod autoscalers 可以搭配使用，而且通常都是部署在叢集中。 當兩者組合時，水平 Pod 自動調整程式著重於執行符合應用程式需求的 Pod 數目。 叢集自動調整程式著重於執行支援排程 Pod 所需的節點數目。

> [!NOTE]
> 當您使用叢集自動調整程式時，會停用手動調整。 可讓叢集自動調整程式判斷所需的節點數目。 如果您想要手動調整叢集，[請停用叢集自動調整程式](#disable-the-cluster-autoscaler)。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>建立 AKS 叢集並啟用叢集自動調整程式

如果您需要建立 AKS 叢集，請使用[az AKS create][az-aks-create]命令。 若要在叢集的節點集區上啟用和設定叢集自動調整程式，請使用 *--enable-cluster-自動調整程式*參數，並指定節點 *--min-count*和 *--max 計數*。

> [!IMPORTANT]
> 叢集自動調整程式是一項 Kubernetes 元件。 雖然 AKS 叢集會將虛擬機器擴展集用於節點，但請勿手動在 Azure 入口網站中或使用 Azure CLI 啟用或編輯擴展集自動調整的設定。 請讓 Kubernetes 叢集自動調整程式管理所需的調整設定。 如需詳細資訊，請參閱[我可以修改節點資源群組中的 AKS 資源嗎？](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

下列範例會建立 AKS 叢集，其中包含一個虛擬機器擴展集所支援的單一節點集區。 它也會在叢集的節點集區上啟用叢集自動調整程式，並設定最少*1*個和最多*3*個節點：

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
> 如果您的 AKS 叢集中有多個節點集區，請跳至[使用多個代理程式組件區自動調整一節](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。 具有多個代理程式組件區的叢集需要使用 `az aks nodepool` 命令集來變更節點集區特定屬性，而不是 `az aks`。

在上一個步驟中，若要建立 AKS 叢集或更新現有的節點集區，叢集自動調整程式的最小節點計數設定為*1*，而節點計數上限設定為*3*。 隨著應用程式需求的變化，您可能需要調整叢集自動調整程式節點計數。

若要變更節點計數，請使用[az aks update][az-aks-update]命令。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上述範例會將*myAKSCluster*中單一節點集區上的叢集自動調整程式更新為最少*1*個和最多*5*個節點。

> [!NOTE]
> 您無法設定目前節點集區所設定的最小節點計數。 例如，如果您目前的最小計數已設為 *1*，則無法將最小計數更新為 *3*。

監視應用程式和服務的效能，並調整叢集自動調整程式節點計數，以符合所需的效能。

## <a name="using-the-autoscaler-profile"></a>使用自動調整程式設定檔

您也可以藉由變更整個叢集的自動調整程式設定檔中的預設值，來設定叢集自動調整程式的更細微詳細資料。 例如，在10分鐘後，節點使用率過低之後，就會發生相應減少事件。 如果您有每隔15分鐘執行一次的工作負載，您可能會想要變更自動調整程式設定檔，以在15或20分鐘後相應減少使用量過低的節點。 當您啟用叢集自動調整程式時，除非您指定不同的設定，否則會使用預設設定檔。 叢集自動調整程式設定檔具有下列可供您更新的設定：

| 設定                          | 描述                                                                              | 預設值 |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| 掃描間隔                    | 重新評估叢集以相應增加或減少的頻率                                    | 10 秒    |
| 相應減少延遲-新增之後       | 相應增加之後相應減少評估繼續的時間長度                               | 10 分鐘    |
| 向下調整-延遲後-刪除    | 節點刪除之後，相應減少評估繼續的時間長度                          | 掃描間隔 |
| 相應減少-延遲失敗   | 相應減少評估繼續的相應減少失敗後的時間長度                     | 3 分鐘     |
| 相應減少-不必要的時間         | 節點有資格相應減少之前，應該不需要的時間長度                  | 10 分鐘    |
| 相應減少-未就緒時間          | 未就緒節點有資格相應減少之前，應該不需要多久的時間         | 20 分鐘    |
| 向下延展-使用率-閾值 | 節點使用率層級（定義為要求的資源總和除以容量），低於此等級可將節點視為相應減少 | 0.5 |
| 最大-正常終止-秒     | 當嘗試相應減少節點時，叢集自動調整程式等待 pod 終止的最大秒數。 | 600秒   |

> [!IMPORTANT]
> 叢集自動調整程式設定檔會影響所有使用叢集自動調整程式的節點集區。 您無法為每個節點集區設定自動調整程式設定檔。

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要設定叢集自動調整程式設定設定檔，您需要*aks-preview* CLI 擴充功能版本0.4.30 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>在現有的 AKS 叢集上設定叢集自動調整程式設定檔

使用[az aks update][az-aks-update]命令搭配*cluster-自動調整程式-profile*參數，在您的叢集上設定叢集自動調整程式設定檔。 下列範例會將掃描間隔設定設為設定檔中的30秒。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

當您在叢集中的節點集區上啟用叢集自動調整程式時，這些叢集也會使用叢集自動調整程式設定檔。 例如：

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
> 當您設定叢集自動調整程式設定檔時，任何已啟用叢集自動調整程式的現有節點集區都會立即開始使用設定檔。

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>建立 AKS 叢集時設定叢集自動調整程式設定檔

建立叢集時，您也可以使用*自動調整程式設定檔*參數。 例如：

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

上述命令會建立 AKS 叢集，並針對整個叢集的自動調整程式設定檔定義掃描間隔為30秒。 此命令也會啟用初始節點集區上的叢集自動調整程式，將最小節點計數設定為1，並將節點計數上限設為3。

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>將叢集自動調整程式設定檔重設為預設值

使用[az aks update][az-aks-update]命令來重設叢集上的叢集自動調整程式設定檔。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>停用叢集自動調整程式

如果您不想再使用叢集自動調整程式，您可以使用[az aks update][az-aks-update]命令來停用它，並指定 *--disable-cluster-自動調整程式*參數。 當叢集自動調整程式停用時，不會移除節點。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

您可以使用[az aks scale][az-aks-scale]命令，在停用叢集自動調整程式之後，手動調整叢集。 如果您使用水準 pod 自動調整程式，該功能會繼續在叢集自動調整程式停用的情況下執行，但如果所有節點資源都在使用中，pod 可能會無法排程。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>重新啟用已停用的叢集自動調整程式

如果您想要在現有叢集上重新啟用叢集自動調整程式，您可以使用[az aks update][az-aks-update]命令來重新啟用它，並指定 *--enable-cluster-自動調整程式*、 *--min-count*和 *--max 計數*參數。

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>取出叢集自動調整程式記錄和狀態

若要診斷和調試自動調整程式事件，可以從自動調整程式附加元件抓取記錄和狀態。

AKS 會代表您管理叢集自動調整程式，並在受控控制平面中執行。 主要節點記錄必須設定為可視為結果。

若要設定將記錄從叢集自動調整程式推送至 Log Analytics，請遵循下列步驟。

1. 設定診斷記錄的規則，以將叢集自動調整程式記錄推送至 Log Analytics。 [這裡詳述指示](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)，請確定您在選取 [記錄] 的選項時，核取了 `cluster-autoscaler` 的核取方塊。
1. 按一下您叢集上的 [記錄] 區段，透過 Azure 入口網站。
1. 將下列範例查詢輸入至 Log Analytics：

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

只要有要取出的記錄，您應該會看到類似下列範例的記錄。

![Log Analytics 記錄](media/autoscaler/autoscaler-logs.png)

叢集自動調整程式也會將健康狀態寫出至名為 `cluster-autoscaler-status`的 configmap。 若要取出這些記錄，請執行下列 `kubectl` 命令。 系統會針對以叢集自動調整程式設定的每個節點集區報告健全狀況狀態。

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

若要深入瞭解自動調整程式中記錄的內容，請閱讀[Kubernetes/自動調整程式 GitHub 專案](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)上的常見問題。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>使用已啟用多個節點集區的叢集自動調整程式

叢集自動調整程式可以與[多個](use-multiple-node-pools.md)啟用的節點集區搭配使用。 遵循該檔，以瞭解如何啟用多個節點集區，並將其他節點集區新增至現有的叢集。 同時使用這兩個功能時，您會在叢集中的每個個別節點集區上啟用叢集自動調整程式，並且可以將唯一的自動調整規則傳遞給每個。

下列命令假設您已遵循本檔稍早的[初始指示](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)，而您想要將現有節點集區的最大計數從*3*更新為*5*。 使用[az aks nodepool update][az-aks-nodepool-update]命令來更新現有節點集區的設定。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

您可以使用[az aks nodepool update][az-aks-nodepool-update]來停用叢集自動調整程式，並傳遞 `--disable-cluster-autoscaler` 參數。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

如果您想要在現有叢集上重新啟用叢集自動調整程式，您可以使用[az aks nodepool update][az-aks-nodepool-update]命令來重新啟用它，並指定 *--enable-cluster-自動調整程式*、 *--min-count*和 *--max count*參數。

## <a name="next-steps"></a>後續步驟

本文示範如何自動調整 AKS 節點數目。 您也可以使用水平 Pod 自動調整程式，自動調整執行應用程式的 Pod 數目。 如需使用水準 pod 自動調整程式的步驟，請參閱[在 AKS 中調整應用程式][aks-scale-apps]。

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

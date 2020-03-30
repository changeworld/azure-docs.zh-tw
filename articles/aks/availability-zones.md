---
title: 在 Azure 庫伯奈斯服務 （AKS） 中使用可用性區域
description: 瞭解如何創建群集，該群集在 Azure 庫伯奈斯服務 （AKS） 中跨可用性區域分發節點
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596805"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>創建使用可用性區域的 Azure 庫伯奈斯服務 （AKS） 群集

Azure 庫伯奈斯服務 （AKS） 群集將資源（如節點和存儲）分佈在基礎 Azure 計算基礎結構的邏輯部分。 此部署模型可確保節點在單個 Azure 資料中心中的單獨更新域和容錯域之間運行。 使用此預設行為部署的 AKS 群集提供了高級別的可用性，以防止硬體故障或計畫維護事件。

為了向應用程式提供更高級別的可用性，AKS 群集可以跨可用性區域分發。 這些區域是給定區域內的物理獨立的資料中心。 當群集元件分佈在多個區域時，AKS 群集能夠容忍其中一個區域中的故障。 即使整個資料中心都有問題，您的應用程式和管理操作仍可用。

本文介紹如何創建 AKS 群集並跨可用性區域分發節點元件。

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 版本 2.0.76 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="limitations-and-region-availability"></a>限制和地區可用性

AKS 群集當前可以使用以下區域的可用性區域創建：

* 美國中部
* 美國東部 2
* 美國東部
* 法國中部
* 日本東部
* 北歐
* 東南亞
* 英國南部
* 西歐
* 美國西部 2

使用可用性區域創建 AKS 群集時，以下限制適用：

* 只能在創建群集時啟用可用性區域。
* 創建群集後無法更新可用性地區設定。 您也不能更新現有的非可用區域群集以使用可用性區域。
* 創建 AKS 群集後，無法禁用其可用性區域。
* 選定的節點大小 （VM SKU） 必須在所有可用性區域中可用。
* 啟用了可用性區域的群集需要使用 Azure 標準負載等化器跨區域進行分發。
* 您必須使用 Kubernetes 版本 1.13.5 或更高版本才能部署標準負載等化器。

使用可用性區域的 AKS 群集必須使用 Azure 負載等化器*標準*SKU，這是負載等化器類型的預設值。 此負載等化器類型只能在群集創建時定義。 有關詳細資訊和標準負載等化器的限制，請參閱[Azure 負載等化器標準 SKU 限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁片限制

使用 Azure 託管磁片的卷當前不是區資源。 在與其原始區域不同的區域中重新計畫的 Pod 無法重新連接其以前的磁片。 建議運行不需要可能遇到地區問題的持久存儲的無狀態工作負載。

如果必須運行有狀態的工作負載，請在窗格規範中使用污點和托架，告訴 Kubernetes 調度程式在磁片相同的區域中創建 pod。 或者，使用基於網路的存儲，如 Azure 檔，這些檔可以在區域之間安排時附加到窗格。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 群集的可用性區域概述

可用性區域是一種高可用性產品，可保護您的應用程式和資料免受資料中心故障的影響。 區域是 Azure 區域中唯一的物理位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 某個地區內可用性區域的實體區隔可保護應用程式和資料不受資料中心故障影響。 區域冗余服務跨可用性區域複製應用程式和資料，以防止單點故障。

有關詳細資訊，請參閱[Azure 中有哪些可用性區域？][az-overview]

使用可用性區域部署的 AKS 群集可以在單個區域內跨多個區域分發節點。 例如， *美國東部 2* 區域中的群集可以在*東美國 2*中的所有三個可用性區域中創建節點。 AKS 群集資源的此分佈提高了群集可用性，因為它們能夠抵禦特定區域的故障。

![跨可用性區域的 AKS 節點分佈](media/availability-zones/aks-availability-zones.png)

在區域中斷中，可以手動重新平衡節點或使用群集自動縮放器。 如果單個區域不可用，則應用程式將繼續運行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>跨可用性區域創建 AKS 群集

使用[az aks 創建][az-aks-create]命令創建群集時，`--zones`參數將定義部署到哪些區域代理節點。 在群集創建時定義參數時，`--zones`群集的 AKS 控制平面元件也會以最高可用配置跨區域分佈。

如果在創建 AKS 群集時未為預設代理池定義任何區域，則群集的 AKS 控制平面元件將不會使用可用性區域。 您可以使用[az aks 節點池添加][az-aks-nodepool-add]命令添加其他節點池，並為`--zones`這些新節點指定，但控制平面元件仍然沒有可用性區域感知。 部署節點池或 AKS 控制平面元件後，無法更改它們的區域感知。

下面的示例在名為*myResourceGroup*的資源組中創建名為*myAKSCluster*的 AKS 群集。 總共創建了*3*個節點 - 區域*1*中的一個代理 *，2*個代理中的 1 個，然後創建一個在*3*個節點中。 AKS 控制平面元件也分佈在區域之間，配置最高，因為它們被定義為群集創建過程的一部分。

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

建立 AKS 叢集需要幾分鐘的時間。

## <a name="verify-node-distribution-across-zones"></a>驗證跨區域的節點分佈

群集準備就緒後，在規模集中列出代理節點，以查看它們部署在哪些可用性區域。

首先，使用[az aks 獲取憑據][az-aks-get-credentials]命令獲取 AKS 群集憑據：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下來，使用[kubectl 描述][kubectl-describe]命令列出群集中的節點。 篩選*failure-domain.Beta.kubernetes.io/zone*值，如以下示例所示：

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

下面的示例輸出顯示了分佈在指定區域和可用性區域的三個節點，例如第一個可用性區的*Eastus2-1*和第二個可用性區域的*Eastus2-2：*

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

將其他節點添加到代理池時，Azure 平臺會自動跨指定的可用性區域分發基礎 VM。

請注意，在較新的 Kubernetes 版本（1.17.0 及更高版本）中，AKS 除了棄用之外，`failure-domain.beta.kubernetes.io/zone`還使用較新的標籤`topology.kubernetes.io/zone`。

## <a name="verify-pod-distribution-across-zones"></a>驗證跨區域的窗格分佈

正如在[已知標籤、注釋和污點][kubectl-well_known_labels]中所記錄的那樣，Kubernetes 使用`failure-domain.beta.kubernetes.io/zone`該標籤在複製控制器或服務中自動分發跨可用區域的窗格。 為了測試這一點，您可以將群集從 3 個節點擴展到 5 個節點，以驗證正確的 pod 展開：

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

當縮放操作在幾分鐘後完成時，該命令`kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"`應提供類似于此示例的輸出：

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

如您所見，我們現在在區域 1 和 2 中增加了兩個節點。 可以部署由三個副本組成的應用程式。 我們將使用 NGINX 作為示例：

```console
kubectl run nginx --image=nginx --replicas=3
```

如果驗證窗格運行的節點，您將看到該窗格在對應于三個不同可用性區域的窗格上運行。 例如，使用 該`kubectl describe pod | grep -e "^Name:" -e "^Node:"`命令，您將獲得類似于此的輸出：

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

從前面的輸出中可以看出，第一個 Pod 正在位於可用性區域`eastus2-1`的節點 0 上運行。 第二個 pod 在節點 2 上運行`eastus2-3`，該節點 2 對應于 ，第`eastus2-2`3 個在節點 4 中運行。 在沒有任何其他配置的情況下，Kubernetes 正在所有三個可用性區域中正確擴展窗格。

## <a name="next-steps"></a>後續步驟

本文詳細介紹了如何創建使用可用性區域的 AKS 群集。 有關高可用群集的更多注意事項，請參閱[AKS 中有關業務連續性和災害復原的最佳做法][best-practices-bc-dr]。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/

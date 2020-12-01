---
title: 使用 Azure Kubernetes Service (AKS) 中的可用性區域
description: 了解如何建立在 Azure Kubernetes Service (AKS) 中的可用性區域之間散發節點的叢集
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 15f66e836a2900349007fb5068a172b89f39d4de
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352791"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>建立使用可用性區域的 Azure Kubernetes Service (AKS) 叢集

Azure Kubernetes Service (AKS) 叢集會在基本 Azure 基礎結構的邏輯區段間散發資源，例如節點和儲存體。 使用可用性區域時，此部署模型會確保在指定可用性區域中的節點實際獨立於另一個可用性區域中定義的節點。 若 AKS 叢集部署了多個可用性區域，且可用性區域的設定是針對整個叢集，就能達到更高一層的可用性，在硬體失敗或計劃性維護時提供防護。

可在叢集中定義節點集區，如此即使單一區域故障，指定節點集區內的節點仍能繼續運作。 如果您的應用程式已經過協調，能容忍節點子集發生故障，那麼即使單一資料中心發生實體故障，您的應用程式仍可繼續使用。

本文說明如何建立 AKS 叢集，並跨多個可用性區域散發節點元件。

## <a name="before-you-begin"></a>開始之前

您必須安裝並設定 Azure CLI 版本 2.0.76 或以後版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="limitations-and-region-availability"></a>限制和區域可用性

目前可使用以下區域中的可用性區域來建立 AKS 叢集：

* 澳大利亞東部
* 加拿大中部
* 美國中部
* 美國東部 
* 美國東部 2
* 法國中部
* 日本東部
* 北歐
* 東南亞
* 英國南部
* 西歐
* 美國西部 2

使用可用性區域建立 AKS 叢集時，會受到以下限制：

* 建立叢集或節點集區時，您只能定義可用性區域。
* 叢集建立之後，便無法更新可用性區域設定。 即使更新現有的非可用性區域叢集，也無法透過此方式使用可用性區域。
* 所選的可用性區域必須均能使用所選的節點大小 (VM SKU)。
* 若叢集具有已經啟用的可用性區域，則需要使用 Azure Standard Load Balancer 才能跨區域散發。 此負載平衡器類型只能在叢集建立時定義。 如需標準負載平衡器的詳細資訊和限制，請參閱 [Azure 負載平衡器標準 SKU 限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁碟限制

使用 Azure 受控磁碟的磁碟區目前並非區域備援資源。 磁片區無法跨區域附加，且必須與裝載目標 pod 的指定節點共置於相同的區域中。

Kubernetes 可感知自1.12 版起的 Azure 可用性區域。 您可以在多區域 AKS 叢集中部署參考 Azure 受控磁片的 PersistentVolumeClaim 物件，而 [Kubernetes 將負責排程](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) 任何在正確的可用性區域中宣告此 PVC 的 pod。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 叢集的可用性區域概觀

可用性區域是高可用性供應項目，可保護您的應用程式和資料不受資料中心故障影響。 區域是在 Azure 區域內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 為了確保復原能力，在所有區域啟用的區域中一律會有一個以上的區域。 某個地區內可用性區域的實體區隔可保護應用程式和資料不受資料中心故障影響。

如需詳細資訊，請參閱 [Azure 中的區域和可用性區域][az-overview]。

使用可用性區域部署的 AKS 叢集，可在單一地區內的多個區域間散發節點。 例如， **  美國東部 2 區域中的叢集，可以在美國東部 2 的所有三個可用性區域中建立節點。 AKS 叢集資源此種散發方式，可在特定區域故障時復原，而能改善叢集可用性。

![跨可用性區域的 AKS 節點散發](media/availability-zones/aks-availability-zones.png)

如果叢集散佈在多個區域，當單一區域變得無法使用時，您的應用程式仍會繼續執行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>在可用性區域之間建立 AKS 叢集

當您使用 [az aks create][az-aks-create] 命令建立叢集時，會由 `--zones` 參數定義要在其中部署的區域代理程式節點。 如果您在建立叢集時定義參數，則 etcd 或 API 等控制平面元件會散佈到區域中的可用區域 `--zones` 。 控制平面元件散佈的特定區域，會獨立於初始節點集區專用的明確區域。

如果您在建立 AKS 叢集時，並未定義預設代理程式集區的任何區域，則不保證控制平面元件會在可用性區域之間散佈。 您可以使用 [az aks nodepool add][az-aks-nodepool-add] 命令來新增其他節點集區，並指定新節點的 `--zones`，但如此並不會變更控制平面在區域間散佈的方式。 僅限叢集或節點集區的建立階段，方能定義可用性區域設定。

下列範例會在名為 myResourceGroup 的資源群組中，建立名為 myAKSCluster 的 AKS 叢集。 總共會建立 3 個節點 - 區域 1、區域 2 和區域 3 各一個代理程式。

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

在決定新節點應屬於哪個區域時，指定的 AKS 節點集區會使用[基礎 Azure 虛擬機器擴展集盡可能提供的最佳工作區域平衡][vmss-zone-balancing]。 對擴展集而言，如果每個區域的 VM 數目相同，或是與所有其他區域中的 VM 數目差異為 +\- 1 個，則會將指定 AKS 節點集區視為「平衡」。

## <a name="verify-node-distribution-across-zones"></a>驗證跨區域的節點散發

當叢集準備就緒時，請列出擴展集中的代理程式節點，以查看部署節點的可用性區域。

首先請使用 [az aks get-credentials][az-aks-get-credentials] 命令取得 AKS 叢集的認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下來，使用 [kubectl 描述][kubectl-describe] 命令來列出叢集中的節點，並根據 *failure-domain.Beta.kubernetes.io/zone* 值進行篩選。 下列範例適用于 Bash shell。

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

下列範例輸出顯示了跨指定區域和可用性區域散發的三個節點，例如第一個可用性區域的 *eastus2-1*，以及第二個可用性區域的 *eastus2-2*：

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

當您將其他節點新增至代理程式集區時，Azure 平台會自動在指定的可用性區域之間散發基礎 VM。

請注意，在較新的 Kubernetes 版本 (1.17.0 和以後版本) 中，除了已淘汰的 `failure-domain.beta.kubernetes.io/zone` 之外，AKS 還會使用較新的標籤 `topology.kubernetes.io/zone`。 您可以執行下列腳本，取得與上述相同的結果：

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

這會提供更簡潔的輸出：

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>驗證跨區域的 Pod 散發

如[已知的標籤、註釋和污點][kubectl-well_known_labels] (英文) 一文中所述，Kubernetes 會使用 `failure-domain.beta.kubernetes.io/zone` 標籤自動將 Pod 散發至不同可用區域中的複寫控制器或服務。 若要進行測試，您可以將叢集從 3 個節點擴大為 5 個節點，以確認 Pod 散佈正確：

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

在幾分鐘後完成調整規模作業之後， `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` Bash shell 中的命令應該會提供類似此範例的輸出：

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

現在於區域 1 和 2 中有兩個額外的節點。 您可以部署由三個複本組成的應用程式。 本文以 NGINX 為例：

```console
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=3
```

檢視 Pod 執行所在的節點，即可發現 Pod 是在對應到三個不同可用性區域的節點上執行。 例如，使用 `kubectl describe pod | grep -e "^Name:" -e "^Node:"` Bash shell 中的命令時，您會得到如下的輸出：

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

如您在先前的輸出中所見，第一個 Pod 是在節點 0上執行，此節點位於可用性區域 `eastus2-1`。 第二個 Pod 在節點 2 上執行，其對應至 `eastus2-3`，而第三個節點則是在 `eastus2-2` 中的節點 4。 如果沒有任何其他設定，Kubernetes 會在這三個可用性區域中之間正確散佈 Pod。

## <a name="next-steps"></a>後續步驟

本文詳細說明了如何建立使用可用性區域的 AKS 叢集。 如需更多高可用性叢集的相關考量事項，請參閱[因應 AKS 中商務持續性和災害復原的最佳做法][best-practices-bc-dr]。

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
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/

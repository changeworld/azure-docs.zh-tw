---
title: 在 Azure Kubernetes Service 中使用可用性區域（AKS）
description: 瞭解如何建立可在 Azure Kubernetes Service （AKS）中的可用性區域間散發節點的叢集
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596805"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>建立使用可用性區域的 Azure Kubernetes Service （AKS）叢集

Azure Kubernetes Service （AKS）叢集會將資源（例如節點和儲存體）分散到基礎 Azure 計算基礎結構的邏輯區段。 此部署模型可確保節點在單一 Azure 資料中心內跨不同的更新和容錯網域執行。 使用此預設行為部署的 AKS 叢集可提供高可用性，以防止硬體故障或預定的維護事件。

若要為您的應用程式提供更高的可用性層級，可以將 AKS 叢集分散到可用性區域。 這些區域是在指定區域中實體獨立的資料中心。 當叢集元件分散到多個區域時，您的 AKS 叢集就能夠容忍其中一個區域的失敗。 即使一個整個資料中心發生問題，您的應用程式和管理作業仍可繼續使用。

本文說明如何建立 AKS 叢集，並將節點元件分散到多個可用性區域。

## <a name="before-you-begin"></a>開始之前

您需要安裝並設定 Azure CLI 版本2.0.76 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="limitations-and-region-availability"></a>限制和區域可用性

目前可使用下欄區域中的可用性區域來建立 AKS 叢集：

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

當您使用「可用性區域」建立 AKS 叢集時，適用下列限制：

* 您只能在建立叢集時啟用可用性區域。
* 建立叢集之後，即無法更新可用性區域設定。 您也無法將現有的非可用性區域叢集更新為使用可用性區域。
* 建立 AKS 叢集之後，您就無法停用其可用性區域。
* 選取的節點大小（VM SKU）必須可在所有可用性區域中使用。
* 啟用可用性區域的叢集需要使用 Azure 標準負載平衡器，以在區域間散發。
* 您必須使用 Kubernetes version 1.13.5 或更高版本，才能部署標準負載平衡器。

使用可用性區域的 AKS 叢集必須使用 Azure 負載平衡器*標準*SKU，這是負載平衡器類型的預設值。 此負載平衡器類型只能在叢集建立時定義。 如需標準負載平衡器的詳細資訊和限制，請參閱[Azure 負載平衡器標準 SKU 限制][standard-lb-limitations]。

### <a name="azure-disks-limitations"></a>Azure 磁片限制

使用 Azure 受控磁片的磁片區目前不是區域性資源。 在不同區域中，從其原始區域重新排定的 pod 無法重新附加其先前的磁片。 建議您執行無狀態工作負載，而不需要持續性儲存區，但可能會遇到跨區域性問題。

如果您必須執行可設定狀態的工作負載，請在 pod 規格中使用污點和容差，告訴 Kubernetes 排程器在與您的磁片相同的區域中建立 pod。 或者，使用以網路為基礎的存放裝置（例如 Azure 檔案儲存體），以在區域之間進行排程時連結至 pod。

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 叢集的可用性區域總覽

可用性區域是高可用性供應專案，可保護您的應用程式和資料不受資料中心失敗的影響。 區域是 Azure 區域內的唯一實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 某個地區內可用性區域的實體區隔可保護應用程式和資料不受資料中心故障影響。 區域冗余服務會跨可用性區域複寫您的應用程式和資料，以防止單一失敗點。

如需詳細資訊，請參閱[什麼是 Azure 中的可用性區域？][az-overview]。

使用可用性區域部署的 AKS 叢集可以在單一區域內的多個區域間散發節點。 例如， *美國東部 2* 區域中的叢集可以在*美國東部 2*的所有三個可用性區域中建立節點。 這種 AKS 叢集資源的散發可以改善叢集可用性，因為它們可以在特定區域失敗時復原。

![跨可用性區域的 AKS 節點散發](media/availability-zones/aks-availability-zones.png)

在區域中斷時，可以手動或使用叢集自動調整程式來重新平衡節點。 如果單一區域變得無法使用，您的應用程式會繼續執行。

## <a name="create-an-aks-cluster-across-availability-zones"></a>在可用性區域之間建立 AKS 叢集

當您使用[az aks create][az-aks-create]命令建立叢集時，`--zones` 參數會定義要部署到哪些區域的代理程式節點。 當您在叢集建立時定義 `--zones` 參數時，叢集的 AKS 控制平面元件也會分散到最高可用設定中的區域。

如果您在建立 AKS 叢集時未定義預設代理程式組件區的任何區域，則叢集的 AKS 控制平面元件將不會使用可用性區域。 您可以使用[az aks nodepool add][az-aks-nodepool-add]命令來新增其他節點集區，並指定這些新節點的 `--zones`，不過，控制平面元件會維持沒有可用性區域感知。 當節點集區或 AKS 控制平面元件部署完成之後，您就無法變更其區域感知。

下列範例會在名為*myResourceGroup*的資源群組中建立名為*myAKSCluster*的 AKS 叢集。 總共建立*3*個節點-一個是區域*1*中的代理程式，一個在*2*，另一個是*3*。 AKS 控制平面元件也會在最高可用設定的區域間散發，因為它們是在叢集建立程式中定義的。

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

當叢集準備就緒時，請列出擴展集中的代理程式節點，以查看其部署所在的可用性區域。

首先，使用[az AKS get-認證][az-aks-get-credentials]命令取得 AKS 叢集認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

接下來，使用 [ [kubectl 描述][kubectl-describe]] 命令來列出叢集中的節點。 篩選*failure-domain.Beta.kubernetes.io/zone*值，如下列範例所示：

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

下列範例輸出顯示分散于指定區域和可用性區域的三個節點，例如第一個可用性區域的*eastus2-1*和第二個可用性區域的*eastus2-2* 。

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

當您將其他節點新增至代理程式組件區時，Azure 平臺會自動將基礎 Vm 分散到指定的可用性區域。

請注意，在較新的 Kubernetes 版本（1.17.0 或和更新版本）中，除了已淘汰的 `failure-domain.beta.kubernetes.io/zone`之外，AKS 還會使用較新的標籤 `topology.kubernetes.io/zone`。

## <a name="verify-pod-distribution-across-zones"></a>確認跨區域的 pod 散發

如已知的[標籤、注釋和污點][kubectl-well_known_labels]中所述，Kubernetes 會使用 `failure-domain.beta.kubernetes.io/zone` 標籤，在不同的可用區域間自動散發複寫控制器或服務中的 pod。 若要進行測試，您可以從3個節點相應增加叢集，以確認正確的 pod 散佈：

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

當調整作業在幾分鐘後完成後，命令 `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` 應該會提供類似此範例的輸出：

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

如您所見，我們現在在區域1和2中有兩個額外的節點。 您可以部署由三個複本組成的應用程式。 我們將使用 NGINX，例如：

```console
kubectl run nginx --image=nginx --replicas=3
```

如果您確認 pod 執行所在的節點，您會看到 pod 在對應至三個不同可用性區域的 pod 上執行。 例如，使用命令 `kubectl describe pod | grep -e "^Name:" -e "^Node:"` 您會得到類似下面的輸出：

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

如您在先前的輸出中所見，第一個 pod 會在節點0上執行，此位置位於可用性區域 `eastus2-1`。 第二個 pod 在節點2上執行，其對應至 `eastus2-3`，而節點4中的第三個則 `eastus2-2`。 如果沒有任何額外的設定，Kubernetes 會在所有三個可用性區域中正確分配 pod。

## <a name="next-steps"></a>後續步驟

本文詳細說明如何建立使用可用性區域的 AKS 叢集。 如需高可用性叢集的詳細考慮，請參閱[AKS 中商務持續性和嚴重損壞修復的最佳做法][best-practices-bc-dr]。

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

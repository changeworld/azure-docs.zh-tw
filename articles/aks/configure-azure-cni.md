---
title: 在 Azure Kubernetes Service (AKS) 中設定 Azure CNI 網路
description: 了解如何在 Azure Kubernetes Service (AKS) 中設定 Azure CNI (進階) 網路，包括將 AKS 叢集部署到現有的虛擬網路和子網路。
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 58c2c597c7a75c801af91cd735561071250bda2c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000567"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中設定 Azure CNI 網路

根據預設，AKS 叢集會使用 [kubenet][kubenet]，並為您建立虛擬網路和子網路。 使用 *kubenet*，節點會從虛擬網路子網路取得 IP 位址。 接著會在節點上設定網路位址轉譯 (NAT)，而 Pod 會接收「隱藏」於節點 IP 後面的 IP 位址。 此方法可減少您需要在網路空間中保留，以供 Pod 使用的 IP 位址數目。

使用 [Azure 容器網路介面 (CNI)][cni-networking]，每個 Pod 都會從子網路取得 IP 位址，並且可以直接存取。 這些 IP 位址在您的網路空間中必須是唯一的，且必須事先規劃。 每個節點都有一個組態參數，用於所支援的最大 Pod 數目。 然後，為該節點預先保留每個節點的相同 IP 位址數目。 此方法需要更多規劃，並且通常會導致 IP 位址耗盡，或者隨著應用程式需求增加，需要在更大型子網路中重建叢集。

本文示範如何使用 *Azure CNI* 網路，針對 AKS 叢集建立和使用虛擬網路子網路。 如需網路選項與考量的詳細資訊，請參閱 [Kubernetes 和 AKS 的網路概念][aks-network-concepts]。

## <a name="prerequisites"></a>Prerequisites

* 適用於 AKS 叢集的虛擬網路必須允許輸出網際網路連線.
* AKS 叢集可能不會 `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` 針對 Kubernetes 服務位址範圍、pod 位址範圍或叢集虛擬網路位址範圍使用、、或。 
* AKS 叢集所使用的服務主體在您虛擬網路內的子網路上必須至少具有[網路參與者](../role-based-access-control/built-in-roles.md#network-contributor)權限。 如果您想要定義[自訂角色](../role-based-access-control/custom-roles.md)，而不使用內建的網路參與者角色，則需要下列權限：
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* 您可以使用系統指派的受控識別來取得許可權，而不是服務主體。 如需詳細資訊，請參閱[使用受控識別](use-managed-identity.md)。
* 指派給 AKS 節點集區的子網不能是 [委派的子網](../virtual-network/subnet-delegation-overview.md)。

## <a name="plan-ip-addressing-for-your-cluster"></a>規劃叢集的 IP 位址

使用 Azure CNI 網路設定的叢集需要進行額外的規劃。 您的虛擬網路及其子網路的大小必須配合您規劃要執行的 Pod 數目以及叢集的節點數目。

Pod 和叢集節點的 IP 位址會從虛擬網路內的指定子網路來指派。 每個節點都以主要 IP 位址進行設定。 根據預設，Azure CNI 預先設定的 30 個額外 IP 位址會指派給在節點上排程的 Pod。 當您將叢集相應放大時，每個節點同樣也會設定子網路中的 IP 位址。 您也可以檢視[每個節點的最大 Pod 數目](#maximum-pods-per-node)。

> [!IMPORTANT]
> 所需的 IP 位址數目應包含針對升級和調整作業考量的數目。 如果設定只支援固定節點數目的 IP 位址範圍，將無法升級或擴展您的叢集。
>
> - 當您 **升級** AKS 叢集時，會部署新的節點到叢集中。 服務和工作負載會開始在新的節點上執行，並移除叢集中較舊的節點。 此輪流升級程序需要至少一個額外的 IP 位址區塊以供使用。 因此您的節點計數為 `n + 1`。
>   - 當您使用 Windows Server 節點集區時，這項考慮特別重要。 AKS 中的 windows Server 節點不會自動套用 Windows Update，而是在節點集區上執行升級。 此升級會使用最新的 Window Server 2019 基礎節點映射和安全性修補程式來部署新的節點。 如需升級 Windows Server 節點集區的詳細資訊，請參閱 [在 AKS 中升級節點集][nodepool-upgrade]區。
>
> - 當您 **擴展** AKS 叢集時，會部署新的節點到叢集中。 服務和工作負載會開始在新的節點上執行。 您的 IP 位址範圍必須將您可能想要相應增加的節點數和您的叢集可支援的 Pod 數目納入考量。 也應包含一個額外的節點以用於升級作業。 因此您的節點計數為 `n + number-of-additional-scaled-nodes-you-anticipate + 1`。

如果預期您的節點會執行最大數目的 Pod，並定期終結及部署 Pod 時，也應該考慮為每個節點提供一些額外的 IP 位址。 這些納入考量的額外 IP 位址列可能需要幾秒鐘的時間刪除服務，並且為部署新服務而釋放 IP 地址並取得位址。

適用於 AKS 叢集的 IP 位址方案會由一個虛擬網路、至少一個適用於節點和 Pod 的子網路，以及一個 Kubernetes 服務位址範圍所組成。

| 位址範圍 / Azure 資源 | 限制和調整大小 |
| --------- | ------------- |
| 虛擬網路 | Azure 虛擬網路可以和 /8 一樣大，但可能只有 65,536 個已設定的 IP 位址。 在設定您的位址空間之前，請考慮您所有的網路需求，包括與其他虛擬網路中的服務進行通訊。 例如，如果您設定過大的位址空間，您可能會遇到網路中其他位址空間重迭的問題。|
| 子網路 | 必須大到足以容納節點、Pod，以及可能會在您叢集中佈建的所有 Kubernetes 和 Azure 資源。 例如，如果您部署內部 Azure Load Balancer，其前端 IP 會從叢集子網路配置，而不是從公用 IP 配置。 子網路大小也應該考量帳戶升級作業或未來的擴展需求。<p />若要計算包括用於升級作業之額外節點的 *最小* 子網路大小：`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>50 個節點叢集的範例：`(51) + (51  * 30 (default)) = 1,581` (/21 或更大)<p/>擁有 50 個節點的叢集範例，其中也包含相應增加額外 10 個節點的佈建：`(61) + (61 * 30 (default)) = 1,891` (/21 或更大)<p>如果您未指定每個節點的最大 Pod 數目，當您建立叢集時，每個節點的最大 Pod 數目設定為 30。 IP 位址所需的最小數目是根據該值。 如果您以不同的最大值來計算最小 IP 位址需求，請參閱[如何設定每個節點的最大 Pod 數目](#configure-maximum---new-clusters)，在您部署叢集時設定此值。 |
| Kubernetes 服務位址範圍 | 此範圍不應由此虛擬網路上或連線到此虛擬網路的任何網路元素所使用。 服務位址 CIDR 必須小於 /12。 您可以跨不同的 AKS 叢集重複使用這個範圍。 |
| Kubernetes DNS 服務 IP 位址 | 叢集服務探索將使用的 Kubernetes 服務位址範圍內的 IP 位址。 請勿使用您位址範圍中的第一個 IP 位址，例如 .1。 您子網路範圍內的第一個位址會用於 kubernetes.default.svc.cluster.local 位址。 |
| Docker 橋接器位址 | Docker 橋接器網路位址代表存在於所有 Docker 安裝中的預設 *docker0* 橋接器網路位址。 雖然 AKS 叢集或 pod 本身不會使用 *docker0* 橋接器，但您必須設定此位址，才能繼續支援 AKS 叢集中的 *docker 組建* 等案例。 您必須為 Docker 橋接器網路位址選取 CIDR，因為否則 Docker 會自動挑選可能與其他 Cidr 衝突的子網。 您必須挑選的位址空間不會與網路上的其餘 Cidr 衝突，包括叢集的服務 CIDR 和 pod CIDR。 預設值為 172.17.0.1/16。 您可以跨不同的 AKS 叢集重複使用這個範圍。 |

## <a name="maximum-pods-per-node"></a>每個節點的最大 Pod 數目

AKS 叢集中每個節點的最大 pod 數目是250。 每個節點「預設」的最大 Pod 數目，會根據 *kubenet* 和 *Azure CNI* 網路以及叢集部署的方法而有所不同。

| 部署方法 | Kubenet 預設值 | Azure CNI 預設值 | 可在部署時設定 |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | 是 (高達 250)  |
| Resource Manager 範本 | 110 | 30 | 是 (高達 250)  |
| 入口網站 | 110 | 30 | 否 |

### <a name="configure-maximum---new-clusters"></a>設定最大值 - 新叢集

您可以在叢集部署期間，或在新增節點集區時，設定每個節點的 pod 數目上限。 如果您使用 Azure CLI 或使用 Resource Manager 範本進行部署，則可以將每個節點的最大 pod 值設定為最高250。

如果您在建立新的節點集區時未指定 maxPods，您會收到 Azure CNI 的預設值30。

系統會強制執行每個節點的最大 pod 數目的最小值，以保證叢集健全狀況的重要系統 pod 空間。 只有在每個節點集區的設定至少有30個 pod 的空間時，可為每個節點的最大 pod 設定的最小值為10。 例如，將每個節點的最大 pod 數設定為最小值10，需要每個個別節點集區至少有3個節點。 這項需求也適用于每個建立的新節點集區，因此，如果將10定義為每個節點的最大 pod 數目，每個新增的節點集區都必須至少有3個節點。

| 網路 | 最小值 | 最大值 |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> 上表中的最小值是由 AKS 服務嚴格強制執行。 您無法將 maxPods 值設定為低於顯示的最小值，因為這樣會導致叢集無法啟動。

* **Azure CLI**：當您使用 [az aks create][az-aks-create] 命令部署叢集時，請指定 `--max-pods` 引數。 最大值為250。
* **Resource Manager 範本**：當您使用 Resource Manager 範本部署叢集時，請指定 [ManagedClusterAgentPoolProfile] 物件中的 `maxPods` 屬性。 最大值為250。
* **Azure 入口網站**：當您使用 Azure 入口網站部署叢集時，您無法變更每一節點的 Pod 數目上限。 使用 Azure 入口網站進行部署時，Azure CNI 網路叢集限制為每一節點 30 個 Pod。

### <a name="configure-maximum---existing-clusters"></a>設定最大值 - 現有叢集

當您建立新的節點集區時，可以定義每個節點的 maxPod 設定。 如果您需要增加現有叢集上每個節點的 maxPod 設定，請使用新的所需 maxPod 計數來新增節點集區。 將 pod 遷移至新的集區之後，請刪除較舊的集區。 若要刪除叢集中的任何較舊的集區，請確定您正在設定 [ [系統節點][system-node-pools]集區] 檔中定義的節點集區模式。

## <a name="deployment-parameters"></a>部署參數

當您建立 AKS 叢集時，可針對 Azure CNI 網路設定下列參數：

**虛擬網路**：要作為 Kubernetes 叢集部署目的地的虛擬網路。 如果您要為叢集建立新的虛擬網路，請選取 [新建] 並遵循＜建立虛擬網路＞一節中的步驟。 如需有關 Azure 虛擬網路限制和配額的資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)。

**子網路**：虛擬網路內要用來部署叢集的子網路。 如果您要在虛擬網路中為叢集建立新的子網路，請選取 [新建] 並遵循＜建立子網路＞一節中的步驟。 混合式連線的位址範圍不應該與您環境中的任何其他虛擬網路重疊。

**Kubernetes 服務位址範圍**：這是 Kubernetes 指派給您叢集中內部 [服務][services] 的一組虛擬 ip。 您可以使用任何符合下列需求的私人位址範圍：

* 不得在叢集的虛擬網路 IP 位址範圍內
* 不得與叢集虛擬網路對等的任何其他虛擬網路重疊
* 不得與任何內部部署 IP 重疊
* 不得在範圍 `169.254.0.0/16` 、 `172.30.0.0/16` 、 `172.31.0.0/16` 或 `192.0.2.0/24`

雖然技術上有可能指定與您叢集相同虛擬網路內的服務位址範圍，但不建議這麼做。 如果使用重疊的 IP 範圍，就會造成無法預期的行為。 如需詳細資訊，請參閱本文的[常見問題集](#frequently-asked-questions)一節。 如需有關 Kubernetes 服務的詳細資訊，請參閱 Kubernetes 文件中的[服務][services]。

**Kubernetes DNS 服務 IP 位址**：叢集 DNS 服務的 IP 位址。 此位址必須位於 *Kubernetes 服務位址範圍* 中。 請勿使用您位址範圍中的第一個 IP 位址，例如 .1。 您子網路範圍內的第一個位址會用於 kubernetes.default.svc.cluster.local 位址。

**Docker 橋接器位址**： docker 橋接器網路位址代表存在於所有 Docker 安裝中的預設 *docker0* 橋接器網路位址。 雖然 AKS 叢集或 pod 本身不會使用 *docker0* 橋接器，但您必須設定此位址，才能繼續支援 AKS 叢集中的 *docker 組建* 等案例。 您必須為 Docker 橋接器網路位址選取 CIDR，因為否則 Docker 會自動挑選可能與其他 Cidr 衝突的子網。 您必須挑選的位址空間不會與網路上的其餘 Cidr 衝突，包括叢集的服務 CIDR 和 pod CIDR。

## <a name="configure-networking---cli"></a>設定網路功能 - CLI

當您使用 Azure CLI 來建立 AKS 叢集時，也可以設定 Azure CLI 網路。 使用下列命令來建立新的 AKS 叢集，並啟用 Azure CNI 網路。

首先，針對將聯結 AKS 叢集的現有子網路取得子網路資源識別碼：

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

使用 [az aks create][az-aks-create] 命令搭配 `--network-plugin azure` 參數來建立具備進階網路功能的叢集。 使用在上一個步驟中收集的子網路識別碼來更新 `--vnet-subnet-id` 值：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>設定網路功能 - 入口網站

下列擷取自 Azure 入口網站的螢幕擷取畫面，會顯示 AKS 叢集建立期間對這些設定進行配置的範例：

![Azure 入口網站中的進階網路組態][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>常見問題集

下列問題和解答適用於 **Azure CNI** 網路設定。

* 是否可以在叢集子網路中部署 VM？

  是。

* *外部系統的哪些來源 IP 適用于源自啟用 Azure CNI 之 pod 的流量？*

  在與 AKS 叢集相同的虛擬網路中，系統會看到 pod IP 作為來自 pod 的任何流量來源位址。 AKS 叢集虛擬網路外的系統會將節點 IP 視為來自 pod 的任何流量的來源位址。 

* *可以設定每個 pod 的網路原則嗎？*

  是，AKS 中有提供 Kubernetes 網路原則。 若要開始使用，請參閱 [在 AKS 中使用網路原則來保護 pod 之間的流量][network-policy]。

* 是否可以設定可部署到節點的 Pod 數目上限？

  是，當您使用 Azure CLI 或 Resource Manager 範本部署叢集時。 請參閱[每個節點的最大 Pod 數目](#maximum-pods-per-node)。

  您無法在現叢集上變更每個節點的 Pod 數目上限。

* *如何? 設定在建立 AKS 叢集時所建立之子網的其他屬性？例如，服務端點。*

  在 AKS 叢集建立期間所建立的虛擬網路和子網路屬性完整清單，均可在 Azure 入口網站的標準虛擬網路組態頁面中進行設定。

* *可以使用叢集虛擬網路內的不同子網路作為* **Kubernetes 服務位址範圍** 嗎？

  不建議，但此組態是可行的。 服務位址範圍是 Kubernetes 指派給您叢集中內部服務的一組虛擬 IP (VIP)。 Azure 網路功能無法查看 Kubernetes 叢集的服務 IP 範圍。 因為無法查看叢集的服務位址範圍，所以稍後有可能在與服務位址範圍重疊的叢集虛擬網路中建立新的子網路。 如果發生這類重疊，Kubernetes 可能會將子網路中另一項資源已經使用的 IP 指派給服務，因而造成無法預期的行為或失敗。 您可藉由確保您使用叢集虛擬網路外部的位址範圍，避免此重疊風險。

## <a name="next-steps"></a>後續步驟

在下列文章中深入了解 AKS 的網路功能：

- [搭配 Azure Kubernetes Service (AKS) 負載平衡器使用靜態 IP 位址](static-ip.md)
- [搭配 Azure Container Service (AKS) 使用內部負載平衡器](internal-lb.md)

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- [使用動態公用 IP 建立輸入控制器，並設定 Let's Encrypt 以自動產生 TLS 憑證][aks-ingress-tls]
- [使用靜態公用 IP 建立輸入控制器，並設定 Let's Encrypt 以自動產生 TLS 憑證][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS 引擎

[Azure Kubernetes Service 引擎 (AKS 引擎)][aks-engine] 是一個開放原始碼專案，會產生 Azure Resource Manager 範本供您用於在 Azure 上部署 Kubernetes 叢集。

使用 AKS 引擎所建立的 Kubernetes 叢集同時支援 [kubenet][kubenet] 和 [Azure CNI][cni-networking] 外掛程式。 因此，這兩個網路案例都會受到 AKS 引擎支援。

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md

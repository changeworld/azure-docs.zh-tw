---
title: 使用標準 SKU 負載平衡器
titleSuffix: Azure Kubernetes Service
description: 了解如何使用標準 SKU 的負載平衡器，透過 Azure Kubernetes Service (AKS) 公開您的服務。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 14e80f6348772af77c5a53b1d5e9111c4ae8ba9b
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402059"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用標準 SKU 負載平衡器

若要在 Azure Kubernetes Service (AKS) 中透過類型 `LoadBalancer` 的 Kubernetes 服務提供應用程式的存取權，則可使用 Azure Load Balancer。 在 AKS 上執行的負載平衡器可做為內部或外部負載平衡器使用。 使用內部負載平衡器，即可僅限執行時所在虛擬網路與 AKS 叢集相同的應用程式方能存取 Kubernetes 服務。 外部負載平衡器會接收一個或多個公用 IP 以供輸入，並使用公用 IP 讓 Kubernetes 服務可供外部存取。

Azure Load Balancer 有兩種 SKU -「基本」和「標準」。 根據預設，您建立 AKS 叢集時，會使用「標準」SKU。 使用「標準」SKU 負載平衡器可提供額外的特性和功能，例如後端集區大小和可用性區域較大。 請務必先瞭解「標準」與「基本」負載平衡器之間的差異，再選擇要使用哪一種。 建立 AKS 叢集之後，就無法變更該叢集的負載平衡器 SKU。 如需「基本」和「標準」SKU 的詳細資訊，請參閱 [Azure Load Balancer SKU][azure-lb-comparison]。

本文假設您對 Kubernetes 和 Azure Load Balancer 概念有基本瞭解。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts] 和[什麼是 Azure Load Balancer？][azure-lb]。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇本機安裝並使用 CLI，本文會要求您執行 Azure CLI 2.0.81 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>開始之前

本文假設您的 AKS 叢集使用的是「標準」SKU Azure Load Balancer。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

如果您使用現有的子網路或資源群組，則 AKS 叢集服務主體也需要網路資源的管理權限。 一般來說，請將「網路參與者」角色指派給委派資源上的服務主體。 您也可以使用系統指派的受控識別來取得權限，取代服務主體。 如需詳細資訊，請參閱[使用受控識別](use-managed-identity.md)。 如需權限的詳細資訊，請參閱[將 AKS 存取權委派給其他 Azure 資源][aks-sp]。

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>從基本 SKU 負載平衡器移至標準 SKU

如果您現有的叢集使用的是基本 SKU 負載平衡器，而想要進行遷移以便使用具有標準 SKU 負載平衡器的叢集，需要留意一些重要的行為差異。

舉例來說，常見的一種做法是進行藍色/綠色部署來遷移叢集，因為只能在叢集建立時定義叢集的 `load-balancer-sku` 類型。 不過，「基本 SKU」負載平衡器會使用「基本 SKU」的 IP 位址，而此 IP 位址與「標準 SKU」負載平衡器不相容，因為標準 SKU 負載平衡器需要的是「標準 SKU」IP 位址。 為了升級 Load Balancer SKU 而遷移叢集時，新 IP 位址需要具有相容的 IP 位址 SKU。

如需如何遷移叢集的詳細考量，請參閱[我們的遷移考量文件](aks-migration.md)，查看遷移時需要注意的重要主題清單。 在 AKS 中使用標準 SKU 負載平衡器時，以下限制也是需要留意的重要行為差異。

### <a name="limitations"></a>限制

您建立和管理可支援「標準」SKU 負載平衡器的 AKS 叢集時，需遵守下列限制：

* 必須至少有一個公用 IP 或 IP 首碼，才能允許來自 AKS 叢集的輸出流量。 此外，也需要公用 IP 或 IP 首碼，以維持平面和代理程式節點之間的連線能力，同時維持與舊版 AKS 的相容性。 可使用下列選項來指定「標準」SKU 負載平衡器的公用 IP 或 IP 首碼：
    * 提供您自己的公用 IP。
    * 提供您自己的公用 IP 首碼。
    * 指定一個 100 以內的數字，以允許 AKS 叢集在與 AKS 叢集相同的資源群組中建立相同數量的「標準」SKU 公用 IP，AKS 叢集的名稱通常是以「MC_」開頭。 AKS 會將公用 IP 指派給「標準」SKU 負載平衡器。 根據預設，如果未指定公用 IP、公用 IP 首碼或 IP 數目，則會在與 AKS 叢集相同的資源群組中自動建立一個公用 IP。 請務必允許公用位址，並避免建立任何禁止 IP 建立的 Azure 原則。
* 使用負載平衡器的「標準」SKU 時，您必須使用 Kubernetes 1.13 版或更高版本。
* 只有在建立 AKS 叢集時，才可以定義負載平衡器 SKU。 建立 AKS 叢集之後，就無法變更負載平衡器 SKU。
* 在單一叢集中，只能使用一種類型的負載平衡器 SKU (基本或標準)。
* 「標準」SKU 負載平衡器只支援「標準」SKU IP 位址。

## <a name="use-the-standard-sku-load-balancer"></a>使用「標準」SKU 負載平衡器

在建立 AKS 叢集的情況下，根據預設，您在該叢集中執行服務時，會使用「標準」SKU 負載平衡器。 例如，[使用 Azure CLI 的快速入門][aks-quickstart-cli]會部署使用「標準」SKU 負載平衡器的範例應用程式。

> [!IMPORTANT]
> 藉由自訂使用者定義的路由 (UDR)，可以避免公用 IP 位址。 將 AKS 叢集的輸出類型指定為 UDR，可跳過 AKS 所建 Azure 負載平衡器略的 IP 佈建和後端集區設定。 請參閱[將叢集的 `outboundType` 設定為「userDefinedRouting」](egress-outboundtype.md)。

## <a name="configure-the-load-balancer-to-be-internal"></a>將負載平衡器設定為內部

您也可以將負載平衡器設定為內部，而不公開公用 IP。 若要將負載平衡器設定為內部，請將 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` 新增為 LoadBalancer 服務的註釋。 您可以在[這裡][internal-lb-yaml]查看範例 yaml 資訊清單，以及有關內部負載平衡器的詳細資料。

## <a name="scale-the-number-of-managed-public-ips"></a>調整受控公用 IP 的數目

使用「標準」SKU 負載平衡器搭配預設建立的受控輸出公用 IP 時，可使用 load-balancer-managed-ip-count參數調整受控輸出公用 IP 的數目。

若要更新現有的叢集，請執行下列命令。 也可以在叢集建立時設定此參數，即可擁有多個受控的輸出公用 IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

對於 myResourceGroup 中的 myAKSCluster 叢集，上述範例會將受控輸出公用 IP 的數目設定為「2」。 

也可以在建立叢集時使用 load-balancer-managed-ip-count 參數，只需附加 `--load-balancer-managed-outbound-ip-count` 參數並將之設定為想要的值，就能設定受控輸出公用 IP 的初始數目。 受控輸出公用 IP 的預設數目為 1。

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>提供您自己的公用 IP 或首碼以供輸出

使用「標準」SKU 負載平衡器時，AKS 叢集會自動在為了 AKS 叢集所建立的同個資源群組中建立公用 IP，並將公用 IP 指派給「標準」SKU 負載平衡器。 或者，您也可以在建立叢集時指派自己的公用 IP，或更新現有叢集的負載平衡器屬性。

帶入多個 IP 位址或首碼，在定義單一負載平衡器物件後方的 IP 位址時，即可定義多個支援服務。 特定節點的輸出端點取決於與這些端點建立關聯的服務。

### <a name="pre-requisites-to-bring-your-own-ip-addresses-or-ip-prefixes"></a>帶入自己的 IP 位址或 IP 首碼的先決條件
1. 請務必使用「標準」SKU 公用 IP，透過您的「標準」SKU 負載平衡器進行輸出。 您可以使用 [az network public-ip show][az-network-public-ip-show] 命令來驗證公用 IP 的 SKU：

   ```azurecli-interactive
   az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
   ```
 1. 公用 IP 和 IP 首碼必須位於與您的 AKS 叢集相同的區域中，並且屬於相同的訂訂閱。
 1. 公用 IP 和 IP 首碼不能是 AKS 建立做為受控 IP 使用的 IP。 請確定指定做為自訂 IP 的任何 IP 皆是手動建立，而非使用 AKS 服務。
 1. 公用 IP 和 IP 首碼無法供另一項資源或服務使用。

 ### <a name="define-your-own-public-ip-or-prefixes-on-an-existing-cluster"></a>在現有叢集上定義您自己的公用 IP 或首碼

使用 [az network public-ip show][az-network-public-ip-show] 命令列出公用 IP 的識別碼。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上述命令會顯示 myResourceGroup 資源群組中的 myPublicIP 公用 IP 所用的識別碼。

請使用 az aks update 命令搭配 load-balancer-outbound-ips 參數，使用您的公用 IP 更新您的叢集。

下列範例使用的是 load-balancer-outbound-ips 參數，搭配前一個命令的識別碼。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

您也可以使用輸出的公用 IP 首碼搭配您的「標準」SKU 負載平衡器。 下列範例使用 [az network public-ip prefix show][az-network-public-ip-prefix-show] 命令列出公用 IP 首碼的識別碼：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上述命令會顯示 myResourceGroup 資源群組中的 myPublicIPPrefix 公用 IP 首碼所用的識別碼。

下列範例會使用 load-balancer-outbound-ip-prefixes 參數搭配前一個命令的識別碼。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>在叢集建立時定義自己的公用 IP 或首碼

在叢集建立時，您可能會想要對於輸出帶入您自己的 IP 位址或 IP 首碼，以支援白名單輸出端點之類的情況。 將上面顯示的相同參數附加至叢集建立步驟，以便在叢集生命週期開始時定義您自己的公用 Ip 和 IP 首碼。

使用 az aks create 命令搭配 load-balancer-outbound-ips 參數，以您的公用 IP 在開始時建立新的叢集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

使用 az aks create 命令搭配 load-balancer-outbound-ip-prefixes 參數，以您的公用 IP 首碼在開始時建立新的叢集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>設定輸出連接埠和閒置逾時

> [!WARNING]
> 下一節適用於較大規模網路連線的進階使用情境，或用於透過預設設定處理 SNAT 耗盡問題。 請務必先正確清查 VM 和 IP 位址的可用配額，再變更 AllocatedOutboundPorts 或 IdleTimeoutInMinutes 的預設值，才能讓叢集狀態維持良好。
> 
> 改變 AllocatedOutboundPorts 和 IdleTimeoutInMinutes 的值，可能會大幅變更負載平衡器輸出規則的行為。 請參閱 [Load Balancer 輸出規則][azure-lb-outbound-rules-overview]、[負載平衡器輸出規則][azure-lb-outbound-rules]和 [Azure 中的輸出連線][azure-lb-outbound-connections]，再更新這些值，以充分瞭解變更的影響。

輸出配置連接埠及其閒置逾時會用於 [SNAT][azure-lb-outbound-connections]。 根據預設，「標準」SKU 負載平衡器會[根據後端集區大小自動指派輸出連接埠數目][azure-lb-outbound-preallocatedports]，並對每個連接埠使用 30 分鐘的閒置逾時。 若要查看這些值，請使用 [az network lb outbound-rule list][az-network-lb-outbound-rule-list] 顯示負載平衡器的輸出規則：

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

先前的命令會列出負載平衡器的輸出規則，例如：

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

範例輸出結果會顯示 AllocatedOutboundPorts 和 IdleTimeoutInMinutes 的預設值。 AllocatedOutboundPorts 的 0 值會根據後端集區大小，使用自動指派的輸出連接埠數目來設定輸出連接埠的數目。 例如，如果叢集有 50 個或更少的節點，則會對每個節點配置 1024 個連接埠。

如果預期根據上述預設設定會出現 SNAT 耗盡的情況，請考慮變更 allocatedOutboundPorts 或 IdleTimeoutInMinutes 的設定。 每個額外的 IP 位址會啟用 64,000 個額外連接埠以供配置，不過，新增更多 IP 位址時，Azure Standard Load Balancer 不會自動增加每個節點的連接埠。 可設定 load-balancer-outbound-ports 和 load-balancer-idle-timeout 參數變更這些值。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 您必須先[計算所需的配額][calculate-required-quota]，再自訂 allocatedOutboundPorts，以避免出現連線能力或調整方面的問題。 指定給 allocatedOutboundPorts 的值也必須是 8 的倍數。

建立叢集時，也可以使用 load-balancer-outbound-ports 和 load-balancer-idle-timeout 參數，但也必須指定 load-balancer-managed-outbound-ip-count、load-balancer-outbound-ips 或 load-balancer-outbound-ip-prefixes。  例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

變更 load-balancer-outbound-ports 和 load-balancer-idle-timeout 參數預設值時，會影響負載平衡器設定檔的行為，因而影響整個叢集。

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>自訂 allocatedOutboundPorts 所需的配額
根據您的節點 VM 數目和所需的配置連出連接埠，您必須有足夠的連出 IP 容量。 若要確認是否有足夠的輸出 IP 容量，請使用下列公式： 
 
outboundIPs \* 64,000 \> nodeVMs \* *desiredAllocatedOutboundPorts*.
 
例如，如果您有 3 個 nodeVMs 和 50,000 個 desiredAllocatedOutboundPorts，您至少必須有 3 個 outboundIPs。 建議納入額外的輸出 IP 容量，超過所需範圍外。 此外，計算輸出 IP 容量時，必須考慮叢集自動調整程式，以及節點集區升級的可能性。 針對叢集自動調整程式，請檢查目前的節點計數和最大節點計數，並使用較高的值。 若要進行升級，請針對允許升級的每個節點集區考慮其他節點 VM。
 
將 IdleTimeoutInMinutes 設定為不同於預設 30 分鐘的值時，請考慮您的工作負載需要輸出連線的時間長度。 也請考慮在 AKS 以外使用「標準」SKU 負載平衡器的預設逾時值為 4 分鐘。 IdleTimeoutInMinutes 值，可以更精確反映您的特定 AKS 工作負載，有助於減少佔用不再使用的連線而造成的 SNAT 耗盡。

## <a name="restrict-access-to-specific-ip-ranges"></a>限制對特定 IP 範圍的存取

根據預設，與負載平衡器的虛擬網路相關聯的網路安全性群組 (NSG) 具有允許所有輸入外部流量的規則。 您可以將此規則更新為只允許輸入流量的特定 IP 範圍。 下列資訊清單會使用 loadBalancerSourceRanges 指定輸入外部流量的新 IP 範圍：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

上述範例會將規則更新為只允許來自 MY_EXTERNAL_IP_RANGE 範圍的輸入外部流量。 如需使用此方法限制負載平衡器服務存取的詳細資訊，請參閱 [Kubernetes 文件][kubernetes-cloud-provider-firewall]。

## <a name="next-steps"></a>後續步驟

在 [Kubernetes 服務文件][kubernetes-services]中深入了解 Kubernetes 服務。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports

---
title: 使用標準 SKU 負載均衡器
titleSuffix: Azure Kubernetes Service
description: 瞭解如何使用具有標準 SKU 的負載均衡器使用 Azure 庫伯奈斯服務 (AKS) 公開服務。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 17e474de9c221126d67cc2982ba11c6ff75e7aa3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668501"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>在 Azure 庫伯奈斯服務 (AKS) 中使用標準 SKU 負載均衡器

要透過 Azure 庫伯奈斯服務 (AKS) 中的 Kubernetes 類型的`LoadBalancer`服務提供對應用程式的訪問,可以使用 Azure 負載均衡器。 在 AKS 上執行的負載均衡器可用作內部負載均衡器或外部負載均衡器。 內部負載均衡器使 Kubernetes 服務只能對在 AKS 群集相同的虛擬網路中運行的應用程序訪問。 外部負載均衡器接收一個或多個用於入口的公共 IP,並使 Kubernetes 服務使用公共 IP 在外部訪問。

Azure Load Balancer 有兩種 SKU -「基本」** 和「標準」**。 默認情況下,在創建 AKS 群集時使用*標準*SKU。 使用*標準*SKU 負載均衡器可提供其他特性和功能,例如更大的後端池大小和可用性區域。 在選擇使用哪種負載之前,請務必了解*標準*負載均衡器和*基本*負載均衡器之間的差異。 創建 AKS 群集後,無法更改該群集的負載均衡器 SKU。 有關*基本*和*標準*SKU 的詳細資訊,請參閱[Azure 負載均衡器 SKU 比較][azure-lb-comparison]。

本文假定對庫伯奈斯和 Azure 負載均衡器概念的基本理解。 有關詳細資訊,請參閱[Azure 庫伯奈斯服務 (AKS) 的核心概念][kubernetes-concepts]和什麼是[Azure 負載均衡器?][azure-lb]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇在本地安裝和使用 CLI,則本文要求您運行 Azure CLI 版本 2.0.81 或更高版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>開始之前

本文假定您具有具有*標準*SKU Azure 負載均衡器的 AKS 群集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

如果使用現有子網或資源組,AKS 群集服務主體還需要管理網路資源的許可權。 通常,在委派的資源上將*網路參與者*角色分配給您的服務主體。 有關權限的詳細資訊,請參閱將[AKS 存取委託給其他 Azure 資源][aks-sp]。

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>從基本 SKU 負載均衡器遷移到標準 SKU

如果現有群集具有基本 SKU 負載均衡器,則在遷移以使用標準 SKU 負載均衡器時,需要注意重要的行為差異。

例如,將藍色/綠色部署用於遷移群集是一種常見做法,`load-balancer-sku`因為群集的類型只能在群集創建時定義。 但是,*基本 SKU*負載均衡器使用*基本 SKU* IP 位址,這些位址與*標準 SKU*負載均衡器不相容,因為它們需要*標準 SKU* IP 位址。 遷移群集以升級負載均衡器 SKU 時,將需要具有相容 IP 位址 SKU 的新 IP 位址。

有關如何遷移群集的更多注意事項,請造訪[有關遷移注意事項的文檔](aks-migration.md),查看遷移時要考慮的重要主題清單。 在 AKS 中使用標準 SKU 負載均衡器時,以下限制也是需要注意的重要行為差異。

### <a name="limitations"></a>限制

當您使用*標準*SKU 建立與管理支援負載均衡器的 AKS 叢集時,以下限制適用:

* 允許從 AKS 群集傳出流量至少需要一個公共 IP 或 IP 首碼。 公共 IP 或 IP 首碼還需要維護控制平面和代理節點之間的連接,以及保持與早期版本的 AKS 的相容性。 具有以下選項,用於使用*標準*SKU 負載均衡器指定公共 IP 或 IP 首碼:
    * 提供您自己的公共 IP。
    * 提供您自己的公共 IP 首碼。
    * 指定最多 100 個數位,允許 AKS 群集建立與 AKS 群集相同的資源組中的許多*標準*SKU 公共 IP,該群集通常在開始時以*MC_* 命名。 AKS 將公共 IP 分配給*標準*SKU 負載均衡器。 預設情況下,如果未指定公共 IP、公共 IP 首碼或 IP 數量,則將自動在與 AKS 群集相同的資源組中創建一個公共 IP。 您還必須允許公共位址,並避免創建任何禁止創建 IP 的 Azure 策略。
* 將*標準*SKU 用於負載均衡器時,必須使用 Kubernetes 版本*1.13 或更高*版本。
* 僅當創建 AKS 群集時,才能定義負載均衡器 SKU。 創建 AKS 群集後,無法更改負載均衡器 SKU。
* 只能在單個群集中使用一種類型的負載均衡器 SKU(基本或標準)。
* *標準*SKU 負載均衡器僅支援*標準*SKU IP 位址。

## <a name="use-the-standard-sku-load-balancer"></a>使用*標準*SKU 負載均衡器

默認情況下,當您創建 AKS 群集時,在群集中運行服務時將使用*標準*SKU 負載均衡器。 例如[,使用 Azure CLI 的快速入門][aks-quickstart-cli]部署使用*標準*SKU 負載均衡器的範例應用程式。

> [!IMPORTANT]
> 透過自訂使用者定義的路由 (UDR), 可以避免公共 IP 位址。 將 AKS 群集的出站類型指定為 UDR 可以跳過 AKS 創建的 Azure 負載均衡器的 IP 預先和後端池設定。 請參閱[將群`outboundType`集 設置為「使用者定義路由」。](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>將負載均衡器設定為內部

您還可以將負載均衡器配置為內部,而不是公開公共 IP。 要將負載均衡器配置為內部,請添加`service.beta.kubernetes.io/azure-load-balancer-internal: "true"`為*Load平衡器*服務的註釋。 你可以[在這裡][internal-lb-yaml]看到一個示例 yaml 清單以及有關內部負載均衡器的更多詳細資訊。

## <a name="scale-the-number-of-managed-public-ips"></a>擴充託管公共 IP 的數量

在預設情況下創建的託管出站公共 IP 中使用*標準*SKU 負載均衡器時,可以使用*負載均衡器託管 ip 計數*參數縮放託管出站公共 IP 的數量。

要更新現有群集,請運行以下命令。 此參數也可以設置為群集創建時間,以具有多個託管出站公共 IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

上述示例將*myAKSCluster 群集*的託管出站公共 IP*myResourceGroup*數設置為*2。* 

您還可以使用*負載均衡器管理 ip 計數*參數通過在創建群集時設置託管出站公共 IP 的`--load-balancer-managed-outbound-ip-count`初始數量,從而附加 參數並將其設置為所需的值。 託管出站公共 IP 的預設數為 1。

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>為出口提供您自己的公共 IP 或前置碼

使用*標準*SKU 負載均衡器時,AKS 群集會自動在為 AKS 群集創建的同一資源組中創建公共 IP,並將公共 IP 分配給*標準*SKU 負載均衡器。 或者,您可以在群集創建時分配自己的公共 IP,也可以更新現有群集的負載均衡器屬性。

以引入多個 IP 位址或前置碼,您可以在在單個負載均衡器物件後面定義 IP 位址時定義多個支援服務。 特定節點的出入口終結點將取決於它們與哪些服務相關聯。

> [!IMPORTANT]
> 您必須使用*標準*SKU 公共 IP 與*您的標準*SKU 負載均衡器一起出口。 您可以使用[az 網路公共 ip 顯示][az-network-public-ip-show]命令驗證公共 IP 的 SKU:
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

使用[az 網路公共 ip 顯示][az-network-public-ip-show]命令列出公共 IP 的 IP 的 IP 的 IP。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上述命令顯示*myPublicIP 公共*IP 在*myResourceGroup*資源組中的 ID。

使用具有*負載均衡器-出站 ips*參數的*az aks 更新*命令使用公共 IP 更新群集。

下面的範例使用*負載均衡器-出站ips*參數與上一個命令中的IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

您還可以使用公共 IP 首碼進行*與標準*SKU 負載均衡器一起出口。 下面的範例使用[az 網路公共 ip 首碼顯示][az-network-public-ip-prefix-show]命令列出公共 IP 首碼的 ID:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上述命令顯示*myForipIpPrefix*公共 IP 首碼在*myResourceGroup*資源組中的 ID。

下面的範例使用*負載均衡器-出站ip首碼參數*與上一個命令中的"指示"一起。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 公共 IP 和 IP 首碼必須與 AKS 群集位於同一區域和同一訂閱的一部分。 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>在叢集建立時定義您自己的公共 IP 或前置字串

您可能希望在群集創建時為出口引入自己的 IP 位址或 IP 首碼,以支援諸如白名單出口終結點等方案。 將上面顯示的相同參數追加到群集創建步驟中,以在群集生命週期開始時定義您自己的公共 IP 和 IP 首碼。

使用*az aks 創建*命令與*負載均衡器-出站 ips*參數一起創建具有公共 IP 的新群集。

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

使用*az aks 創建*命令與*負載均衡器-出站 ip 首碼參數*一起創建具有公共 IP 首碼的新群集。

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>設定出站埠和空閒逾時

> [!WARNING]
> 以下部分適用於大規模網路的先進方案或用於解決預設配置的 SNAT 耗盡問題。 在將*配置的出站埠*或*IdleTimeoutInM*從預設值更改之前,您必須準確列出 VM 和 IP 位址的可用配額,以便維護健康的群集。
> 
> 更改*分配的出站埠*和*IdleTimeoutInM 值*可能會顯著更改負載均衡器的出站規則的行為。 在更新這些值之前,請查看 Azure 中的[負載均衡器出站規則][azure-lb-outbound-rules-overview]、[負載均衡器出站規則][azure-lb-outbound-rules]和[出站連接][azure-lb-outbound-connections],以充分瞭解更改的影響。

出站配置的連接埠及其空閒超時用於[SNAT][azure-lb-outbound-connections]。 預設情況下,*標準*SKU 負載均衡器[根據後端池大小對出站埠數使用自動分配][azure-lb-outbound-preallocatedports],每個埠的空閒超時為 30 分鐘。 要檢視這些值,請使用[az 網路 lb 出站規則清單][az-network-lb-outbound-rule-list]來顯示負載均衡器的出站規則:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

前面的指令將列出負載均衡器的出站規則,例如:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

範例輸出顯示*分配的出站埠*和*空閒時間分鐘中的*預設值。 *"已分配出站埠*"的值為 0,使用基於後端池大小自動分配出站埠數來設置出站埠數。 例如,如果群集具有 50 個或更少的節點,則為每個節點分配 1024 個埠。

如果您希望基於上述預設設定面臨 SNAT 耗盡,請考慮更改*已分配的出站埠*或*空閒時間分鐘設定*。 每個附加的 IP 位址啟用 64,000 個附加連接埠進行分配,但是 Azure 標準負載均衡器不會在添加更多 IP 位址時自動增加每個節點的埠。 您可以通過設定*負載平衡器出站埠*和*負載平衡器空閑超時*參數來更改這些值。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 在自定義*分配的出站埠*之前,必須[計算所需的配額][calculate-required-quota],以避免連接或縮放問題。 為*分配的出站埠*指定的值也必須為 8 的倍數。

建立叢集時,還可以使用*負載均衡器-出站埠*和*負載均衡器空閒超時*參數,但還必須指定*負載均衡器管理-出站 ip 計數*、*負載均衡器-出站-ips*或*負載均衡器-出站-ip 前綴*。  例如：

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

當從預設值更改*負載平衡器出站埠*和*負載均衡器空閑超時*參數時,它會影響負載均衡器配置檔的行為,該配置檔會影響整個群集。

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>自訂配置的出站埠所需的配額
您必須有足夠的出站 IP 容量,具體取決於節點 VM 的數量和所需的分配的出站埠數。 要驗證您有足夠的出站 IP 容量,請使用以下公式: 
 
*出站 IP* \* \> 64,000*節點 VM* \* *需要分配的出站埠*。
 
例如,如果您有 3*個節點 VM*和 50,000*個所需的分配出站埠*,則需要至少 3 個*出站 IP*。 建議您合併超出所需範圍的其他出站 IP 容量。 此外,在計算出站 IP 容量時,必須考慮群集自動縮放器以及節點池升級的可能性。 對於群集自動縮放器,請查看當前節點計數和最大節點計數,並使用較高的值。 要進行升級,可以為每個允許升級的節點池考慮一個額外的節點 VM。
 
將*IdleTimeoutIn分鐘*設置為與預設值 30 分鐘不同的值時,請考慮工作負荷需要出站連接多長時間。 還要考慮 AKS 外部使用*的標準*SKU 負載均衡器的預設超時值為 4 分鐘。 能夠更準確地反映特定 AKS 工作負載*的 IdleTimeoutIn分鐘*值可幫助減少因不再使用連接而導致的 SNAT 耗盡。

## <a name="restrict-access-to-specific-ip-ranges"></a>限制對特定 IP 範圍的存取

默認情況下,與負載均衡器的虛擬網路關聯的網路安全組 (NSG) 具有允許所有入站外部流量的規則。 您可以更新此規則,以僅允許入站流量的特定 IP 範圍。 以下清單使用*loadBalancerSourceRange*為入站外部流量指定新的 IP 範圍:

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

上述示例更新規則,以僅允許來自*MY_EXTERNAL_IP_RANGE*範圍的入站外部流量。 有關使用此方法限制對負載均衡器服務的存取的詳細資訊,請參閱[Kubernetes 文件][kubernetes-cloud-provider-firewall]。

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
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
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

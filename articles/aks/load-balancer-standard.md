---
title: 在 Azure Kubernetes Service 中使用標準 SKU 負載平衡器（AKS）
description: 瞭解如何使用具有標準 SKU 的負載平衡器，以 Azure Kubernetes Service （AKS）公開您的服務。
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: b15c60d5436feada8558c83cb14efd7e21a22493
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212420"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service 中使用標準 SKU 負載平衡器（AKS）

若要透過 Azure Kubernetes Service （AKS）中 `LoadBalancer` 類型的 Kubernetes 服務來提供應用程式的存取權，您可以使用 Azure Load Balancer。 在 AKS 上執行的負載平衡器可做為內部或外部負載平衡器使用。 內部負載平衡器可讓 Kubernetes 服務僅供在與 AKS 叢集相同的虛擬網路中執行的應用程式存取。 外部負載平衡器會接收一或多個公用 Ip 進行輸入，並使用公用 Ip 讓 Kubernetes 服務可從外部進行存取。

Azure Load Balancer 有兩種 SKU -「基本」和「標準」。 根據預設，當您建立 AKS 叢集時，會使用*標準*SKU。 使用*標準*SKU 負載平衡器提供額外的特性和功能，例如較大的後端集區大小和可用性區域。 請務必先瞭解*標準*和*基本*負載平衡器之間的差異，再選擇要使用哪一個。 建立 AKS 叢集之後，您就無法變更該叢集的負載平衡器 SKU。 如需*基本*和*標準*sku 的詳細資訊，請參閱[Azure 負載平衡器 sku 比較][azure-lb-comparison]。

本文假設您對 Kubernetes 和 Azure Load Balancer 概念有基本瞭解。 如需詳細資訊，請參閱[Kubernetes 的核心概念 Azure Kubernetes Service （AKS）][kubernetes-concepts]和[Azure Load Balancer 的內容？][azure-lb]。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 版2.0.81 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>開始之前

本文假設您的 AKS 叢集具有*標準*SKU Azure Load Balancer。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

如果您使用現有的子網或資源群組，則 AKS 叢集服務主體也需要管理網路資源的許可權。 一般來說，請將「*網路參與者*」角色指派給委派資源上的服務主體。 如需許可權的詳細資訊，請參閱[將 AKS 存取權委派給其他 Azure 資源][aks-sp]。

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>從基本 SKU Load Balancer 移至標準 SKU

如果您現有的叢集具有基本 SKU Load Balancer，則在遷移以使用具有標準 SKU Load Balancer 的叢集時，有重要的行為差異需要注意。

例如，讓藍色/綠色部署遷移叢集是一種常見的作法，因為叢集的 `load-balancer-sku` 類型只能在叢集建立時定義。 不過，*基本 sku*負載平衡器會使用與*標準*sku 負載平衡器不相容的*基本 sku* Ip 位址，因為它們需要*標準 sku* ip 位址。 當遷移叢集以 Load Balancer Sku 升級時，將需要具有相容 IP 位址 SKU 的新 IP 位址。

如需有關如何遷移叢集的詳細考慮，請流覽[有關遷移考慮的檔](acs-aks-migration.md)，以查看遷移時要考慮的重要主題清單。 下列限制也是在 AKS 中使用標準 SKU 負載平衡器時，所要注意的重要行為差異。

### <a name="limitations"></a>限制

當您建立和管理支援具有*標準*SKU 之負載平衡器的 AKS 叢集時，適用下列限制：

* 必須至少有一個公用 IP 或 IP 首碼，才能允許來自 AKS 叢集的輸出流量。 此外，也需要公用 IP 或 IP 首碼，以維護控制平面和代理程式節點之間的連線，以及維護與舊版 AKS 的相容性。 您有下列選項可使用*標準*SKU 負載平衡器來指定公用 IP 或 IP 首碼：
    * 提供您自己的公用 Ip。
    * 提供您自己的公用 IP 首碼。
    * 指定最多100的數位，以允許 AKS 叢集在建立為 AKS 叢集的相同資源群組中建立許多*標準*SKU 公用 ip，這通常會在一開始就使用*MC_* 來命名。 AKS 會將公用 IP 指派給*標準*SKU 負載平衡器。 根據預設，如果未指定公用 IP、公用 IP 首碼或 Ip 數目，則會在與 AKS 叢集相同的資源群組中自動建立一個公用 IP。 您也必須允許公用位址，並避免建立 ban IP 建立的任何 Azure 原則。
* 使用負載平衡器的*標準*SKU 時，您必須使用 Kubernetes *1.13 或更高*版本。
* 只有當您建立 AKS 叢集時，才可以定義負載平衡器 SKU。 建立 AKS 叢集之後，您就無法變更負載平衡器 SKU。
* 在單一叢集中，您只能使用一種類型的負載平衡器 SKU （基本或標準）。
* *標準*SKU 負載平衡器只支援*標準*SKU IP 位址。

## <a name="use-the-standard-sku-load-balancer"></a>使用*標準*SKU 負載平衡器

當您建立 AKS 叢集時，根據預設，當您在該叢集中執行服務時，會使用*標準*SKU 負載平衡器。 例如，[使用 Azure CLI 的快速入門][aks-quickstart-cli]會部署使用*標準*SKU 負載平衡器的範例應用程式。

> [!IMPORTANT]
> 藉由自訂使用者定義的路由（UDR），可以避免公用 IP 位址。 將 AKS 叢集的輸出類型指定為 UDR，可以略過 AKS 所建立 Azure 負載平衡器的 IP 布建和後端集區設定。 請參閱[將叢集的 `outboundType` 設定為 ' userDefinedRouting '](egress-outboundtype.md)。

## <a name="configure-the-load-balancer-to-be-internal"></a>將負載平衡器設定為內部

您也可以將負載平衡器設定為內部，而不公開公用 IP。 若要將負載平衡器設定為內部，請將 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` 新增為*LoadBalancer*服務的注釋。 您可以在[這裡][internal-lb-yaml]查看範例 yaml 資訊清單，以及有關內部負載平衡器的詳細資料。

## <a name="scale-the-number-of-managed-public-ips"></a>調整受控公用 Ip 的數目

使用*標準*SKU 負載平衡器搭配預設建立的受控輸出公用 ip 時，您可以使用*負載平衡器管理的 ip 計數*參數來調整受控輸出公用 ip 數目。

若要更新現有的叢集，請執行下列命令。 此參數也可以在叢集建立時設定，以擁有多個受控輸出公用 Ip。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

上述範例會將*myResourceGroup*中*myAKSCluster*叢集的受控輸出公用 ip 數目設定為*2* 。 

您也可以在建立叢集時，使用*負載平衡器管理的 ip 計數*參數來設定初始的受控輸出公用 ip 數目，方法是附加 `--load-balancer-managed-outbound-ip-count` 參數，並將它設定為您想要的值。 受控輸出公用 Ip 的預設數目為1。

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>提供您自己的公用 Ip 或輸出的首碼

使用*標準*sku 負載平衡器時，AKS 叢集會自動在針對 AKS 叢集所建立的相同資源群組中建立公用 ip，並將公用 ip 指派給*標準*SKU 負載平衡器。 或者，您也可以在建立叢集時指派自己的公用 IP，或更新現有叢集的負載平衡器屬性。

藉由帶入多個 IP 位址或首碼，您可以在定義單一負載平衡器物件後方的 IP 位址時，定義多個支援服務。 特定節點的輸出端點將取決於它們所關聯的服務。

> [!IMPORTANT]
> 您必須使用*標準*Sku 公用 ip，搭配您的*標準*sku 與您的負載平衡器。 您可以使用[az network public-ip show][az-network-public-ip-show]命令來驗證公用 IP 的 SKU：
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

使用[az network public-ip show][az-network-public-ip-show]命令來列出公用 Ip 的識別碼。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

上述命令會顯示*myResourceGroup*資源群組中*myPublicIP*公用 IP 的識別碼。

使用*az aks update*命令搭配*負載平衡器-輸出 ip*參數，將您的叢集更新為您的公用 ip。

下列範例會使用*負載平衡器-輸出 ip*參數與上一個命令中的識別碼。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

您也可以使用公用 IP 首碼，搭配*標準*SKU 負載平衡器進行輸出。 下列範例會使用[az network public-ip prefix show][az-network-public-ip-prefix-show]命令來列出公用 ip 首碼的識別碼：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

上述命令會顯示*myResourceGroup*資源群組中*myPublicIPPrefix*公用 IP 首碼的識別碼。

下列範例會使用*負載平衡器-輸出 ip-* 前置詞參數與上一個命令的識別碼。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 公用 Ip 和 IP 首碼必須位於與您的 AKS 叢集相同的區域中，且屬於相同的訂用帳戶。 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>在叢集建立時間定義您自己的公用 IP 或首碼

您可能想要在叢集建立時，將您自己的 IP 位址或 IP 首碼帶入輸出，以支援允許清單輸出端點之類的案例。 將上面顯示的相同參數附加至叢集建立步驟，以在叢集生命週期開始時定義您自己的公用 Ip 和 IP 首碼。

使用*az aks create*命令搭配*負載平衡器-輸出 ip*參數，在一開始就建立含有公用 ip 的新叢集。

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

使用*az aks create*命令搭配*負載平衡器-輸出 ip-首碼*參數，在開始時建立具有公用 ip 首碼的新叢集。

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>設定輸出埠和閒置超時

> [!WARNING]
> 下一節適用于較大規模網路的先進案例，或使用預設設定來解決 SNAT 耗盡問題。 您必須先正確清查 Vm 和 IP 位址的可用配額，才能從其預設值變更*AllocatedOutboundPorts*或*IdleTimeoutInMinutes* ，以便維持狀況良好的叢集。
> 
> 改變*AllocatedOutboundPorts*和*IdleTimeoutInMinutes*的值，可能會大幅變更負載平衡器輸出規則的行為。 請先參閱[Azure 中][azure-lb-outbound-connections]的[Load Balancer 輸出規則][azure-lb-outbound-rules-overview]、[負載平衡器輸出規則][azure-lb-outbound-rules]和輸出連線，再更新這些值，以充分瞭解變更的影響。

輸出配置的埠及其閒置的超時會用於[SNAT][azure-lb-outbound-connections]。 根據預設，*標準*SKU 負載平衡器會[針對以後端集區大小為基礎的輸出埠數目][azure-lb-outbound-preallocatedports]，以及每個埠30分鐘的閒置超時，使用自動指派。 若要查看這些值，請使用[az network lb 輸出規則清單][az-network-lb-outbound-rule-list]來顯示負載平衡器的輸出規則：

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

範例輸出會顯示*AllocatedOutboundPorts*和*IdleTimeoutInMinutes*的預設值。 *AllocatedOutboundPorts*值為0時，會根據後端集區大小，使用自動指派的輸出埠數目來設定輸出埠的數目。 例如，如果叢集有50或更少的節點，則會配置每個節點的1024埠。

如果您預期會根據上述預設設定來面對 SNAT 耗盡，請考慮變更*allocatedOutboundPorts*或*IdleTimeoutInMinutes*的設定。 每個額外的 IP 位址會啟用64000額外的埠來進行配置，不過，新增更多 IP 位址時，Azure Standard Load Balancer 不會自動增加每個節點的埠。 您可以藉由設定*負載平衡器-輸出埠*和*負載平衡器-閒置-timeout*參數來變更這些值。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 您必須在自訂*allocatedOutboundPorts*之前[計算所需的配額][calculate-required-quota]，以避免連線或調整問題。 您為*allocatedOutboundPorts*指定的值也必須是8的倍數。

建立叢集時，您也可以使用*負載平衡器-輸出埠*和*負載平衡器-閒置時間*參數，但您也必須指定*負載平衡器管理-* -------------------------- *---* ---------- *---* -  例如：

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

從預設值改變*負載平衡器-輸出埠*和*負載平衡器-閒置時間*參數時，它會影響負載平衡器設定檔的行為，這會影響整個叢集。

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>自訂 allocatedOutboundPorts 所需的配額
您必須根據節點 Vm 的數目和所需配置的輸出埠，擁有足夠的輸出 IP 容量。 若要驗證您有足夠的輸出 IP 容量，請使用下列公式： 
 
*outboundIPs* \* 64000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*。
 
例如，如果您有3個*nodeVMs*和 50000 *desiredAllocatedOutboundPorts*，您至少必須有3個*outboundIPs*。 建議您將額外的輸出 IP 容量納入所需的範圍外。 此外，您必須考慮叢集自動調整程式，以及在計算輸出 IP 容量時，節點集區升級的可能性。 針對叢集自動調整程式，請檢查目前的節點計數和最大節點計數，並使用較高的值。 若要進行升級，請針對允許升級的每個節點集區，考慮其他節點 VM。
 
將*IdleTimeoutInMinutes*設定為不同于預設30分鐘的值時，請考慮您的工作負載需要輸出連線的時間長度。 也請考慮在 AKS 外部使用的*標準*SKU 負載平衡器的預設超時值為4分鐘。 *IdleTimeoutInMinutes*值可更精確地反映您的特定 AKS 工作負載，有助於減少因不再使用連接而造成的 SNAT 耗盡。

## <a name="restrict-access-to-specific-ip-ranges"></a>限制對特定 IP 範圍的存取

根據預設，與負載平衡器的虛擬網路相關聯的網路安全性群組（NSG）具有允許所有輸入外部流量的規則。 您可以將此規則更新為只允許輸入流量的特定 IP 範圍。 下列資訊清單會使用*loadBalancerSourceRanges*來指定輸入外部流量的新 IP 範圍：

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

上述範例會將規則更新為只允許來自*MY_EXTERNAL_IP_RANGE*範圍的輸入外部流量。 如需使用此方法來限制負載平衡器服務存取權的詳細資訊，請[參閱 Kubernetes 檔][kubernetes-cloud-provider-firewall]（英文）。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Kubernetes services，請[參閱 Kubernetes services 檔][kubernetes-services]。

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

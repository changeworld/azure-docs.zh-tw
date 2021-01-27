---
title: Azure Kubernetes Service (AKS) 中的 API 伺服器授權 IP 範圍
description: '瞭解如何使用 IP 位址範圍保護您的叢集，以存取 Azure Kubernetes Service (AKS 中的 API 伺服器) '
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ca6e1c06b3ad90ef12c9bf375bae50d46c5f7c37
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98890626"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>使用 Azure Kubernetes Service (AKS) 的授權 IP 位址範圍，安全地存取 API 伺服器

在 Kubernetes 中，API 伺服器會接收在叢集中執行動作的要求，例如建立資源或調整節點數目。 API 伺服器是用來與叢集互動和管理的主要方式。 若要改善叢集安全性並將攻擊降至最低，應該只能從一組有限的 IP 位址範圍存取 API 伺服器。

本文說明如何使用 API 伺服器授權 IP 位址範圍來限制哪些 IP 位址和 Cidr 可以存取控制平面。

## <a name="before-you-begin"></a>開始之前

本文說明如何使用 Azure CLI 建立 AKS 叢集。

您必須安裝並設定 Azure CLI 版本 2.0.76 或以後版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

### <a name="limitations"></a>限制

API 伺服器授權的 IP 範圍功能有下列限制：
- 在2019年10月將 API 伺服器授權 IP 位址範圍移出預覽版之後所建立的叢集上，只有 *標準* SKU 負載平衡器支援 api 伺服器授權 ip 位址範圍。 已設定 *基本* sku 負載平衡器和 API 伺服器授權 IP 位址範圍的現有叢集將會繼續運作，但無法遷移至 *標準* sku 負載平衡器。 如果升級 Kubernetes 版本或控制平面，這些現有的叢集也會繼續運作。 私人叢集不支援 API 伺服器授權 IP 位址範圍。
- 這項功能與使用 [每個節點節點集區預覽功能的公用 IP](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools-preview)的叢集不相容。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 伺服器授權的 IP 範圍總覽

Kubernetes API 伺服器是基礎 Kubernetes Api 的公開方式。 此元件可提供管理工具的互動，例如 `kubectl` 或 Kubernetes 儀表板。 AKS 提供單一租使用者叢集控制平面，具有專用的 API 伺服器。 根據預設，會將公用 IP 位址指派給 API 伺服器，而且您應該使用 Kubernetes 角色型存取控制 (Kubernetes RBAC) 或 Azure RBAC 來控制存取權。

若要保護對其他可公開存取的 AKS 控制平面/API 伺服器的存取，您可以啟用和使用已授權的 IP 範圍。 這些授權的 IP 範圍只允許定義的 IP 位址範圍與 API 伺服器通訊。 從不屬於這些授權 IP 範圍的 IP 位址對 API 伺服器提出的要求會遭到封鎖。 繼續使用 Kubernetes RBAC 或 Azure RBAC 來授權使用者及其所要求的動作。

如需 API 伺服器和其他叢集元件的詳細資訊，請參閱 [AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>建立已啟用 API 伺服器授權 IP 範圍的 AKS 叢集

使用 [az aks create][az-aks-create] 來建立叢集，並指定 *`--api-server-authorized-ip-ranges`* 參數以提供授權 IP 位址範圍的清單。 這些 IP 位址範圍通常是您的內部部署網路或公用 Ip 所使用的位址範圍。 當您指定 CIDR 範圍時，請從範圍中的第一個 IP 位址開始。 例如， *137.117.106.90/29* 是有效的範圍，但請務必指定範圍內的第一個 IP 位址，例如 *137.117.106.88/29*。

> [!IMPORTANT]
> 根據預設，您的叢集會使用 [標準 SKU 負載平衡器][standard-sku-lb] ，以供您用來設定輸出閘道。 當您在叢集建立期間啟用 API 伺服器授權的 IP 範圍時，除了您指定的範圍之外，預設也允許叢集的公用 IP。 如果您指定 *""* 或沒有的值 *`--api-server-authorized-ip-ranges`* ，則會停用 API 伺服器授權的 IP 範圍。 請注意，如果您使用的是 PowerShell，請使用 *`--api-server-authorized-ip-ranges=""`* (搭配等號) 來避免任何剖析問題。

下列範例會在名為 *myResourceGroup* 的資源群組中建立名為 *myAKSCluster* 的單一節點叢集，並啟用 API 伺服器授權的 IP 範圍。 允許的 IP 位址範圍為 *73.140.245.0/24*：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> 您應該將這些範圍新增至允許清單：
> - 防火牆公用 IP 位址
> - 代表您將管理叢集之網路的任何範圍
> - 如果您要在 AKS 叢集上使用 Azure Dev Spaces，您必須 [根據您的區域允許其他範圍][dev-spaces-ranges]。
>
> 您可以指定的 IP 範圍數目上限為200。
>
> 規則最多可能需要2min 才能傳播。 在測試連線時，請等待最多時間。

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>指定標準 SKU 負載平衡器的輸出 Ip

建立 AKS 叢集時，如果您指定叢集的輸出 IP 位址或首碼，也會允許這些位址或首碼。 例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

在上述範例中，參數中提供的所有 Ip *`--load-balancer-outbound-ip-prefixes`* 都會與參數中的 ip 一起使用 *`--api-server-authorized-ip-ranges`* 。

相反地，您可以指定 *`--load-balancer-outbound-ip-prefixes`* 參數來允許輸出負載平衡器 IP 首碼。

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>只允許標準 SKU 負載平衡器的輸出公用 IP

當您在叢集建立期間啟用 API 伺服器授權的 IP 範圍時，除了您指定的範圍之外，預設也允許叢集的標準 SKU 負載平衡器的輸出公用 IP。 若只要允許標準 SKU 負載平衡器的輸出公用 IP，請在指定參數時使用 *0.0.0.0/32* *`--api-server-authorized-ip-ranges`* 。

在下列範例中，只允許標準 SKU 負載平衡器的輸出公用 IP，您只能從叢集中的節點存取 API 伺服器。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>更新叢集的 API 伺服器授權 IP 範圍

若要更新現有叢集上的 API 伺服器授權 IP 範圍，請使用 [az aks update][az-aks-update] 命令，並使用 *`--api-server-authorized-ip-ranges`* ,--負載平衡器-輸出-ip 首碼 *、 *`--load-balancer-outbound-ips`* 或--負載平衡器-輸出 ip 首碼* 參數。

下列範例會在名為 *myResourceGroup* 的資源群組中，針對名為 *myAKSCluster* 的叢集，更新 API 伺服器授權的 IP 範圍。 要授權的 IP 位址範圍是 *73.140.245.0/24*：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

您也可以在指定參數時使用 *0.0.0.0/32* ， *`--api-server-authorized-ip-ranges`* 以只允許標準 SKU 負載平衡器的公用 IP。

## <a name="disable-authorized-ip-ranges"></a>停用授權的 IP 範圍

若要停用授權的 IP 範圍，請使用 [az aks update][az-aks-update] 並指定空的範圍，以停用 API 伺服器授權的 ip 範圍。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>尋找現有的授權 IP 範圍

若要尋找已獲得授權的 IP 範圍，請使用 [az aks show][az-aks-show] 並指定叢集的名稱和資源群組。 例如：

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>使用 Azure 入口網站來更新、停用及尋找已授權的 IP 範圍

您也可以在 Azure 入口網站中，執行新增、更新、尋找和停用授權 IP 範圍的上述作業。 若要存取，請在叢集資源的功能表分頁中，流覽至 [**設定**] 下的 [**網路**]。

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="在瀏覽器中，Azure 入口網站頁面顯示叢集資源的網路設定。已反白顯示 [設定指定的 IP 範圍] 和 [指定的 IP 範圍] 選項。":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>如何尋找我的 IP 以納入 `--api-server-authorized-ip-ranges` ？

您必須將您的開發電腦、工具或自動化 IP 位址新增至已核准 IP 範圍的 AKS 叢集清單，才能從該處存取 API 伺服器。 

另一個選項是在防火牆虛擬網路中的個別子網路內，使用所需的工具來設定 Jumpbox。 這假設您的環境具有個別網路的防火牆，且您已將防火牆 Ip 新增至授權的範圍。 同樣地，如果您有從 AKS 子網到防火牆子網的強制通道，則不是讓叢集子網中的 jumpbox 也有問題。

使用下列命令，將另一個 IP 位址新增至核准的範圍。

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> 上述範例會在叢集中附加 API 伺服器授權的 IP 範圍。 若要停用授權的 IP 範圍，請使用 az aks update 並指定空的範圍 ""。 

另一個選項是在 Windows 系統上使用下列命令來取得公用 IPv4 位址，或您可以使用 [ [尋找您的 IP 位址](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)] 中的步驟。

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

您也可以在網際網路瀏覽器中搜尋「我的 IP 位址」來尋找此位址。

## <a name="next-steps"></a>後續步驟

在本文中，您已啟用 API 伺服器授權的 IP 範圍。 這種方法是您可以如何執行安全 AKS 叢集的其中一個部分。

如需詳細資訊，請參閱 [AKS 中的應用程式和叢集的安全性概念][concepts-security] ，以及 [AKS 中叢集安全性和升級的最佳做法][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md

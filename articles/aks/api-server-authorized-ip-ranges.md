---
title: Azure Kubernetes Service （AKS）中的 API 伺服器授權 IP 範圍
description: 瞭解如何使用 IP 位址範圍來保護您的叢集，以存取 Azure Kubernetes Service 中的 API 伺服器（AKS）
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: c92d4e00da1cc3d372cca0bf4efbe648ae522608
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87057476"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中使用授權的 IP 位址範圍來保護 API 伺服器的存取

在 Kubernetes 中，API 伺服器會接收在叢集中執行動作的要求，例如建立資源或調整節點數目。 API 伺服器是與叢集互動和管理叢集的主要方式。 為了改善叢集安全性並將攻擊降至最低，您只能從一組有限的 IP 位址範圍存取 API 伺服器。

本文說明如何使用 API 伺服器授權的 IP 位址範圍，來限制哪些 IP 位址和 CIDRs 可以存取控制平面。

> [!IMPORTANT]
> 在2019年10月已授權的 API 伺服器已授權 IP 位址範圍完成後建立的叢集上，只有*標準*SKU 負載平衡器支援 api 伺服器授權的 ip 位址範圍。 已設定*基本*SKU 負載平衡器和 API 伺服器授權 IP 位址範圍的現有叢集，會繼續正常工作，但無法遷移至*標準*SKU 負載平衡器。 如果升級 Kubernetes 版本或控制平面，這些現有的叢集也會繼續工作。 私人叢集不支援 API 伺服器授權的 IP 位址範圍。

## <a name="before-you-begin"></a>開始之前

本文說明如何使用 Azure CLI 建立 AKS 叢集。

您必須安裝並設定 Azure CLI 版本 2.0.76 或以後版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 伺服器授權的 IP 範圍總覽

Kubernetes API 伺服器是基礎 Kubernetes Api 的公開方式。 此元件可提供管理工具的互動，例如 `kubectl` 或 Kubernetes 儀表板。 AKS 提供具有專用 API 伺服器的單一租使用者叢集主機。 根據預設，會將公用 IP 位址指派給 API 伺服器，而且您應該使用角色型存取控制（RBAC）來控制存取權。

若要安全存取其他可公開存取的 AKS 控制平面/API 伺服器，您可以啟用並使用已授權的 IP 範圍。 這些授權的 IP 範圍只允許已定義的 IP 位址範圍與 API 伺服器通訊。 從不屬於這些授權 IP 範圍的 IP 位址對 API 伺服器提出的要求會遭到封鎖。 繼續使用 RBAC 來授權使用者和他們所要求的動作。

如需有關 API 伺服器和其他叢集元件的詳細資訊，請參閱[AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>建立已啟用 API 伺服器授權 IP 範圍的 AKS 叢集

使用[az aks create][az-aks-create]建立叢集，並指定 *`--api-server-authorized-ip-ranges`* 參數以提供授權的 IP 位址範圍清單。 這些 IP 位址範圍通常是內部部署網路或公用 Ip 所使用的位址範圍。 當您指定 CIDR 範圍時，請從範圍中的第一個 IP 位址開始。 例如， *137.117.106.90/29*是有效的範圍，但請務必指定範圍中的第一個 IP 位址，例如*137.117.106.88/29*。

> [!IMPORTANT]
> 根據預設，您的叢集會使用[標準 SKU 負載平衡器][standard-sku-lb]，您可以用來設定輸出閘道。 當您在叢集建立期間啟用 API 伺服器授權的 IP 範圍時，除了您指定的範圍之外，預設也會允許叢集的公用 IP。 如果您指定 *""* 或沒有的值 *`--api-server-authorized-ip-ranges`* ，則會停用 API 伺服器授權的 IP 範圍。 請注意，如果您使用的是 PowerShell，請使用 *`--api-server-authorized-ip-ranges=""`* （加上等號）來避免任何剖析問題。

下列範例會在名為*myResourceGroup*的資源群組中建立名為*myAKSCluster*的單一節點叢集，並啟用 API 伺服器授權的 IP 範圍。 允許的 IP 位址範圍為*73.140.245.0/24*：

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
> - 任何範圍，代表您將從中管理叢集的網路
> - 如果您在 AKS 叢集上使用 Azure Dev Spaces，您必須根據您的[區域允許額外的範圍][dev-spaces-ranges]。
>
> 您可以指定的 IP 範圍數目上限為200。
>
> 這些規則最多可能需要2min 才能傳播。 請在測試連接時最多允許這段時間。

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>指定標準 SKU 負載平衡器的輸出 Ip

建立 AKS 叢集時，如果您指定叢集的輸出 IP 位址或首碼，則也會允許這些位址或首碼。 例如:

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

在上述範例中，參數中提供的所有 Ip， *`--load-balancer-outbound-ip-prefixes`* 以及參數中的 ip 都允許使用 *`--api-server-authorized-ip-ranges`* 。

或者，您也可以指定 *`--load-balancer-outbound-ip-prefixes`* 參數來允許輸出負載平衡器 IP 首碼。

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>僅允許標準 SKU 負載平衡器的輸出公用 IP

當您在叢集建立期間啟用 API 伺服器授權的 IP 範圍時，除了您指定的範圍之外，預設也會允許叢集標準 SKU 負載平衡器的輸出公用 IP。 若只要允許標準 SKU 負載平衡器的輸出公用 IP，請在指定參數時使用*0.0.0.0/32* *`--api-server-authorized-ip-ranges`* 。

在下列範例中，只允許標準 SKU 負載平衡器的輸出公用 IP，而且您只能從叢集內的節點存取 API 伺服器。

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

若要在現有叢集上更新 API 伺服器授權的 IP 範圍，請使用[az aks update][az-aks-update]命令並使用 *`--api-server-authorized-ip-ranges`* ,--負載平衡器-輸出-ip-前置詞 *、 *`--load-balancer-outbound-ips`* 或--負載平衡器-輸出-ip 首碼*參數。

下列範例會在名為*myResourceGroup*的資源群組中，更新名為*myAKSCluster*的叢集上的 API 伺服器授權 IP 範圍。 要授權的 IP 位址範圍是*73.140.245.0/24*：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

您也可以在指定參數時使用*0.0.0.0/32* ， *`--api-server-authorized-ip-ranges`* 只允許標準 SKU 負載平衡器的公用 IP。

## <a name="disable-authorized-ip-ranges"></a>停用授權的 IP 範圍

若要停用授權的 IP 範圍，請使用[az aks update][az-aks-update]並指定空的範圍來停用 API 伺服器授權的 IP 範圍。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>後續步驟

在本文中，您已啟用 API 伺服器授權的 IP 範圍。 此方法是您可以如何執行安全 AKS 叢集的其中一個部分。

如需詳細資訊，請參閱[AKS 中應用程式和叢集的安全性概念][concepts-security]和[AKS 中叢集安全性和升級的最佳做法][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md

---
title: Azure 庫伯奈斯服務 （AKS） 中的 API 伺服器授權 IP 範圍
description: 瞭解如何使用 IP 位址範圍保護群集，以便訪問 Azure Kubernetes 服務 （AKS） 中的 API 伺服器
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 593f9e0b335e6f4d62c76ce92f833ff4e9143372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126614"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>使用 Azure 庫伯奈斯服務 （AKS） 中的授權 IP 位址範圍安全訪問 API 伺服器

在 Kubernetes 中，API 伺服器收到在群集中執行操作的請求，例如創建資源或縮放節點數。 API 伺服器是與群集交互和管理群集的中心方式。 為了提高群集安全性並最大限度地減少攻擊，API 伺服器只能從有限的 IP 位址範圍集進行訪問。

本文介紹如何使用 API 伺服器授權的 IP 位址範圍來限制哪些 IP 位址和 CIDR 可以存取控制平面。

> [!IMPORTANT]
> 在新群集上，API 伺服器授權的 IP 位址範圍僅在*標準*SKU 負載等化器上支援。 配置*了基本*SKU 負載等化器和 API 伺服器授權的 IP 位址範圍的現有群集將繼續工作，但不能遷移到*標準*SKU 負載等化器。 如果升級了庫伯奈斯版本或控制平面，這些現有群集也將繼續工作。

## <a name="before-you-begin"></a>開始之前

API 伺服器授權的 IP 範圍僅適用于您創建的新 AKS 群集。 本文介紹如何使用 Azure CLI 創建 AKS 群集。

您需要 Azure CLI 版本 2.0.76 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 伺服器授權 IP 範圍概述

庫伯內斯 API 伺服器是基礎庫貝內斯 API 公開的方式。 此元件可提供管理工具的互動，例如 `kubectl` 或 Kubernetes 儀表板。 AKS 提供單租戶群集主機，帶有專用 API 伺服器。 預設情況下，API 伺服器被分配為公共 IP 位址，您應該使用基於角色的存取控制 （RBAC） 控制訪問。

要確保訪問其他可公開訪問的 AKS 控制平面/API 伺服器，您可以啟用和使用授權的 IP 範圍。 這些授權的 IP 範圍僅允許定義的 IP 位址範圍與 API 伺服器進行通信。 阻止從不屬於這些授權 IP 範圍的 IP 位址向 API 伺服器發出的請求。 繼續使用 RBAC 授權使用者及其請求的操作。

有關 API 伺服器和其他群集元件的詳細資訊，請參閱[AKS 的 Kubernetes 核心概念][concepts-clusters-workloads]。

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>創建啟用 API 伺服器授權的 IP 範圍的 AKS 群集

API 伺服器授權的 IP 範圍僅適用于新的 AKS 群集。 使用[az aks 創建][az-aks-create]群集並指定 *--api 伺服器授權的 ip 範圍*參數，以提供授權 IP 位址範圍的清單。 這些 IP 位址範圍通常是本地網路或公共 IP 使用的位址範圍。 指定 CIDR 範圍時，從範圍內的第一個 IP 位址開始。 例如 *，137.117.106.90/29*是有效的範圍，但請確保指定範圍內的第一個 IP 位址，例如*137.117.106.88/29*。

> [!IMPORTANT]
> 預設情況下，群集使用[標準 SKU 負載等化器][standard-sku-lb]，您可以使用該器配置出站閘道。 在群集創建期間啟用 API 伺服器授權的 IP 範圍時，除了指定的範圍外，預設情況下還允許群集的公共 IP。 如果為*api 伺服器授權 ip 範圍*指定 *""* 或無值，則將禁用 API 伺服器授權的 IP 範圍。

下面的示例在名為*myResourceGroup*的資源組中創建名為*myAKSCluster*的單節點群集，並啟用了 API 伺服器授權的 IP 範圍。 允許的 IP 位址範圍為*73.140.245.0/24*：

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
> 應將這些範圍添加到允許清單中：
> - 防火牆公共 IP 位址
> - 表示將從
> - 如果在 AKS 群集上使用 Azure 開發空間，則必須[基於區域允許其他範圍][dev-spaces-ranges]。

> 可以指定的 IP 範圍數的上限為 3500。 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>為標準 SKU 負載等化器指定出站 IP

創建 AKS 群集時，如果為群集指定出站 IP 位址或首碼，則也允許這些位址或首碼。 例如：

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

在上面的示例中，參數中提供的所有 IP*都允許與* *--api 伺服器授權的 ip 範圍*參數中的 IP 一起。

或者，您可以指定 *--負載等化器-出站 ip 首碼參數*，以允許出站負載等化器 IP 首碼。

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>僅允許標準 SKU 負載等化器的出站公共 IP

在群集創建期間啟用 API 伺服器授權的 IP 範圍時，除了指定的範圍外，預設情況下還允許群集的標準 SKU 負載等化器的出站公共 IP。 要僅允許標準 SKU 負載等化器的出站公共 IP，請使用*0.0.0.0/32*指定 *--api 伺服器授權的 ip 範圍*參數。

在下面的示例中，只允許標準 SKU 負載等化器的出站公共 IP，並且您只能從群集中的節點訪問 API 伺服器。

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>更新群集的 API 伺服器授權的 IP 範圍

要更新現有群集上的 API 伺服器授權 IP 範圍，請使用[az aks 更新][az-aks-update]命令並使用 *--api 伺服器授權的 ip 範圍**、--負載等化器-出站-ip 首碼**、--負載等化器-出站-ips，* 或 *--負載等化器-出站-ip 首碼*參數。

以下示例更新名為 myAKSCluster 的資源組中名為*myAKSCluster*的群集上的 API 伺服器授權 IP*範圍。* 要授權的 IP 位址範圍為*73.140.245.0/24*：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

在指定 *--api 伺服器授權的 ip 範圍*參數時，還可以使用*0.0.0.0/32，* 以便僅允許標準 SKU 負載等化器的公共 IP。

## <a name="disable-authorized-ip-ranges"></a>禁用授權的 IP 範圍

要禁用授權的 IP 範圍，請使用[az aks 更新][az-aks-update]並指定空範圍以禁用 API 伺服器授權的 IP 範圍。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>後續步驟

在本文中，您啟用了 API 伺服器授權的 IP 範圍。 此方法是運行安全 AKS 群集的一部分。

有關詳細資訊，請參閱[AKS 中應用程式和群集的安全概念][concepts-security]和[AKS 中群集安全和升級的最佳做法][operator-best-practices-cluster-security]。

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
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

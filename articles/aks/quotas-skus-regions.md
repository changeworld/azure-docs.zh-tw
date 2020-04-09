---
title: 資源、SKU、區域的限制
titleSuffix: Azure Kubernetes Service
description: 瞭解 Azure 庫伯奈斯服務 (AKS) 的預設配額、受限節點 VM SKU 大小以及區域可用性。
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 054d6ff4fc105d84192ac81feda97515f6cfae49
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886767"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure 庫伯奈斯服務 (AKS) 中的配額、虛擬機器大小限制和地區可用性

所有 Azure 服務都為資源和功能設置預設限制和配額。 某些虛擬機 (VM) SKU 也受到限制。

本文詳細介紹了 Azure 庫伯內斯服務 (AKS) 資源的預設資源限制以及 Azure 區域中 AKS 的可用性。

## <a name="service-quotas-and-limits"></a>服務配額和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>佈建的基礎結構

所有其他網路、計算和儲存體限制都適用於佈建的基礎結構。 有關相關限制,請參閱[Azure 訂閱和服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。

> [!IMPORTANT]
> 升級 AKS 群集時,將臨時消耗其他資源。 這些資源包括虛擬網路子網中的可用 IP 位址或虛擬機器 vCPU 配額。 如果使用 Windows Server 容器(目前在 AKS 中處於預覽版中),則將最新更新應用於節點的唯一認可方法是執行升級操作。 失敗的叢集升級過程可能表示您沒有處理這些臨時資源的可用 IP 位址空間或 vCPU 配額。 有關 Windows 伺服器節點升級過程的詳細資訊,請參閱[升級 AKS 中的節點池][nodepool-upgrade]。

## <a name="restricted-vm-sizes"></a>受限的 VM 大小

AKS 群集中的每個節點都包含固定數量的計算資源,如 vCPU 和記憶體。 如果 AKS 節點包含的計算資源不足,則 pod 可能無法正常運行。 為了確保所需的*kube 系統*pod 和應用程式能夠可靠地計畫,**請不要在 AKS 中使用以下 VM SKU:**

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

關於 VM 類型及其計算資源的詳細資訊,請參閱[Azure 中的虛擬機器的大小][vm-skus]。

## <a name="region-availability"></a>區域可用性

有關可以部署和執行叢集的最新清單,請參閱[AKS 區域可用性][region-availability]。

## <a name="next-steps"></a>後續步驟

某些預設限制和配額可以增加。 如果資源支援增加,請通過[Azure 支援請求][azure-support]請求增加(對於**問題類型**,請選擇**配額**)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool

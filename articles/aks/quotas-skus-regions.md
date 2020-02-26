---
title: Azure Kubernetes Service （AKS）中的配額、Sku 和區域可用性
description: 瞭解預設配額、受限制的節點 VM SKU 大小，以及 Azure Kubernetes Service 的區域可用性（AKS）。
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 03e7396932f0813ef4bd00d644dcdaddfe229e6a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594544"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 中的配額、虛擬機器大小限制和區域可用性（AKS）

所有 Azure 服務都會設定資源和功能的預設限制和配額。 某些虛擬機器（VM） Sku 也會受到限制，可供使用。

本文詳細說明 Azure Kubernetes Service （AKS）資源的預設資源限制，以及 Azure 區域中 AKS 的可用性。

## <a name="service-quotas-and-limits"></a>服務配額和限制

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>佈建的基礎結構

所有其他網路、計算和儲存體限制都適用於佈建的基礎結構。 如需相關限制，請參閱[Azure 訂用帳戶和服務限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。

> [!IMPORTANT]
> 當您升級 AKS 叢集時，會暫時耗用額外的資源。 這些資源包括虛擬網路子網中的可用 IP 位址，或虛擬機器 vCPU 配額。 如果您使用 Windows Server 容器（目前在 AKS 中處於預覽狀態），將最新的更新套用至節點的唯一背書方法是執行升級作業。 失敗的叢集升級程式可能表示您沒有可用的 IP 位址空間或 vCPU 配額來處理這些暫存資源。 如需 Windows Server 節點升級程式的詳細資訊，請參閱[升級 AKS 中的節點集][nodepool-upgrade]區。

## <a name="restricted-vm-sizes"></a>受限制的 VM 大小

AKS 叢集中的每個節點都包含固定的計算資源量，例如 vCPU 和 memory。 如果 AKS 節點包含的計算資源不足，pod 可能無法正確執行。 若要確保能夠可靠地排程所需的*kube 系統*pod 和您的應用程式，請**不要在 AKS 中使用下列 VM sku**：

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

如需 VM 類型及其計算資源的詳細資訊，請參閱[Azure 中的虛擬機器大小][vm-skus]。

## <a name="region-availability"></a>區域可用性

如需可部署和執行叢集之位置的最新清單，請參閱[AKS 區域可用性][region-availability]。

## <a name="next-steps"></a>後續步驟

某些預設限制和配額可以增加。 如果您的資源支援增加，請要求透過[Azure 支援要求][azure-support]增加（針對 [**問題類型**]，選取 [**配額**]）。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool

---
title: 服務配額和限制 - Azure Batch | Microsoft Docs
description: 了解預設的 Azure Batch 配額、限制和條件約束，以及如何要求增加配額
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c8a78acfa1f3e7332cee337ae1a82ee365fe356a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248225"
---
# <a name="batch-service-quotas-and-limits"></a>Batch 服務配額和限制

如同使用其他 Azure 服務，對於與 Batch 服務相關聯的特定資源有一些限制。 這其中有許多限制是 Azure 在訂用帳戶或帳戶層級上所套用的預設配額。 本文將討論這些預設值，以及如何申請加大配額。

當您設計和相應增加您的 Batch 工作負載時，請記住這些配額。 比方說，如果您的集區未達到您所指定的計算節點目標數目，您可能已達到 Batch 帳戶的核心配額限制。

您可以在單一 Batch 帳戶中執行多個 Batch 工作負載，或在相同訂用帳戶 (但不同 Azure 區域) 的 Batch 帳戶之間散佈工作負載。

如果您計劃在 Batch 中執行生產工作負載，您可能需要增加一或多個高於預設值的配額。 若要加大配額，您可以開啟線上 [客戶支援要求](#increase-a-quota) ，不另外加收費用。

## <a name="resource-quotas"></a>資源配額

配額是一種信用限制，不是容量保證。 如果您有大規模的容量需求，請連絡 Azure 支援。

另請注意，配額不是保證值。 配額可能因批次服務或使用者請求更改配額值而有所不同。

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>使用者訂用帳戶模式中的核心配額

如果您建立 Batch 帳戶時所用的集區配置模式是設定為**使用者訂用帳戶**，則配額會以不同的方式套用。 在這個模式中，建立集區時，Batch VM 和其他資源會直接建立在您的訂用帳戶中。 Azure Batch 核心配額不適用於在此模式中建立的帳戶。 相反地，會套用您在地區計算核心和其他資源之訂用帳戶中的配額。 深入了解 [Azure 訂用帳戶和服務限制、配額與限制](../azure-resource-manager/management/azure-subscription-service-limits.md)中的配額。

## <a name="pool-size-limits"></a>集區大小限制

池大小限制由批次處理服務設置。 與[資源配額](#resource-quotas)不同，這些值無法更改。 只有具有節點間通信和自訂映射的池具有不同于標準配額的限制。

| **資源** | **最大限度** |
| --- | --- |
| **[節點間通訊已啟用集區](batch-mpi.md)中的計算節點**  ||
| 批次服務集區配置模式 | 100 |
| 批次訂用帳戶集區配置模式 | 80 |
| **[使用託管映射資源創建的池](batch-custom-images.md)中的計算節點**<sup>1</sup> ||
| 專用節點 | 2000 |
| 低優先順序節點 | 1000 |

<sup>1</sup> 適用於未啟用節點間通訊的集區。

## <a name="other-limits"></a>其他限制

批次處理服務設置的其他限制。 與[資源配額](#resource-quotas)不同，這些值無法更改。

| **資源** | **最大限度** |
| --- | --- |
| [並行工作](batch-parallel-node-tasks.md) | 4 x 節點的核心數目 |
| [應用程式](batch-application-packages.md) | 20 |
| 每個應用程式的應用程式封裝 | 40 |
| 每個池的應用程式包 | 10 |
| 工作存留期上限 | 180 天<sup>1</sup> |
| 每個計算節點[的裝載](virtual-file-mount.md) | 10 |

<sup>1</sup> 工作的最長存留期 (從它新增至作業到完成時) 為 180 天。 已完成的任務將持續七天;無法訪問在最大存留期內未完成的任務的資料。

## <a name="view-batch-quotas"></a>檢視 Batch 配額

在 [Azure 入口網站][portal]中檢視您的 Batch 帳戶配額。

1. 在入口網站中選取 [Batch 帳戶] **** ，然後選取您感興趣的 Batch 帳戶。
1. 在 Batch 帳戶的功能表上選取 [配額]****。
1. 檢視目前套用至 Batch 帳戶的配額

    ![Batch 帳戶配額][account_quotas]

## <a name="increase-a-quota"></a>增加配額

使用 [Azure 入口網站][portal]，遵循下列步驟來要求增加您 Batch 帳戶或訂用帳戶的配額。 增加配額的類型取決於您 Batch 帳戶的集區配置模式。 要請求增加配額，必須包括要增加的 VM 系列。 應用配額增加時，它將應用於所有 VM 系列。

### <a name="increase-cores-quota-in-batch"></a>增加批次處理中的核心配額 

1. 選取入口網站儀表板上的 [說明 + 支援]**** 圖格或入口網站右上角的問號 (**？**)。
1. 選擇 **"新建支援請求** > **基礎知識**"。
1. 在 [基本]**** 中：
   
    a. **問題類型** > **服務和訂閱限制（配額）**
   
    b. 選取您的訂用帳戶。
   
    c. **配額類型** > **批次**
      
    選取 [下一步]****。
    
1. 在 [詳細資料]**** 中：
      
    a. 在 **"提供詳細資訊**"中，指定位置、配額類型和批次處理帳戶。
    
    ![批次配額增加][quota_increase]

    配額類型包括：

    * **每個批次帳戶**  
        特定于單個 Batch 帳戶的值，包括專用和低優先順序內核以及作業和池數。
        
    * **每個區域**  
        應用於區域中的所有批次處理帳戶的值，包括每個訂閱每個區域的批次處理帳戶數。

    低優先順序配額是所有 VM 系列的單個值。 如果需要受約束的 SKU，則必須選擇**低優先順序內核**，並包括要請求的 VM 系列。

    b. 根據[商業影響][support_sev]選取 [嚴重性]****。

    選取 [下一步]****。

1. 在 [合約資訊]**** 中：
   
    a. 選取 [偏好的連絡方式]****。
   
    b. 確認並輸入必要的連絡人詳細資料。
   
    選擇 **"創建**"以提交支援請求。

一旦您上傳支援要求，Azure 支援會與您連絡。 配額請求可能在幾分鐘內或最多兩個工作日內完成。

## <a name="related-quotas-for-vm-pools"></a>適用於 VM 集區的相關配額

部署於 Azure 虛擬網路之虛擬機器設定中的 Batch 集區會自動配置額外的 Azure 網路資源。 針對虛擬網路中每 50 個集區節點，將會需要下列資源：

* 一個[網路安全性群組](../virtual-network/security-overview.md#network-security-groups)
* 一[個公共 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* 一個[負載等化器](../load-balancer/load-balancer-overview.md)

這些資源會配置於包含在建立 Batch 集區時所提供之虛擬網路的訂用帳戶中。 這些資源會被訂用帳戶的[資源配額](../azure-resource-manager/management/azure-subscription-service-limits.md)所限制。 如果您計畫在虛擬網路中進行大型的集區部署，請檢查這些資源的訂用帳戶配額。 若有需要，請在 Azure 入口網站中選取 [説明 + 支援]**** 來要求提升配額。


## <a name="related-topics"></a>相關主題
* [使用 Azure 門戶創建 Azure 批次處理帳戶](batch-account-create-portal.md)
* [Azure Batch 功能概觀](batch-api-basics.md)
* [Azure 訂用帳戶和服務限制、配額與限制](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png

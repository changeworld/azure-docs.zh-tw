---
title: 服務配額和限制
description: 了解預設的 Azure Batch 配額、限制和條件約束，以及如何要求增加配額
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: b2039794a0c8a13070c9d81b83869ca4097bd02e
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325958"
---
# <a name="batch-service-quotas-and-limits"></a>Batch 服務配額和限制

如同使用其他 Azure 服務，對於與 Batch 服務相關聯的特定資源有一些限制。 這其中有許多限制是 Azure 在訂用帳戶或帳戶層級上所套用的預設配額。

當您設計和擴大您的 Batch 工作負載時，請記住這些配額。 比方說，如果您的集區未達到您所指定的計算節點目標數目，您可能已達到 Batch 帳戶的核心配額限制。

您可在單一 Batch 帳戶中執行多個 Batch 工作負載，或在相同訂用帳戶 (但不同 Azure 區域) 的 Batch 帳戶之間散佈工作負載。

如果您計劃在 Batch 中執行生產工作負載，您可能需要增加一或多個高於預設值的配額。 若要加大配額，您可以開啟線上 [客戶支援要求](#increase-a-quota) ，不另外加收費用。

## <a name="resource-quotas"></a>資源配額

配額是限制，而不是容量保證。 如果您有大規模的容量需求，請連絡 Azure 支援。

另請注意，配額不是保證的值。 根據 Batch 服務的變更，或使用者要求變更配額值，配額會有所不同。

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>核心配額

### <a name="cores-quotas-in-batch-service-mode"></a>Batch 服務模式的核心配額

系統會改善專用核心配額的強制執行，並在2020年12月底將所有 Batch 帳戶的變更提供給這些變更，並已完成。

Batch 所支援的每個 VM 系列都有核心配額，而且會顯示在入口網站的 [ **配額** ] 頁面上。 您可以使用支援要求來更新 VM 系列配額限制，如下所述。

由於現有的機制即將推出，因此不會檢查 VM 系列的配額限制，只會強制執行帳戶的總配額限制。 這表示，您可能會為 VM 系列配置更多的核心，而非由 VM 系列配額所表示，最多可達總帳戶配額限制。

除了總帳戶配額之外，更新的機制還會強制執行 VM 系列配額。 在轉換至新的機制時，VM 系列配額值可能會更新以避免配置失敗，最近幾個月內使用的任何 VM 系列將會更新其 VM 系列配額，以符合總帳戶配額。 這項變更不會讓使用的容量比已有的還多。

您可以藉由檢查下列各項來判斷 Batch 帳戶是否已啟用 VM 系列配額強制執行：

* Batch 帳戶 [dedicatedCoreQuotaPerVMFamilyEnforced](/rest/api/batchmanagement/batchaccount/get#batchaccount) API 屬性。

* 入口網站中 Batch 帳戶 **配額** 頁面上的文字。

### <a name="cores-quotas-in-user-subscription-mode"></a>使用者訂用帳戶模式中的核心配額

如果您建立的 [Batch 帳戶](accounts.md) 的集區配置模式設定為「 **使用者訂** 用帳戶」，則建立或調整集區時，batch vm 和其他資源會直接建立在您的訂用帳戶中。 Azure Batch 核心配額不適用於區域計算核心、個別系列計算核心和其他資源的訂用帳戶中的配額。

若要深入了解這些配額，請參閱 [Azure 訂用帳戶和服務限制、配額與限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。

## <a name="pool-size-limits"></a>集區大小限制

集區大小限制是由 Batch 服務所設定。 不同於[資源配額](#resource-quotas)，這些值無法變更。 只有支援節點間通訊和自訂映像的集區，才會受到不同於標準配額的限制。

| **Resource** | **上限** |
| --- | --- |
| **[節點間通訊已啟用集區](batch-mpi.md)中的計算節點**  ||
| 批次服務集區配置模式 | 100 |
| 批次訂用帳戶集區配置模式 | 80 |
| **[使用受控映像資源建立的集區](batch-custom-images.md)中的計算節點**<sup>1</sup> ||
| 專用節點 | 2000 |
| 低優先順序節點 | 1000 |

<sup>1</sup> 適用於未啟用節點間通訊的集區。

## <a name="other-limits"></a>其他限制

Batch 服務所設定的其他限制。 不同於[資源配額](#resource-quotas)，無法變更這些值。

| **Resource** | **上限** |
| --- | --- |
| [並行工作](batch-parallel-node-tasks.md) | 4 x 節點的核心數目 |
| [應用程式](batch-application-packages.md) | 20 |
| 每個應用程式的應用程式封裝 | 40 |
| 每個集區的應用程式封裝 | 10 |
| 工作存留期上限 | 180 天<sup>1</sup> |
| 每個計算節點的[掛接](virtual-file-mount.md) | 10 |

<sup>1</sup> 工作的最長存留期 (從它新增至作業到完成時) 為 180 天。 已完成的工作會保存七天；如果工作未於最長存留期內完成，則無法存取其資料。

## <a name="view-batch-quotas"></a>檢視 Batch 配額

若要在 [Azure 入口網站](https://portal.azure.com)中檢視 Batch 帳戶配額：

1. 請選取 [Batch 帳戶]，然後選取感興趣的 Batch 帳戶。
1. 在 Batch 帳戶的功能表上選取 [配額]。
1. 檢視目前套用至 Batch 帳戶的配額。

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Batch 帳戶配額":::

## <a name="increase-a-quota"></a>增加配額

您可使用 [Azure 入口網站](https://portal.azure.com)來要求增加 Batch 帳戶或訂用帳戶的配額。 增加配額的類型取決於您 Batch 帳戶的集區配置模式。 如需要求增加配額，您必須註明想要增加配額的 VM 系列包括在內。 增加配額通過申請後會套用至所有 VM 系列。

1. 選取入口網站儀表板上的 [說明 + 支援] 圖格或入口網站右上角的問號 ( **？** )。
1. 選取 [新增支援要求] > [基本]。
1. 在 [基本] 中：
   
    1. [問題類型] > [服務與訂用帳戶限制 (配額)]
   
    1. 選取您的訂用帳戶。
   
    1. **配額類型** > **批次**
      
       選取 [下一步] 。
    
1. 在 [詳細資料] 中：
      
    1. 在 [提供詳細資料] 中，指定位置、配額類型和 Batch 帳戶。
    
       ![Batch 增加配額][quota_increase]

       配額類型包括：

       * **每個 Batch 帳戶**  
         單一 Batch 帳戶的特定值，包括專用和低優先順序的核心，以及作業和集區的數目。
        
       * **每個區域**  
         適用於區域中所有 Batch 帳戶的值，包括每個訂用帳戶每個區域的 Batch 帳戶數目。

       低優先順序配額是跨所有 VM 系列的單一值。 如果需要受限的 SKU，您必須選取 [低優先順序核心]，並將 VM 系列加入要求中。

    1. 根據[業務衝擊](https://aka.ms/supportseverity)，選取 [嚴重性]。

       選取 [下一步] 。

1. 在 [合約資訊] 中：
   
    1. 選取 [偏好的連絡方式]。
   
    1. 確認並輸入必要的連絡人詳細資料。
   
       選取 [建立] 以提交支援要求。

一旦您上傳支援要求，Azure 支援會與您連絡。 配額要求可能在幾分鐘內完成，最多兩個工作天。

## <a name="related-quotas-for-vm-pools"></a>適用於 VM 集區的相關配額

部署於 Azure 虛擬網路之虛擬機器設定中的 Batch 集區會自動配置額外的 Azure 網路資源。 針對虛擬網路中每 50 個集區節點，將會需要下列資源：

- 一個[網路安全性群組](../virtual-network/network-security-groups-overview.md#network-security-groups)
- 一個[公用 IP 位址](../virtual-network/public-ip-addresses.md)
- 一個[負載平衡器](../load-balancer/load-balancer-overview.md)

這些資源會配置於包含在建立 Batch 集區時所提供之虛擬網路的訂用帳戶中。 這些資源會被訂用帳戶的[資源配額](../azure-resource-manager/management/azure-subscription-service-limits.md)所限制。 如果您計畫在虛擬網路中進行大型的集區部署，請檢查這些資源的訂用帳戶配額。 若有需要，請在 Azure 入口網站中選取 [説明 + 支援] 來要求提升配額。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 入口網站建立 Azure Batch 帳戶](batch-account-create-portal.md)。
* 了解 [Batch 服務工作流程和主要資源](batch-service-workflow-features.md)，例如集區、節點、作業和工作。
* 了解 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
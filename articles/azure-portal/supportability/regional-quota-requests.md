---
title: 請求增加 Azure 區域 vCPU 配額限制
description: 如何請求提高 Azure 門戶中區域的 vCPU 配額限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843679"
---
# <a name="standard-quota-increase-limits-by-region"></a>標準配額：按區域增加限制

Azure 資源管理器支援虛擬機器的兩種類型的 vCPU 配額：

* *即用即付 VM*和保留*VM 實例*受*標準 vCPU 配額*的約束。
* *現貨 VM*受點*vCPU 配額的約束*。

即用即付和保留虛擬機器實例的標準 vCPU 配額在每個區域中的每個訂閱的兩層執行：

* 第一層是所有 VM 系列*的區域 vCPU 限制*。
* 第二層是*每個 VM 系列 vCPU 限制*，如 D 系列 vCPU。

每當部署新的 spot VM 時，該 VM 系列的新 vCPU 和現有 vCPU 總使用量不得超過該特定 VM 系列的已批准的 vCPU 配額。 此外，在所有 VM 系列中部署的新 vCPU 和現有 vCPU 的總數不應超過訂閱的已批准區域 vCPU 配額總數。 如果超過這些配額之一，則不允許 VM 部署。

可以使用 Azure 門戶請求提高 VM 系列的 vCPU 配額限制。 VM 系列配額的增加會自動將區域 vCPU 總限制增加相同的數量。

創建新訂閱時，預設的區域 vCPU 總數可能不等於所有單個 VM 系列的預設 vCPU 配額總數。 此差異可能導致訂閱具有足夠的配額，以便部署每個單獨的 VM 系列。 但是，可能沒有足夠的配額來容納所有部署的總區域 vCPU。 在這種情況下，您必須提交請求，以顯式增加區域 vCPU 總數的限制。 區域 vCPU 總限制不能超過該區域所有 VM 系列中批准的總配額。

要瞭解有關標準 vCPU 配額的更多，請參閱[虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md)以及 Azure[訂閱和服務限制、配額和約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

要瞭解有關增加 spot VM vCPU 限制的更多，請參閱[Spot 配額：增加所有 VM 系列的限制](low-priority-quota.md)。

您可以通過兩種方式之一請求按區域增加 vCPU 標準配額限制。

## <a name="request-a-quota-increase-by-region-from-help--support"></a>請求從説明和支援按區域增加配額

請求按區域從**説明 + 支援**增加 vCPU 配額：

1. 在[Azure 門戶](https://portal.azure.com)功能表中，選擇 **"説明 + 支援**"。

   !["説明 + 支援"連結](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 **"説明+支援"** 中，選擇 **"新建支援請求**"。

    ![新增支援要求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 針對 [問題類型]****，選取 [服務與訂用帳戶限制 (配額)]****。

   ![選擇問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 對於**訂閱**，選擇要增加其配額的訂閱。

   ![選取一個訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 對於**配額類型**，請選擇 **"其他請求**"。

   ![選擇配額類型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 選擇 **"下一步"：** 打開**問題詳細資訊的解決方案**。 在**說明**中，提供以下資訊：

    1. 對於**部署模型**，指定**資源管理器**。  
    1. 對於**區域**，指定所需的區域，例如，**東美國 2**。  
    1. 對於 **"新限制"，** 為區域指定新的 vCPU 限制。 此值不應超過此訂閱的各個 SKU 系列的已批准配額的總和。

    ![輸入配額請求的詳細資訊](./media/resource-manager-core-quotas-request/regional-details.png)

1. 選擇 **"審閱 + 創建**"以繼續創建支援請求。

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>請求按訂閱按區域增加配額

要請求按訂閱區域增加 vCPU**配額**：

1. 在[Azure 門戶](https://portal.azure.com)中，搜索並選擇 **"訂閱**"。

   ![轉到 Azure 門戶中的訂閱](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選擇要增加其配額的訂閱。

   ![選擇要修改的訂閱](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左側窗格中，選擇 **"使用方式 + 配額**"。

   ![關注使用方式和配額連結](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角，選擇 **"請求增加**"。

   ![選擇增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 從**配額類型**中，選擇 **"其他請求**"。

   ![選擇配額類型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 選擇 **"下一步"：** 打開**問題詳細資訊的解決方案**。 在 **"描述"** 框中，提供以下附加資訊：

    1. 對於**部署模型**，指定**資源管理器**。  
    1. 對於**區域**，指定所需的區域，例如，**東美國 2**。  
    1. 對於 **"新限制"，** 為區域指定新的 vCPU 限制。 此值不應超過此訂閱的各個 SKU 系列的已批准配額的總和。

    ![輸入詳細資訊](./media/resource-manager-core-quotas-request/regional-details.png)

1. 選擇 **"審閱 + 創建**"以繼續創建支援請求。

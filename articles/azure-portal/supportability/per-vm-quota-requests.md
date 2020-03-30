---
title: 請求提高每個 Azure VM 系列的 vCPU 配額限制
description: 如何在 Azure 門戶中請求增加 VM 系列的 vCPU 配額限制，這將區域 vCPU 總限制增加相同的數量。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843704"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>標準配額：按 VM 系列增加限制

Azure 資源管理器支援虛擬機器的兩種類型的 vCPU 配額：

* *即用即付 VM*和保留*VM 實例*受*標準 vCPU 配額*的約束。
* *現貨 VM*受點*vCPU 配額的約束*。

即用即付和保留虛擬機器實例的標準 vCPU 配額在每個區域中的每個訂閱的兩層執行：

* 第一層是所有 VM 系列*的區域 vCPU 限制*。
* 第二層是*每個 VM 系列 vCPU 限制*，例如 Dv3 系列 vCPU。

每當部署新的 spot VM 時，該 VM 系列的新 vCPU 和現有 vCPU 總使用量不得超過該特定 VM 系列的已批准的 vCPU 配額。 此外，在所有 VM 系列中部署的新 vCPU 和現有 vCPU 的總數不應超過訂閱的已批准區域 vCPU 配額總數。 如果超過這些配額之一，則不允許 VM 部署。

可以使用 Azure 門戶請求提高 VM 系列的 vCPU 配額限制。 VM 系列配額的增加會自動將區域 vCPU 總限制增加相同的數量。

要瞭解有關標準 vCPU 配額的更多，請參閱[虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md)以及 Azure[訂閱和服務限制](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)。

要瞭解如何按區域增加標準配額的 vCPU 限制，請參閱[標準配額：按區域增加限制](regional-quota-requests.md)。

要瞭解有關增加 spot VM vCPU 限制的更多，請參閱[Spot 配額：增加所有 VM 系列的限制](low-priority-quota.md)。

您可以通過以下兩種方式之一請求提高每個 VM 系列的標準 vCPU 配額限制，如以下各節所述。

## <a name="request-a-standard-quota-increase-from-help--support"></a>請求從説明和支援增加標準配額

要請求標準 vCPU 配額增加每個 VM 系列從**説明 + 支援**：

> [!NOTE]
> 您還可以通過單個支援案例請求提高多個區域的配額限制。 有關詳細資訊，請參閱步驟 8。

1. 在[Azure 門戶](https://portal.azure.com)功能表上，選擇 **"説明 + 支援**"。

   !["説明+支援"連結](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 **"説明+支援"** 中，選擇 **"新建支援請求**"。

    ![創建新的支援請求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 針對 [問題類型]****，選取 [服務與訂用帳戶限制 (配額)]****。

   ![選擇問題類型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 對於**訂閱**，選擇要增加其配額的訂閱。

   ![為增加的配額選擇訂閱](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 對於**配額類型**，選擇**計算 VM（核心-vCPU）訂閱限制增加**。

   ![選擇配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 選擇 **"下一步"：** 打開**問題詳細資訊的解決方案**。 選擇 **"提供詳細資訊**以輸入其他資訊"。

   !["提供詳細資訊"連結](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在**配額詳細資訊**中，執行以下步驟：

   ![T 提供其他配額詳細資訊](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 對於**部署模型**，請選擇相應的模型。

   1. 對於 **"位置**"，請選擇位置。 對於所選位置，在 **"選擇類型****"** 下，選擇 **"標準**"。

      ![配額詳細資訊 - 配額類型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 **"類型"** 下，您可以通過多選擇支援從單個支援案例請求標準和點配額類型。

      有關增加點配額限制的詳細資訊，請參閱[虛擬機器縮放集的 Azure spot VM。](../../virtual-machine-scale-sets/use-spot.md)

   1. 在 **"標準"** 中，選擇 SKU 系列以增加配額。

      ![配額詳細資訊 - SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 輸入此訂閱所需的新配額限制。 要從清單中刪除 SKU，請清除 SKU 旁邊的核取方塊，或選擇丟棄的"X"圖示。

      ![選擇新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 要請求多個位置的配額增加，請在 **"位置**"中選擇其他位置，然後選擇適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![在配額詳細資訊中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選擇 **"保存並繼續**創建支援請求"。

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>請求從訂閱中增加標準配額

要請求從**訂閱**中每個 VM 系列增加標準 vCPU 配額：

> [!NOTE]
> 您還可以通過單個支援案例請求提高多個區域的配額限制。 有關詳細資訊，請參閱步驟 7。

1. 在[Azure 門戶](https://portal.azure.com)中，搜索並選擇 **"訂閱**"。

   ![Azure 門戶搜索中的訂閱](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 選擇要增加其配額的訂閱。

   ![要為更改選擇的訂閱](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左側窗格中，選擇 **"使用方式 + 配額**"。

   !["使用 + 配額"連結](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角，選擇 **"請求增加**"。

   ![選擇增加配額](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 對於**配額類型**，選擇**計算 VM（核心-vCPU）訂閱限制增加**。

   ![選擇配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 在**配額詳細資訊**中，執行以下步驟：

   1. 對於**部署模型**，選擇適當的模型，並為**位置**，選擇位置。

      ![提供配額詳細資訊](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 對於所選位置，在 **"類型"** 下選擇 **"選擇類型**"，然後選擇 **"標準**"。

      ![選擇標準類型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 **"類型"** 下，您可以通過多選擇支援從單個支援案例請求標準和點配額類型。

      有關增加點配額限制的詳細資訊，請參閱[虛擬機器縮放集的 Azure spot VM。](../../virtual-machine-scale-sets/use-spot.md)

   1. 對於**標準**，選擇要增加其配額的 SKU 系列。

      ![配額詳細資訊 - SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 輸入此訂閱所需的新配額限制。 要從清單中刪除 SKU，請取消選中 SKU 旁邊的核取方塊或選擇丟棄的"X"圖示。

      ![選擇新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 要請求多個位置的配額增加，請在 **"位置**"中選擇其他位置，然後選擇適當的 VM 類型。

   此步驟預載入為早期位置選擇的 SKU 系列。 輸入要應用於其他系列的配額限制。

   ![在配額詳細資訊中選擇其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選擇 **"保存並繼續**創建支援請求"。

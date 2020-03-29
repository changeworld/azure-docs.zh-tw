---
title: 點虛擬機器配額 - Azure
description: 增加現場 VM 的配額限制，該限制提供了 Azure 使用模型，允許您承擔更低的成本，以換取 Azure 根據需要刪除 VM。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842755"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>點配額：提高所有 VM 系列的限制

Spot 虛擬機器 （VM） 提供了不同的 Azure 使用模型。 它們允許您承擔更低的成本，以換取 Azure 根據需要刪除虛擬機器，以便即用即付或保留 VM 實例部署。 有關現場 VM 的詳細資訊，請參閱[虛擬機器縮放集的 Azure spot VM。](../../virtual-machine-scale-sets/use-spot.md)

Azure 資源管理器支援虛擬機器的兩種類型的 vCPU 配額：

* *即用即付 VM*和保留*VM 實例*受*標準 vCPU 配額*的約束。
* *現貨 VM*受點*vCPU 配額的約束*。

對於現場 vCPU 配額類型，資源管理器 vCPU 配額作為單個區域限制在所有可用的虛擬機器系列中強制執行。

每當部署新的點 VM 時，所有點 VM 實例的新和現有 vCPU 使用率不得超過批准的點 vCPU 配額限制。 如果超過點配額，則不允許部署現場 VM。

本文討論如何通過使用 Azure 門戶請求提高當前 vCPU 配額限制。

要瞭解有關標準 vCPU 配額的更多，請參閱[虛擬機器 vCPU 配額](../../virtual-machines/windows/quotas.md)以及 Azure[訂閱和服務限制、配額和約束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

要瞭解如何按區域增加 vCPU 限制，請參閱[標準配額：按區域增加限制](regional-quota-requests.md)。

## <a name="request-a-quota-limit-increase-from-help--support"></a>請求從説明和支援增加配額限制

請求使用**説明和支援**的所有虛擬機器系列增加配額限制：

> [!NOTE]
> 您還可以通過單個支援案例請求提高多個區域的配額限制。 有關詳細資訊，請參閱步驟 8。

1. 在[Azure 門戶](https://portal.azure.com)功能表中，選擇 **"説明 + 支援**"。

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

   1. 對於**部署模型**，選擇適當的模型，並為**位置**，選擇位置。

      ![提供其他配額詳細資訊](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 對於所選位置，在 **"選擇類型****"** 下鍵入 "**點**"。

      ![選擇點類型](./media/resource-manager-core-quotas-request/select-spot-type.png)

       在 **"類型"** 下，您可以通過多選擇支援從單個支援案例請求標準和點配額類型。

       有關詳細資訊，請參閱[標準配額：按 VM 系列增加限制](per-vm-quota-requests.md)。

   1. 輸入此訂閱所需的新配額限制。

      ![為現場 VM 選擇新配額](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 要請求多個位置的配額增加，請在 **"位置**"中選擇其他位置，然後選擇適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![在配額詳細資訊中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選擇 **"保存並繼續**創建支援請求"。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>從"訂閱"窗格請求提高配額限制

要從 **"訂閱"** 窗格請求增加所有 VM 系列的即期配額限制，請從"訂閱"窗格中：

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

1. 選擇 **"下一步"：** 打開**問題詳細資訊的解決方案**。 選擇 **"提供詳細資訊**以輸入其他資訊"。 在**配額詳細資訊**中，輸入以下資訊：

   1. 對於**部署模型**，選擇適當的模型，並為**位置**，選擇位置。

      ![提供配額詳細資訊](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 對於所選位置，在 **"選擇類型****"** 下鍵入 "**點**"。

      ![選擇點類型](./media/resource-manager-core-quotas-request/select-spot-type.png)

      有關詳細資訊，請參閱[標準配額：按 VM 系列增加限制](per-vm-quota-requests.md)。

   1. 輸入此訂閱所需的新配額限制。

      ![輸入 vCPU 限制的新值](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 要請求多個位置的配額增加，請在 **"位置**"中選擇其他位置，然後選擇適當的 VM 類型。 然後，您可以輸入適用于其他位置的限制。

   ![在配額詳細資訊中選擇其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 選擇 **"保存並繼續**創建支援請求"。

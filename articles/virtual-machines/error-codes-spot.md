---
title: Azure Spot VM 和縮放集實例的錯誤代碼
description: 瞭解在使用 Spot VM 和縮放集實例時可能看到的錯誤代碼。
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 1863c510967dcf80044d768aee1a37643a068ce4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115865"
---
# <a name="preview-error-messages-for-spot-vms-and-scale-sets"></a>預覽：Spot VM 和縮放集的錯誤訊息


> [!IMPORTANT]
> Spot VM 和虛擬機器規模集當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


以下是使用 Spot VM 和縮放集時可以接收的一些可能的錯誤代碼。


| Key | 訊息 | 描述 |
|-----|---------|-------------|
| SkuNotAvailable | 資源"資源\<\>"的請求層當前不在位置"\<位置\>"，用於訂閱"\<訂閱 ID"。\> 請嘗試其他層或部署到其他位置。 | 此位置沒有足夠的 Azure Spot 容量來創建 VM 或縮放集實例。 |
| 驅逐策略只能設置 AzureSpot 虛擬機器  |  只能在 Azure Spot 虛擬機器上設置逐出策略。 | 此 VM 不是 Spot VM，因此無法設置逐出策略。 |
| AzureSpotVM 不支援的可用性集  |  可用性集中不支援 Azure Spot 虛擬機器。 | 您需要選擇使用 Spot VM 或在可用性集中使用 VM，不能同時選擇這兩種功能。 |
| 未啟用訂閱的 Azure Spot 功能  |  未使用 Azure Spot 功能啟用訂閱。 | 您需要有一個支援 Spot VM 的訂閱。 |
| VM 優先順序不可應用  |  指定的優先順序值 ''{0}' 不能應用於虛擬機器'{1}， 因為創建虛擬機器時未指定優先順序。 | 創建 VM 時，需要指定優先順序。 |
| 現貨價格大於提供最大價格  |  無法執行{0}操作''，因為提供的最大價格 "USD"{1}低於 Azure Spot VM{2}大小的當前即期價格"美元"。{3} | 選擇較高的最高價格。 有關詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定價資訊。|
| 最大價格值無效  |  不正確最大值。 最大價格的唯一支援值是 -1 或大於零的小數。 最高價格值 -1 表示 Azure Spot 虛擬機器不會出於價格原因被逐出。 | 輸入有效最高價格。 有關詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)或[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定價。 |
| 對於分配的 VM，不允許使用最大價格更改 | 當前分配 VM '{0}時，不允許更改最大價格。 請取消分配，然後重試。 | 停止\分配 VM，以便您可以更改最大價格。 |
| 最大價格更改不允許 | 不允許最大價格變化。 | 您不能更改此 VM 的最大價格。 |
| AzureSpotis 不支援此 API 版本  |  此 API 版本不支援 Azure Spot。 | API 版本需要為 2019-03-01。 |
| AzureSpotis 不支援此 VMSize  |  此 VM 大小{0}不支援 Azure Spot。 | 選擇另一個 VM 大小。 有關詳細資訊，請參閱[Spot 虛擬機器](./linux/spot-vms.md)。 |
| 僅支援 AzureSpot 虛擬機器的 MaxPriceis  |  僅支援 Azure Spot 虛擬機器的最大價格。 | 有關詳細資訊，請參閱[Spot 虛擬機器](./linux/spot-vms.md)。 |
| 移動資源，不支援 AzureSpotVM  |  移動資源請求包含 Azure Spot 虛擬機器。 目前不支援。 請檢查虛擬機器 ID 的錯誤詳細資訊。 | 不能移動 Spot VM。 |
| 移動資源與 AzureSpotVms 不支援  |  移動資源請求包含 Azure Spot 虛擬機器縮放集。 目前不支援。 請檢查虛擬機器規模設置 Id 的錯誤詳細資訊。 | 不能移動"點縮放集實例"。 |
| 臨時OSDisk不支援SpotVM | Spot VM 不支援臨時作業系統磁片。 | 您需要為 Spot VM 使用常規作業系統磁片。 |
| AzureSpotVM 不支援在 VM 中使用 VM 編排模式 | 使用 VM 業務流程模式的虛擬機器縮放集不支援 Azure Spot 虛擬機器。 | 將業務流程模式設置為虛擬機器縮放集，以便使用 Spot 實例。 |


**後續步驟**有關詳細資訊，請參閱[現場虛擬機器](./linux/spot-vms.md)。
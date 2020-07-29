---
title: Azure 事件中樞 Resource Manager 例外狀況 |Microsoft Docs
description: Azure Resource Manager 和建議的動作所呈現的 Azure 事件中樞例外狀況清單。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: aa1a3ca647bbf9e6590446549455a9853411fd7d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281033"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure 事件中樞 Resource Manager 例外狀況
本文列出使用 Azure Resource Manager 的範本或直接呼叫來與 Azure 事件中樞互動時所產生的例外狀況。

> [!IMPORTANT]
> 這份檔經常更新。 請回來查看是否有更新。

下列各節提供透過 Azure Resource Manager 呈現的各種例外狀況/錯誤。

## <a name="error-code-conflict"></a>錯誤碼：衝突

| 錯誤碼 | 錯誤子代碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 衝突 | 40300 | 已達到或超過 EventHub 類型的資源數目上限。 實際： #，允許的最大值：# | 命名空間已達到其可包含的事件中樞數目[配額](event-hubs-quotas.md)。 | 刪除命名空間中任何未使用或無關的事件中樞，或考慮升級至[專用](event-hubs-dedicated-overview.md)叢集。 |
| 衝突 | 無 | 無法刪除嚴重損壞修復（DR）設定，因為複寫正在進行中。 在嘗試刪除 DR 設定之前，先故障切換或中斷配對。 | [GeoDR](event-hubs-geo-dr.md)複寫正在進行中，因此無法在此時間刪除設定。 | 若要解除封鎖設定的刪除，請等候複寫完成、觸發容錯移轉，或中斷 GeoDR 配對。 |
| 衝突 | 無 | 命名空間更新失敗，後端發生衝突。 | 目前正在此命名空間上執行另一個作業。 | 等到目前的作業完成，然後再試一次。 |

## <a name="error-code-429"></a>錯誤碼：429

| 錯誤碼 | 錯誤子代碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 無 | 命名空間提供轉換 | 目前正在此命名空間上執行另一個作業。 | 等到目前的作業完成，然後再試一次。 |
| 429 | 無 | 嚴重損壞修復操作進行中。 | 目前正在此命名空間或配對上進行[GeoDR](event-hubs-geo-dr.md)作業。 | 等到目前的 GeoDR 作業完成，然後再試一次。 |

## <a name="error-code-badrequest"></a>錯誤碼： BadRequest

| 錯誤碼 | 錯誤子代碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | 無法變更事件中樞的 PartitionCount。 | 基本或標準層的 Azure 事件中樞不支援變更資料分割。 | 在您的基本或標準層命名空間中，建立具有所需資料分割數目的新事件中樞。 [專用](event-hubs-dedicated-overview.md)叢集支援分割區向外延展。 |
| BadRequest | 40000 | MessageRetentionInDays 的值 ' # ' 對基本層而言無效。 此值不能超過 ' 1 ' 天。 | 基本層事件中樞命名空間僅支援最多1天的訊息保留期。 | 如果需要超過一天的訊息保留期，請[建立標準事件中樞命名空間](event-hubs-create.md)。 | 
| BadRequest | 無 | 指定的名稱無法使用。 | 命名空間名稱必須是唯一的，而且指定的名稱已被採用。 | 如果您是具有指定名稱之現有命名空間的擁有者，您可以將它刪除，這會導致資料遺失。 然後，使用相同的名稱再試一次。 如果命名空間不安全而無法刪除（或您不是擁有者），請選擇其他命名空間名稱。 |
| BadRequest | 無 | 指定的訂用帳戶已達到其命名空間的配額。 | 您的訂用帳戶已達到可保存之命名空間數目的[配額](event-hubs-quotas.md)。 | 請考慮刪除此訂用帳戶中未使用的命名空間、建立另一個訂用帳戶，或升級至[專用](event-hubs-dedicated-overview.md)叢集。 |
| BadRequest | 無 | 無法更新次要命名空間 | 無法更新命名空間，因為它是[GeoDR 配對](event-hubs-geo-dr.md)中的次要命名空間。 | 如有需要，請改為變更此配對中的主要命名空間。 否則，請中斷 GeoDR 配對以進行變更。 |
| BadRequest | 無 | 無法在基本 SKU 中設定自動擴充 | 無法在基本層事件中樞命名空間上啟用自動擴充。 | 若要在命名空間上[啟用自動](event-hubs-auto-inflate.md)擴充，請確定它是標準層。 |
| BadRequest | 無 | 沒有足夠的容量可建立命名空間。 請洽詢您的事件中樞系統管理員。 | 選取的區域位於容量中，而且無法建立更多的命名空間。 | 選取另一個區域來存放您的命名空間。 |
| BadRequest | 無 | 無法在實體類型 ' ConsumerGroup ' 上執行作業，因為命名空間的命名空間名稱 ' 使用「基本」層。  | 基本層事件中樞命名空間具有一個取用者群組的[配額](event-hubs-quotas.md#event-hubs-basic-and-standard---quotas-and-limits)（預設值）。 不支援建立更多取用者群組。 | 繼續使用預設取用者群組（$Default），或如果需要更多，請考慮改為使用標準層事件中樞命名空間。 | 
| BadRequest | 無 | 命名空間 ' namespace name ' 不存在。 | 找不到提供的命名空間。 | 再次檢查命名空間名稱是否正確，並可在您的訂用帳戶中找到。 如果不是，請[建立事件中樞命名空間](event-hubs-create.md)。 | 
| BadRequest | 無 | 資源的 location 屬性不符合其包含的命名空間。 | 在特定區域中建立事件中樞失敗，因為它不符合命名空間的區域。 | 嘗試在與命名空間相同的區域中建立事件中樞。 | 

## <a name="error-code-internal-server-error"></a>錯誤碼：內部伺服器錯誤

| 錯誤碼 | 錯誤子代碼 | 錯誤訊息 | 描述 | 建議 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 內部伺服器錯誤 | 無 | 內部伺服器錯誤。 | 事件中樞服務發生內部錯誤。 | 請重試失敗的操作。 如果作業持續失敗，請聯絡支援人員。 |
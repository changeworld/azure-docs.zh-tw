---
title: 市場計量服務 API - 常見問題 |Azure 應用商店
description: 在 Azure 應用商店中發出 SaaS 產品/服務的使用。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275776"
---
# <a name="marketplace-metering-service-apis---faq"></a>Marketplace 計量服務 API - 常見問題集

Azure 使用者訂閱包含按流量計費的 SaaS 服務後，將跟蹤客戶正在使用的每個計費維度的消耗量。 如果消耗量超過為客戶選擇的術語設置的包含數量，則服務將向 Microsoft 發出使用事件。

## <a name="emit-usage-events"></a>發出使用事件

>[!Note]
>本節僅適用于 SaaS 產品/服務，其中至少有一個計畫在發佈產品/服務時定義了計量服務維度。

![發出使用事件](media/isv-emits-usage-event.png)

有關發出使用事件事件的 API 協定的資訊，請參閱[SaaS 批次處理使用事件 API。](./marketplace-metering-service-apis.md#batch-usage-event)

### <a name="how-often-is-it-expected-to-emit-usage"></a>它預期多久發出一次用法？

理想情況下，您應在過去一小時每小時發出一次用法，僅當前一小時有用法時。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>事件發生的時間與向 Microsoft 發出使用事件之間的最大延遲是多少？

理想情況下，對於過去一小時發生的事件，每小時都會發出使用事件。 但是，預計會有延誤。 允許的最大延遲為 24 小時，之後將不接受使用事件。

例如，如果使用事件發生在一天的下午 1 點，則您必須在第二天下午 1 點之前發出與此事件關聯的使用事件。 這意味著，在系統發出使用有停機時間的情況下，它可以恢復，然後發送使用事件的時間間隔發生使用，而不會丟失逼真度。

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>當您在同一小時內發送多個使用事件時會發生什麼情況？

每小時間隔僅接受一個使用事件。 小時間隔從分鐘 0 開始，以第 59 分鐘結束。  如果在同一小時間隔內發出多個使用事件，則任何後續使用事件將作為重複項丟棄。

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>當您發出已取消訂閱的 SaaS 訂閱的使用量時會發生什麼情況？

刪除 SaaS 訂閱後，將不接受發送到市場平臺的任何使用事件。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>能否獲取所有 SaaS 訂閱的清單，包括活動訂閱和取消訂閱訂閱？

是，當您調用 API`GET /saas/subscriptions`時，它包含所有 SaaS 訂閱的清單。 每個 SaaS 訂閱的回應中的狀態欄位捕獲訂閱是活動還是取消訂閱。 對清單訂閱的調用此時最多返回 100 個訂閱。

## <a name="next-steps"></a>後續步驟

- 有關詳細資訊[，請參閱應用商店計量服務 API。](./marketplace-metering-service-apis.md)

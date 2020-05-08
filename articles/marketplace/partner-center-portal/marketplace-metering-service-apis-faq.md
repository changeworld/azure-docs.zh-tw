---
title: 計量服務 Api 常見問題-Microsoft 商業 marketplace
description: Microsoft AppSource 和 Azure Marketplace 中適用于 SaaS 供應專案之計量服務 Api 的常見問題。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb27089777baaaa7a29e020318fbc7635792af2d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857893"
---
# <a name="marketplace-metering-service-apis---faq"></a>Marketplace 計量服務 API - 常見問題集

當 Azure 使用者訂閱包含計量付費計費的 SaaS 服務時，您將會追蹤客戶所使用之每個計費維度的耗用量。 如果耗用量超過客戶所選取之詞彙的已包含數量，您的服務將會向 Microsoft 發出使用事件。

## <a name="emit-usage-events"></a>發出使用事件

>[!Note]
>本節僅適用于 SaaS 供應專案，其中至少有一個方案的計量服務維度是在發佈供應專案時所定義。

![發出使用事件](media/isv-emits-usage-event.png)

如需發出使用事件之 API 合約的詳細資訊，請參閱[SaaS 批次使用事件 API](./marketplace-metering-service-apis.md#batch-usage-event) 。

### <a name="how-often-is-it-expected-to-emit-usage"></a>預期會發出使用量的頻率為何？

在理想的情況下，您應該在過去一小時內每小時發出使用量，只有在前一小時有使用方式時。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>事件發生的時間與使用事件發出給 Microsoft 的時間之間的延遲上限為何？

在理想情況下，每小時會針對過去一小時發生的事件發出 usage 事件。 不過，預期會有延遲。 允許的延遲上限為24小時，之後將不接受使用事件。

例如，如果在一天的下午1點發生使用事件，您在下一天有 1 PM 的時間會發出與此事件相關聯的使用事件。 當系統發出的使用量有停機時間時，它將會復原，然後傳送使用量事件（在此情況下會發生使用方式），而不會失去精確度。

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>當您在同一個小時傳送一個以上的使用方式事件時，會發生什麼事？

每小時間隔只接受一個使用事件。 小時間隔會從分鐘0開始，並于59分鐘結束。  如果在同一個小時間隔內發出多個使用事件，任何後續的使用事件都會被捨棄為重複的。

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>當您針對已取消訂閱的 SaaS 訂用帳戶發出使用方式時，會發生什麼事？

刪除 SaaS 訂閱之後，將不會接受發出至 marketplace 平臺的任何使用事件。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>您是否可以取得所有 SaaS 訂用帳戶的清單，包括作用中和取消訂閱的訂閱？

是，當您呼叫`GET /saas/subscriptions` API 時，它會包含所有 SaaS 訂閱的清單。 每個 SaaS 訂閱的回應中的 [狀態] 欄位都會捕捉訂用帳戶為作用中或取消訂閱。 列出訂用帳戶的呼叫會在當時傳回最多100個訂閱。

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Marketplace 計量服務中斷時，會發生什麼事？

如果 ISV 傳送自訂計量並收到錯誤，則 ISV 應該等待然後再試一次。

若錯誤持續發生，請將該自訂計量重新提交到下一個小時（累積數量）。 繼續此程式，直到收到非錯誤回應為止。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[Marketplace 計量服務 api](./marketplace-metering-service-apis.md)。

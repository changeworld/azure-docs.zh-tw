---
title: 計量服務 Api 常見問題-Microsoft 商業 marketplace
description: Microsoft AppSource 和 Azure Marketplace 中適用于 SaaS 供應專案之計量服務 Api 的常見問題。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: bdd540ad6c7ef1954936e4fb74bade2458e50927
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120784"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Marketplace 計量付費計費 Api-常見問題

一旦客戶訂閱了 SaaS 服務，或與受管理的應用程式方案 Azure 應用程式，使用計量付費計費，您就會追蹤所使用之每個計費維度的耗用量。  如果耗用量超過客戶所選取之詞彙的已包含數量，您的服務將會向 Microsoft 發出使用事件。

## <a name="for-both-saas-offers-and-managed-apps"></a>適用于 SaaS 供應專案和受控應用程式

### <a name="how-often-is-it-expected-to-emit-usage"></a>預期會發出使用量的頻率為何？

在理想的情況下，您應該在過去一小時內每小時發出使用量，只有在前一小時有使用方式時。

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>一個排放和下一個發出之間是否有最長的期間？

沒有這項限制。 只會在發生時發出使用狀況。 例如，如果您只需要針對每個訂用帳戶存留期提交一個單位的使用量，您可以這麼做。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>事件發生的時間與使用事件發出給 Microsoft 的時間之間的延遲上限為何？

在理想情況下，會每小時針對過去一小時發生的事件發出 usage 事件。 不過，預期會有延遲。 允許的延遲上限為24小時，之後將不接受使用事件。 最佳做法是收集每小時使用量，並在一小時結束時發出為一個事件。

例如，如果在一天的下午1點發生使用事件，您在下一天有 1 PM 的時間會發出與此事件相關聯的使用事件。  如果系統發出的使用量已關閉，它可以復原，然後傳送使用量事件的時間間隔，而不會失去精確度。

如果在實際使用量之後已經過了24小時，您仍然可以使用較新的使用事件來發出已耗用的單位。  不過，這種作法可能會傷害終端客戶的帳單附隨報告的可信度。  我們建議您避免傳送每日/周/月發出的計量。  這會比較難瞭解客戶的實際使用量，並解決可能針對使用事件而引發的問題。

每小時傳送使用量的另一個原因是，為了避免在發行者傳送每日/每週/每月發出事件之前，使用者取消訂閱的情況。

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>當您在同一個小時內傳送多個使用事件時，會發生什麼事？

一小時的間隔內只接受一個使用事件。 小時間隔會從分鐘0開始，並于59分鐘結束。  如果同一個小時發出多個使用事件，任何後續的使用事件都會被捨棄為重複的。

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>當客戶在取消原則允許的時間內取消購買時，會發生什麼事？

不會收取一般費率金額，但超額使用量將會是。

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>自訂計量計畫可以用於一次性付款嗎？

是，您可以將自訂維度定義為單一時間付款的一個單位，並只針對每個客戶發出一次。

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>是否可以使用自訂計量計畫來進行階層式計價模式？

是，它可以使用代表單一價格層的每個自訂維度來執行。

例如，Contoso 想要針對第一次1000的電子郵件收取 $0.5 的費用，並于1000與5000電子郵件之間 $0.4 每封電子郵件，以及上述 $0.2 電子郵件的每封電子郵件5000。 它們可以定義三個對應至三個電子郵件定價層的自訂維度。 只要電子郵件數目維持在1000以下，就會發出第一個維度的單位，當電子郵件數目介於1000與5000之間時，則為第二個維度的單位，最後是上述5000電子郵件的第三個維度單位。

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Marketplace 計量服務中斷時，會發生什麼事？

如果 ISV 傳送自訂計量，並收到錯誤，可能是 Microsoft 端發生問題所造成（通常是在沒有錯誤的情況下接受類似的事件），則 ISV 應該等待並重試發出的訊息。

若錯誤持續發生，請將該自訂計量重新提交到下一個小時（累積數量）。 繼續此程式，直到收到非錯誤回應為止。

## <a name="for-saas-offers-only"></a>僅適用于 SaaS 供應專案

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>當您針對已取消訂閱的 SaaS 訂用帳戶發出使用方式時，會發生什麼事？

刪除 SaaS 訂閱之後，將不會接受發出至 marketplace 平臺的任何使用事件。

只能針對訂閱狀態（而不是 `PendingFulfillmentStart` 、或狀態中的訂用帳戶）發出使用方式 `Suspended` `Unsubscribed` 。

唯一的例外狀況是報告在 SaaS 訂閱取消之前的使用時間。

例如，客戶今天下午3點取消了 SaaS 訂閱。 現在，發行者仍然可以針對此 SaaS 訂用帳戶，在過去下午6點到下午3的期間發出使用量。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>您是否可以取得所有 SaaS 訂用帳戶的清單，包括作用中和取消訂閱的訂閱？

是，當您呼叫取得訂用帳戶[清單 API](pc-saas-fulfillment-api-v2.md#subscription-apis)時，它會包含所有 SaaS 訂閱的清單。 每個 SaaS 訂閱的回應中的 [狀態] 欄位都會捕捉訂用帳戶為作用中或取消訂閱。

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>SaaS 訂閱期限和超額使用方式的開始和結束日期是否已連線？

在*訂閱*狀態中，任何現有 SaaS 訂用帳戶的任何時間點都可以發出超額事件。 發行者必須負責根據計費方案中定義的原則發出使用事件。 超額部分必須根據 SaaS 訂用帳戶的期限中定義的日期來計算。 

例如，如果發行者定義一個 SaaS 方案，其中包含每月一般費率1000的 $100 電子郵件，則1000上述每個電子郵件都會透過自訂維度向 $1 計費。

當客戶在1月6日購買並啟用訂用帳戶時，包含在一般費率中的1000電子郵件將會從當天開始計算。 因此，如果直到2月5日（訂用帳戶的第一個月結束）只有900封電子郵件，客戶只需支付此訂用帳戶第一個月的固定費率，發行者就不會在1月6日到2月5日前發出任何超額使用量事件。 在2月6日，訂用帳戶將會自動更新，而計數會重新開始。 如果客戶已送出1000年2月15日，則在3月5日之前傳送的電子郵件將以「發行者」發出的超額使用量事件計費（每封電子郵件 $1）。

## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱[Marketplace 計量服務 api](./marketplace-metering-service-apis.md)。

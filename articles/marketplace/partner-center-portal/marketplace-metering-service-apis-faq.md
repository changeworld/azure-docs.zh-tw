---
title: 計量服務 Api 常見問題-Microsoft 商業市場
description: Microsoft AppSource 和 Azure Marketplace 中 SaaS 供應專案的計量服務 Api 相關常見問題。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319995"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Marketplace 計量付費 Api-常見問題

一旦客戶訂閱 SaaS 服務，或使用受管理的應用程式方案 Azure 應用程式使用計量付費，您將會追蹤所使用的每個計費維度的耗用量。  如果耗用量超過客戶所選詞彙所設定的包含數量，您的服務將會向 Microsoft 發出使用事件。

## <a name="for-both-saas-offers-and-managed-apps"></a>適用于 SaaS 供應專案和受控應用程式

### <a name="how-often-is-it-expected-to-emit-usage"></a>預期會發出使用量的頻率為何？

在理想情況下，只有在前一個小時內有使用量時，才會在過去一小時內發出使用量。

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>其中一個發射和下一個發出之間是否有最長的期間？

沒有這類限制。 只會在發生時發出使用狀況。 例如，如果您只需要在每個訂用帳戶存留期提交一個單位的使用量，就可以這樣做。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>事件發生時間之間的最大延遲，以及向 Microsoft 發出使用事件的時間為何？

在理想情況下，會每小時針對過去一小時內發生的事件發出使用事件。 不過，預期會有延遲。 允許的延遲上限為24小時，之後將不接受使用事件。 最佳做法是收集每小時的使用量，並在該小時結束時發出一個事件。

例如，如果使用事件在一天下午1點發生，則您會在下一天一直到下午1點發出與此事件相關聯的使用事件。  如果系統發出使用狀況已關閉，則它可以復原，然後針對使用方式發生的小時間隔傳送使用事件，而不會遺失精確度。

如果在實際使用量之後超過24小時，您仍然可以使用後續的使用事件來發出使用的單位。  不過，這種做法可能會危害終端客戶的計費附隨報告的可信度。  我們建議您避免每日/每週發出一次傳送計量。  您將難以瞭解客戶的實際使用情形，以及解決有關使用事件的問題或可能引發的問題。

每小時傳送使用量的另一個原因是要避免使用者在發行者傳送每日/每週/每月發出事件之前取消訂閱的情況。

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>當您在同一小時內傳送一個以上的使用事件時，會發生什麼事？

一小時的間隔只接受一個使用事件。 小時間隔從第0分鐘開始，結束于59分鐘。  如果多個使用事件發出的時間超過一小時，則會將任何後續的使用事件捨棄為重複專案。

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>當客戶在取消原則允許的時間內取消購買時，會發生什麼事？

不會向外付金額計費，但超額使用量將會是。

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>是否可以使用自訂計量方案進行一次性付款？

是，您可以將自訂維度定義為一次性付款單位，並只針對每個客戶發出一次。

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>是否可以使用自訂計量方案來分層計價模式？

可以，每個代表單一定價層的自訂維度都可以執行此工作。

例如，Contoso 想要針對前1000封電子郵件收取每封電子郵件 $0.5，$0.4 1000 和5000電子郵件之間的每封電子郵件，以及以上的 $0.2 電子郵件的每封電子郵件的5000。 他們可以定義三個對應至三個電子郵件定價層的自訂維度。 發出第一個維度的單位（只要電子郵件數目維持在1000以下），而第二個維度的單位在電子郵件數目介於1000和5000之間，最後是上述5000電子郵件的第三個維度單位。

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>如果 Marketplace 計量服務中斷，會發生什麼事？

如果 ISV 傳送自訂計量並收到錯誤，可能是因為 Microsoft 端的問題所致 (通常是在不) 錯誤的情況下接受類似事件，然後 ISV 應該等候再重試發出。

如果錯誤持續發生，請在下一個小時重新提交自訂計量， (累積數量) 。 繼續此程式，直到收到非錯誤回應為止。

## <a name="for-saas-offers-only"></a>僅適用于 SaaS 供應專案

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>當您針對已取消訂閱的 SaaS 訂用帳戶發出使用量時，會發生什麼事？

刪除 SaaS 訂用帳戶之後，將不會接受任何發出至 marketplace 平臺的使用事件。

您只能針對訂閱狀態中的訂閱發出使用方式 (而不是針對 `PendingFulfillmentStart` 、 `Suspended` 或 `Unsubscribed` 狀態) 中的訂用帳戶發出。

唯一的例外狀況是在取消 SaaS 訂用帳戶之前的時間報告使用方式。

例如，客戶在今天下午3點取消了 SaaS 訂用帳戶。 現在下午5點，發行者仍可以針對此 SaaS 訂用帳戶，在過去下午6點到下午3點之間發出使用量。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>您可以取得所有 SaaS 訂用帳戶的清單，包括作用中和取消訂閱的訂閱嗎？

是，當您呼叫取得訂用帳戶 [清單 API](pc-saas-fulfillment-api-v2.md#subscription-apis) 時，它會包含所有 SaaS 訂用帳戶的清單。 每個 SaaS 訂用帳戶的回應中的 [狀態] 欄位會捕捉訂用帳戶為使用中或已取消訂閱。

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>SaaS 訂用帳戶期限和超額使用量的開始和結束日期是否已連線？

在訂用帳戶狀態中，現有的 SaaS 訂用帳戶 *可以在任何* 時間點發出超額事件。 發行者必須負責發出以計費方案中定義的原則為基礎的使用事件。 超額部分必須根據 SaaS 訂用帳戶期限內所定義的日期來計算。 

例如，如果發行者定義了一個 SaaS 方案，其中包含 $100 的每月一般費率1000電子郵件，則1000上述每封電子郵件都會透過自訂維度以 $1 的方式計費。

當客戶在1月6日購買並啟用訂用帳戶時，將會在此天開始計算隨附費率的1000電子郵件。 因此，如果在訂用帳戶的第一個月2月5日 (結束) 只會傳送900個電子郵件，客戶只會在此訂用帳戶的第一個月支付固定費率，而且發行者在1月6日到2月5日之間將不會發出超額使用事件。 在2月6日，訂用帳戶將會自動更新，而計數將會重新開始。 如果客戶已在2月15日送達1000電子郵件，則在3月5日之前傳送的其餘電子郵件，將會根據發行者發出的超額使用事件，以每封電子郵件) 的超額部分 $1 (收費。

## <a name="next-steps"></a>接下來的步驟

- 如需詳細資訊，請參閱 [Marketplace 計量服務 api](./marketplace-metering-service-apis.md)。

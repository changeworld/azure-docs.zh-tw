---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d22b0100074a230451e5c6b3967fa5dbc8ae3f56
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515845"
---
下表列出 Azure 服務匯流排訊息特有的配額資訊。 如需服務匯流排的定價和其他配額的詳細資訊，請參閱[服務匯流排定價](https://azure.microsoft.com/pricing/details/service-bus/)。

| 配額名稱 | 影響範圍 | 注意 | 值 |
| --- | --- | --- | --- |
| 每個 Azure 訂用帳戶的基本或標準命名空間的最大數目 |命名空間 |Azure 入口網站會拒絕其他基本或標準命名空間的後續要求。 |100|
| 每個 Azure 訂用帳戶的 Premium 命名空間數目上限 |命名空間 |入口網站會拒絕後續對其他 Premium 命名空間的要求。 |100 |
| 佇列或主題大小 |單位 |在建立佇列或主題時定義。 <br/><br/> 後續的傳入訊息會遭到拒絕，而呼叫程式碼會收到例外狀況。 |1、2、3、4 GB 或 5 GB。<br /><br />在 Premium SKU 和啟用[分割](/azure/service-bus-messaging/service-bus-partitioning)的標準 SKU 中，佇列或主題大小上限為 80 GB。 |
| 命名空間上的並行連線數目 |命名空間 |後續對更多連線的要求將會遭到拒絕，而且呼叫端程式碼將會收到例外狀況。 REST 作業不會計入並行 TCP 連線。 |網路訊息：1000。<br /><br />AMQP：5000。 |
| 佇列、主題或訂用帳戶實體上的並行接收要求數目 |單位 |後續接收要求會遭到拒絕，而且呼叫程式碼會收到例外狀況。 這個配額套用至一個主題的所有訂用帳戶的並行接收作業數目合計。 |5,000 |
| 每個命名空間的主題或佇列數目 |命名空間 |後續在命名空間上建立新主題或佇列的要求都會遭到拒絕。 因此，如果透過 [Azure 入口網站][Azure portal]設定，則會產生錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼會收到例外狀況。 |適用于基本或標準層的10000。 命名空間中主題和佇列的總數必須小於或等於 10,000。 <br/><br/>針對進階層，每個訊息單位（MU）1000。 上限為4000。 |
| 每個命名空間的[分割主題或佇列](/azure/service-bus-messaging/service-bus-partitioning)數目 |命名空間 |後續要求在命名空間上建立新分割主題或佇列會遭到拒絕。 因此，如果透過 [Azure 入口網站][Azure portal]設定，則會產生錯誤訊息。 如果從管理 API 進行呼叫，則呼叫程式碼會收到例外狀況**QuotaExceededException** 。 |基本和標準層：100。<br/><br/>高階[層不](../articles/service-bus-messaging/service-bus-premium-messaging.md)支援分割的實體。<br/><br />每個分割的佇列或主題都會計入每個命名空間1000個實體的配額。 |
| 任何傳訊實體路徑的大小上限︰佇列或主題 |單位 |- |260個字元。 |
| 任何傳訊實體名稱的大小上限︰命名空間、訂用帳戶或訂用帳戶規則 |單位 |- |50 個字元。 |
| [訊息 ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) 的大小上限 | 單位 |- | 128 |
| 訊息[會話識別碼](/dotnet/api/microsoft.azure.servicebus.message.sessionid)的大小上限 | 單位 |- | 128 |
| 佇列、主題或訂用帳戶實體的訊息大小 |單位 |超出這些配額的內送訊息會遭到拒絕，而且呼叫程式碼會收到例外狀況。 |訊息大小上限：[標準層](../articles/service-bus-messaging/service-bus-premium-messaging.md)的 256 KB，進階層為 1 [MB。](../articles/service-bus-messaging/service-bus-premium-messaging.md) <br /><br />由於系統額外負荷，所以此限制小於這些值。<br /><br />標頭大小上限： 64 KB。<br /><br />屬性包中的標頭屬性數目上限： **byte/int。** Int32.maxvalue。<br /><br />屬性包中的屬性大小上限︰沒有明確限制。 受限於標頭大小上限。 |
| 佇列、主題或訂用帳戶實體的訊息屬性大小 |單位 | 產生例外狀況 `SerializationException` 。 |每個屬性的訊息屬性大小上限為32000。 所有屬性的累計大小不得超過64000。 這項限制適用于代理[訊息](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)的整個標頭，其中同時包含使用者屬性和系統屬性，例如[序號](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)、[標籤](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)和[訊息識別碼](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)。 |
| 每個主題的訂用帳戶數目 |單位 |後續要求建立主題的其他訂用帳戶會遭到拒絕。 因此，如果透過入口網站設定，則會顯示錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼會收到例外狀況。 |適用于標準層的每個主題2000。 |
| 每個主題的 SQL 篩選器數目 |單位 |後續要求在主題上建立其他篩選器會遭到拒絕，而且呼叫程式碼會收到例外狀況。 |2,000 |
| 每個主題的相互關聯篩選器數目 |單位 |後續要求在主題上建立其他篩選器會遭到拒絕，而且呼叫程式碼會收到例外狀況。 |100,000 |
| SQL 篩選器或動作的大小 |命名空間 |後續要求建立其他篩選器都會遭到拒絕，而且呼叫程式碼會收到例外狀況。 |篩選準則字串的長度上限：1024（1 K）。<br /><br />規則動作字串的長度上限：1024（1 K）。<br /><br />每個規則動作的運算式數目上限︰32。 |
| 每個命名空間、佇列或主題的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則數目 |實體、命名空間 |後續要求建立其他規則會遭到拒絕，而且呼叫程式碼會收到例外狀況。 |每個實體類型的規則數目上限：12。 <br /><br /> 在服務匯流排命名空間上設定的規則適用于所有類型：佇列、主題。 |
| 每個交易的訊息數目 | 交易 | 其他內送訊息會遭到拒絕，而呼叫程式碼會收到例外狀況，指出「無法在單一交易中傳送超過100個訊息」。 | 100 <br /><br /> 適用於 **Send()** 和 **SendAsync()** 作業。 |
| 虛擬網路和 IP 篩選規則的數目 | 命名空間 | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com

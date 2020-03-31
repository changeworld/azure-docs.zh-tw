---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651526"
---
下表列出了特定于 Azure 服務匯流排消息傳遞的配額資訊。 有關服務匯流排的定價和其他配額的資訊，請參閱[服務匯流排定價](https://azure.microsoft.com/pricing/details/service-bus/)。

| 配額名稱 | 影響範圍 | 注意 | 值 |
| --- | --- | --- | --- |
| 每個 Azure 訂閱的基本或標準命名空間的最大數量 |命名空間 |Azure 門戶將拒絕對其他基本或標準命名空間的後續請求。 |100|
| 每個 Azure 訂閱的最大高級命名空間數 |命名空間 |門戶將拒絕對其他高級命名空間的後續請求。 |100 |
| 佇列或主題大小 |單位 |在創建佇列或主題時定義。 <br/><br/> 後續傳入消息將被拒絕，並且調用代碼會收到異常。 |1、2、3、4 GB 或 5 GB。<br /><br />在高級 SKU 和啟用[分區](/azure/service-bus-messaging/service-bus-partitioning)的標準 SKU 中，最大佇列或主題大小為 80 GB。 |
| 命名空間上的並行連線數目 |命名空間 |後續的其他連接請求將被拒絕，並且調用代碼會收到異常。 REST 操作不計入併發 TCP 連接。 |網路消息： 1，000。<br /><br />AMQP： 5，000。 |
| 佇列、主題或訂閱實體上的併發接收請求數 |單位 |後續接收請求被拒絕，調用代碼會收到異常。 這個配額套用至一個主題的所有訂用帳戶的並行接收作業數目合計。 |5,000 |
| 每個命名空間的主題或佇列數 |命名空間 |後續在命名空間上建立新主題或佇列的要求都會遭到拒絕。 因此，如果透過 [Azure 入口網站][Azure portal]設定，則會產生錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼會收到例外狀況。 |10，000 表示基本或標準層。 命名空間中主題和佇列的總數必須小於或等於 10,000。 <br/><br/>對於高級層，每個消息傳遞單元 （MU） 1，000 個。 最大限制為 4，000。 |
| 每個命名空間[的分區主題或佇列](/azure/service-bus-messaging/service-bus-partitioning)數 |命名空間 |後續要求在命名空間上建立新分割主題或佇列會遭到拒絕。 因此，如果透過 [Azure 入口網站][Azure portal]設定，則會產生錯誤訊息。 如果從管理 API 呼叫，則調用代碼會接收異常**配額。"已異常"。** |基本層和標準層：100。<br/><br/>[高級](../articles/service-bus-messaging/service-bus-premium-messaging.md)層不支援分區實體。<br/><br />每個分區佇列或主題都會計入每個命名空間 1，000 個實體的配額。 |
| 任何傳訊實體路徑的大小上限︰佇列或主題 |單位 |- |260 個字元。 |
| 任何傳訊實體名稱的大小上限︰命名空間、訂用帳戶或訂用帳戶規則 |單位 |- |50 個字元。 |
| [訊息 ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) 的大小上限 | 單位 |- | 128 |
| 消息[會話 ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid)的最大大小 | 單位 |- | 128 |
| 佇列、主題或訂閱實體的消息大小 |單位 |超過這些配額的傳入消息將被拒絕，並且調用代碼會收到異常。 |最大消息大小：[標準層](../articles/service-bus-messaging/service-bus-premium-messaging.md)為 256 KB，[高級層](../articles/service-bus-messaging/service-bus-premium-messaging.md)為 1 MB。 <br /><br />由於系統額外負荷，所以此限制小於這些值。<br /><br />最大標頭大小：64 KB。<br /><br />屬性包中標頭屬性的最大數量：**位元組/int。最大值**。<br /><br />屬性包中的屬性大小上限︰沒有明確限制。 受限於標頭大小上限。 |
| 佇列、主題或訂閱實體的消息屬性大小 |單位 | 生成異常**序列化異常**。 |每個屬性的最大消息屬性大小為 32，000。 所有屬性的累積大小不能超過 64，000。 此限制適用于[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)的整個標頭 ，它同時具有使用者屬性和系統屬性，如[序號](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)、[標籤](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)和[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)。 |
| 每個主題的訂用帳戶數目 |單位 |後續要求建立主題的其他訂用帳戶會遭到拒絕。 因此，如果透過入口網站設定，則會顯示錯誤訊息。 如果從管理 API 進行呼叫，則呼叫端程式碼會收到例外狀況。 |標準層每個主題 2，000 個主題。 |
| 每個主題的 SQL 篩選器數目 |單位 |後續關於創建主題上其他篩選器的請求將被拒絕，並且調用代碼會收到異常。 |2,000 |
| 每個主題的相互關聯篩選器數目 |單位 |後續關於創建主題上其他篩選器的請求將被拒絕，並且調用代碼會收到異常。 |100,000 |
| SQL 篩選器或操作的大小 |命名空間 |後續創建其他篩選器的請求將被拒絕，並且調用代碼會收到異常。 |篩選準則字串的最大長度：1，024 （1 K）。<br /><br />規則操作字串的最大長度：1，024 （1 K）。<br /><br />每個規則動作的運算式數目上限︰32。 |
| 每個命名空間、佇列或主題的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 規則數目 |實體、命名空間 |後續創建其他規則的請求將被拒絕，並且調用代碼會收到異常。 |每個實體類型的最大規則數：12。 <br /><br /> 在服務匯流排命名空間上配置的規則適用于所有類型的：佇列、主題。 |
| 每個交易的訊息數目 | 交易 | 其他傳入消息將被拒絕，並且調用代碼會收到一個例外，指出"在單個事務中發送的電單次郵件不能超過 100 條"。 | 100 <br /><br /> 適用於 **Send()** 和 **SendAsync()** 作業。 |
| 虛擬網路和 IP 篩選器規則的數量 | 命名空間 | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com

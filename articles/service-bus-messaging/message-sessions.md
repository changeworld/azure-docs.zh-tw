---
title: Azure 服務匯流排訊息工作階段 | Microsoft Docs
description: 本文介紹如何使用會話對相關消息的無邊界序列啟用聯合和有序處理。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314048"
---
# <a name="message-sessions"></a>訊息工作階段
Microsoft Azure 服務匯流排工作階段能夠聯合和依序處理未繫結的相關訊息序列。 會話可用於先出、先出 (FIFO) 和請求-回應模式。 本文演示如何在使用服務總線時使用會話來實現這些模式。 

## <a name="first-in-first-out-fifo-pattern"></a>先先出 (FIFO) 模式
要在服務總線中實現 FIFO 保證,請使用會話。 服務總線並不規定消息之間的關係的性質,也沒有定義用於確定消息序列開始或結束位置的特定模型。

> [!NOTE]
> 服務總線的基本層不支援會話。 標準層和高階層則支援工作階段。 有關這些層之間的差異,請參閱[服務總線定價](https://azure.microsoft.com/pricing/details/service-bus/)。

所有傳送者均可在將訊息提交至佇列或主題時建立工作階段，方法是將 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 屬性設定為某個應用程式定義的識別碼，此識別碼對該工作階段而言是唯一的。 在 AMQP 1.0 通訊協定層級，這個值會對應至「群組識別碼」** 屬性。

在會話感知佇列或訂閱中,當會話的[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)至少有一條消息時,會話將存在。 會話存在後,沒有定義會話過期或消失的時間或 API。 理論上，今日可接收工作階段的訊息，一年內的下一個訊息，而且如果 **SessionId** 相符，則從服務匯流排的觀點來看，工作階段是一樣。

不過，一般而言，應用程式會對一組相關訊息開頭與結尾位置具有清楚的概念。 服務總線不設置任何特定規則。

如何描述傳輸檔案的序列範例是將第一個訊息的 **Label** 屬性設為 **start**、針對中繼訊息設為 **content**，然後針對最後一個訊息設為 **end**。 內容訊息的相對位置可從 **start** 訊息的 SequenceNumber** 計算成為目前訊息的 SequenceNumber** 差異。

服務匯流排中的工作階段功能會啟用特定的接收作業，在 C# 和 Java API 中的形式為 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession)。 您可以透過 Azure Resource Manager 來設定佇列或訂用帳戶上的 [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 屬性，或者在入口網站中設定旗標，藉以啟用此功能。 在嘗試使用相關的 API 操作之前,需要這樣做。

在入口網站中，使用下列核取方塊來設定旗標：

![][2]

> [!NOTE]
> 在佇列或訂閱上啟用作業階段時,用戶端應用程式***無法再***發送/接收常規消息。 所有消息都必須作為工作階段的一部分發送(通過設置會話 ID)並通過接收會話接收。

適用於工作階段的 API 會存在於佇列和訂用帳戶用戶端上。 有一個命令模型,用於控制何時接收會話和消息,以及一個基於處理程式的模型(類似於*OnMessage)* 隱藏管理接收迴圈的複雜性。

### <a name="session-features"></a>工作階段功能

工作階段會提供交錯式訊息資料流的並行分離信號，同時保留並保證會依序傳遞。

![][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 接收者會由接受工作階段的用戶端所建立。 用戶端會在 C# 中呼叫 [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) 或 [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)。 在回應式回呼模型中，它會註冊工作階段處理常式。

當[接受 MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession)物件且由用戶端保留時,該用戶端對佇列或訂閱中存在該會話的[SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId)的所有消息以及該**會話 Id**仍位於會話保持期間到達的所有消息持有獨佔鎖。

當調用**Close**或**CloseAsync**時,或在應用程式無法執行關閉操作的情況下,鎖過期時釋放鎖。 會話鎖應視為檔上的獨佔鎖,這意味著應用程式應在不再需要會話時立即關閉會話和/或不希望收到任何進一步的消息。

當多個並行接收者從佇列提取時，就會將屬於特殊工作階段的訊息分派給目前保有該工作階段之鎖定的特定接收者。 通過此操作,一個佇列或訂閱中的交錯消息流可以乾淨地除以多路複用到不同的接收器,並且這些接收器也可以駐留在不同的用戶端計算機上,因為鎖管理發生在服務總線內的服務端。

上圖顯示三個並行工作階段接收者。 帶有 `SessionId` = 4 的工作階段沒有任何作用中的所有權用戶端，這表示沒有訊息從此特定工作階段傳遞。 工作階段的行為在許多方面類似子佇列。

工作階段接收者所保留的工作階段鎖定是對於「查看鎖定」** 安置模式所使用之訊息鎖定的保護傘。 接收方不能同時「在飛行中」有兩條消息,但必須按順序處理消息。 只有在已完成先前的訊息或使其成為失效信件時才能取得新訊息。 放棄訊息會導致下一個接收作業再次提供相同的訊息。

### <a name="message-session-state"></a>訊息工作階段狀態

在大規模高可用性雲系統中處理工作流時,與特定會話關聯的工作流處理程序必須能夠從意外故障中恢復,並且可以從工作開始地的不同進程或計算機上恢復部分已完成的工作。

工作階段狀態設施會在訊息代理程式內部，為訊息工作階段啟用應用程式定義的註釋，因此，相對於該工作階段所記錄的處理狀態會在新的處理器取得該工作階段時立即變成可用狀態。

從服務匯流排的觀點而言，訊息工作階段狀態是一個不透明的二進位物件，其可保存一個訊息大小的資料，針對服務匯流排標準為 256 KB，針對服務匯流排進階則是 1 MB。 相對於工作階段的處理狀態可以保留在工作階段狀態內部，或者工作階段狀態可以指向某個保留這類資訊的儲存體位置或資料庫記錄。

管理工作階段狀態的 API ([SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 和 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)) 可在 C# 和 Java API 中的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 物件上找到。 先前未設定任何工作階段狀態的工作階段會針對 **GetState** 傳回 **null** 參考。 使用 [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 來完成清除先前設定的工作階段狀態。

工作階段狀態保持,只要它未清除(傳回**null),** 即使會話中的所有消息都已使用。

可以使用 Java API 中的**作業器**方法以及 .NET 框架用戶端中的[佇列用戶端](/dotnet/api/microsoft.servicebus.messaging.queueclient)和[訂閱用戶端](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient)上的[GetMessage 工作階段](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions)來枚舉佇列或訂閱中的所有現有作業階段。

保留於佇列或訂用帳戶中的工作階段狀態會計入該實體的儲存體配額。 使用工作階段完成應用程式時，接著會建議應用程式清除其保留狀態以避免產生外部管理成本。

### <a name="impact-of-delivery-count"></a>交貨計數的影響

在沒有會話的情況下,會話上下文中每封郵件的傳遞計數的定義與定義略有不同。 下面是一個匯總交貨計數增量的表。

| 狀況 | 郵件的傳遞計數是否增加 |
|----------|---------------------------------------------|
| 會話已接受,但會話鎖過期(由於超時) | 是 |
| 會話被接受,會話中的消息未完成(即使它們已鎖定),會話已關閉 | 否 |
| 會話被接受,消息完成,然後會話被顯式關閉 | 不適用(這是標準流)。 此處從工作階段中刪除訊息 ) |

## <a name="request-response-pattern"></a>要求-回應模式
[請求-回覆模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)是一種成熟的集成模式,它使發送方應用程式能夠發送請求,併為接收方提供正確發送回應的方法。 此模式通常需要短時間佇列或主題,應用程式才能向其發送回應。 在這種情況下,會話提供了具有類似語義的簡單替代解決方案。 

多個應用程式可以將其請求發送到單個請求佇列,並設置特定的標頭參數以唯一標識寄件人應用程式。 接收方應用程式可以處理佇列中的請求,並在啟用會話的佇列上發送答覆,將會話 ID 設置為寄件人在請求消息上發送的唯一標識符。 然後,發送請求的應用程式可以接收特定會話 ID 上的消息並正確處理答覆。

> [!NOTE]
> 發送初始請求的應用程式應瞭解工作階段 ID,並`SessionClient.AcceptMessageSession(SessionID)`用於鎖定它期望回應的作業階段。 最好使用將應用程式實例唯一標識為會話 ID 的 GUID。不應在工作階段處理程式或`AcceptMessageSession(timeout)`佇列中設置任何工作階段處理程式,以確保回應可供特定接收方鎖定和處理。

## <a name="next-steps"></a>後續步驟

- 有關使用 .NET 框架客戶端處理工作階段感知消息的範例,請參閱[Microsoft.Azure.ServiceBus 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions)或[Microsoft.ServiceBus.消息範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions)。 

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png

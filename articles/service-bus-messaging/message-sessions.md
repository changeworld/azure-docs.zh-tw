---
title: Azure 服務匯流排訊息工作階段 | Microsoft Docs
description: 本文說明如何使用會話來啟用未系結的相關訊息序列的聯合和排序處理。
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
ms.date: 04/23/2020
ms.author: aschhab
ms.openlocfilehash: a4bc2dcfd1826623516a40be0aff7688d0b6168c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116684"
---
# <a name="message-sessions"></a>訊息工作階段
Microsoft Azure 服務匯流排工作階段能夠聯合和依序處理未繫結的相關訊息序列。 會話可以在先進先出（FIFO）和要求-回應模式中使用。 本文說明如何在使用服務匯流排時，使用會話來執行這些模式。 

## <a name="first-in-first-out-fifo-pattern"></a>先進先出（FIFO）模式
若要實現服務匯流排中的 FIFO 保證，請使用會話。 服務匯流排不會規定訊息之間關聯性的本質，而且也不會定義特定的模型來判斷訊息序列的開始或結束位置。

> [!NOTE]
> 服務匯流排的基本層不支援會話。 標準層和高階層則支援工作階段。 如需這些層級之間的差異，請參閱[服務匯流排定價](https://azure.microsoft.com/pricing/details/service-bus/)。

所有傳送者均可在將訊息提交至佇列或主題時建立工作階段，方法是將 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 屬性設定為某個應用程式定義的識別碼，此識別碼對該工作階段而言是唯一的。 在 AMQP 1.0 通訊協定層級，這個值會對應至「群組識別碼」** 屬性。

在會話感知的佇列或訂用帳戶上，當至少有一個訊息具有會話的[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)時，會話就會存在。 會話一旦存在，就不會針對會話到期或消失時，定義時間或 API。 理論上，今日可接收工作階段的訊息，一年內的下一個訊息，而且如果 **SessionId** 相符，則從服務匯流排的觀點來看，工作階段是一樣。

不過，一般而言，應用程式會對一組相關訊息開頭與結尾位置具有清楚的概念。 服務匯流排不會設定任何特定的規則。

如何描述傳輸檔案的序列範例是將第一個訊息的 **Label** 屬性設為 **start**、針對中繼訊息設為 **content**，然後針對最後一個訊息設為 **end**。 內容訊息的相對位置可從 **start** 訊息的 SequenceNumber** 計算成為目前訊息的 SequenceNumber** 差異。

服務匯流排中的工作階段功能會啟用特定的接收作業，在 C# 和 Java API 中的形式為 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession)。 您可以透過 Azure Resource Manager 來設定佇列或訂用帳戶上的 [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 屬性，或者在入口網站中設定旗標，藉以啟用此功能。 在您嘗試使用相關的 API 作業之前，這是必要的。

在入口網站中，使用下列核取方塊來設定旗標：

![][2]

> [!NOTE]
> 當會話在佇列或訂用帳戶上啟用時，用戶端應用程式將無法***再***傳送/接收一般訊息。 所有訊息都必須以會話的一部分傳送（藉由設定會話識別碼），並接收會話接收。

適用於工作階段的 API 會存在於佇列和訂用帳戶用戶端上。 有一個命令式模型可控制會話和訊息的接收時間，以及一個以處理常式為基礎的模型，類似于*OnMessage*，會隱藏管理接收迴圈的複雜度。

### <a name="session-features"></a>工作階段功能

工作階段會提供交錯式訊息資料流的並行分離信號，同時保留並保證會依序傳遞。

![][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 接收者會由接受工作階段的用戶端所建立。 用戶端會在 C# 中呼叫 [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) 或 [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)。 在回應式回呼模型中，它會註冊工作階段處理常式。

當[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession)物件被接受且由用戶端持有時，該用戶端會在具有該會話之[sessionid](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) （存在於佇列或訂用帳戶中）的所有訊息上保留獨佔鎖定，同時也會在保留會話時仍抵達的所有具有該**sessionid**的訊息上。

鎖定會在呼叫**close**或**CloseAsync**時釋放，或者當鎖定在應用程式無法執行關閉作業的情況下過期時。 會話鎖定應視為檔案的獨佔鎖定，這表示應用程式應該在它不再需要時關閉會話，並（或）不會預期任何進一步的訊息。

當多個並行接收者從佇列提取時，就會將屬於特殊工作階段的訊息分派給目前保有該工作階段之鎖定的特定接收者。 有了該作業，一個佇列或訂用帳戶中的交錯式訊息資料流程就會完全解除多工到不同的接收者，而這些接收者也可以存留在不同的用戶端電腦上，因為鎖定管理會在服務匯流排內的服務端進行。

上圖顯示三個並行工作階段接收者。 帶有 `SessionId` = 4 的工作階段沒有任何作用中的所有權用戶端，這表示沒有訊息從此特定工作階段傳遞。 工作階段的行為在許多方面類似子佇列。

工作階段接收者所保留的工作階段鎖定是對於「查看鎖定」** 安置模式所使用之訊息鎖定的保護傘。 只有一個接收者可以擁有一個會話的鎖定。 接收者可能會有許多進行中的訊息，但會依序接收訊息。 放棄訊息會導致下一個接收作業再次提供相同的訊息。

### <a name="message-session-state"></a>訊息工作階段狀態

在高規模、高可用性的雲端系統中處理工作流程時，與特定會話相關聯的工作流程處理常式必須能夠從非預期的失敗中復原，並可在工作開始所在的不同進程或電腦上繼續部分完成的工作。

工作階段狀態設施會在訊息代理程式內部，為訊息工作階段啟用應用程式定義的註釋，因此，相對於該工作階段所記錄的處理狀態會在新的處理器取得該工作階段時立即變成可用狀態。

從服務匯流排的觀點而言，訊息工作階段狀態是一個不透明的二進位物件，其可保存一個訊息大小的資料，針對服務匯流排標準為 256 KB，針對服務匯流排進階則是 1 MB。 相對於工作階段的處理狀態可以保留在工作階段狀態內部，或者工作階段狀態可以指向某個保留這類資訊的儲存體位置或資料庫記錄。

管理工作階段狀態的 API ([SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 和 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)) 可在 C# 和 Java API 中的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 物件上找到。 先前未設定任何工作階段狀態的工作階段會針對 **GetState** 傳回 **null** 參考。 使用 [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 來完成清除先前設定的工作階段狀態。

會話狀態會維持不會被清除（傳回**null**），即使已耗用會話中的所有訊息也一樣。

佇列或訂用帳戶中的所有現有會話都可以使用 JAVA API 中的**SessionBrowser**方法來列舉，並在 .NET Framework 用戶端中使用[QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient)和[SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient)上的[GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) 。

保留於佇列或訂用帳戶中的工作階段狀態會計入該實體的儲存體配額。 使用工作階段完成應用程式時，接著會建議應用程式清除其保留狀態以避免產生外部管理成本。

### <a name="impact-of-delivery-count"></a>傳遞計數的影響

會話內容中每個訊息的傳遞計數定義會隨著會話中的定義而略有不同。 以下是摘要說明傳遞計數何時遞增的資料表。

| 案例 | 訊息的傳遞計數會遞增 |
|----------|---------------------------------------------|
| 已接受會話，但會話鎖定過期（因為超時） | 是 |
| 已接受會話，會話中的訊息不會完成（即使已鎖定），而且會話已關閉 | 否 |
| 已接受會話，訊息已完成，然後會話已明確關閉 | N/A （這是標準流程。 這裡的訊息會從會話中移除） |

## <a name="request-response-pattern"></a>要求-回應模式
[要求-回復模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)是一種已妥善建立的整合模式，可讓傳送者應用程式傳送要求，並提供一種方法讓接收者將回應正確地傳回給傳送者應用程式。 此模式通常需要一個短期的佇列或主題，應用程式才能傳送回應。 在此案例中，會話會提供具有類似語義的簡單替代方案。 

多個應用程式可以將其要求傳送至單一要求佇列，並將特定的標頭參數設定為唯一識別傳送者應用程式。 接收者應用程式可以處理傳入佇列中的要求，並在已啟用會話的佇列上傳送回復，將會話識別碼設定為傳送者在要求訊息上傳送的唯一識別碼。 然後，傳送要求的應用程式就可以接收特定會話識別碼的訊息，並正確地處理回復。

> [!NOTE]
> 傳送初始要求的應用程式應該知道會話識別碼，並使用`SessionClient.AcceptMessageSession(SessionID)`來鎖定其預期回應的會話。 使用可唯一識別應用程式實例作為會話識別碼的 GUID 是個不錯的主意。不應該有會話處理常式或`AcceptMessageSession(timeout)`佇列，以確保回應可供特定接收者鎖定和處理。

## <a name="next-steps"></a>後續步驟

- 如需使用 .NET Framework 用戶端處理會話感知訊息[Microsoft.ServiceBus.Messaging samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions)的範例，請參閱[Microsoft Azure 服務匯流排範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions)或。 

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png

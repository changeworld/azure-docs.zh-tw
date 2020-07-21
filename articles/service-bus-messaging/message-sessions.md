---
title: Azure 服務匯流排訊息工作階段 | Microsoft Docs
description: 本文說明如何使用工作階段來聯結和依序處理未繫結的相關訊息序列。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05efc550e119186a2925c13d3fcfed11bec17251
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511291"
---
# <a name="message-sessions"></a>訊息工作階段
Microsoft Azure 服務匯流排工作階段能夠聯合和依序處理未繫結的相關訊息序列。 工作階段可在**先進先出 (FIFO)** 和**要求-回應**模式中使用。 本文說明如何在利用服務匯流排時，使用工作階段來實作這些模式。 

> [!NOTE]
> 基本層的服務匯流排並不支援工作階段。 標準層和高階層則支援工作階段。 如需這些階層之間的差異，請參閱[服務匯流排定價](https://azure.microsoft.com/pricing/details/service-bus/)。

## <a name="first-in-first-out-fifo-pattern"></a>先進先出 (FIFO) 模式
若要實現服務匯流排中的 FIFO 保證，請使用工作階段。 服務匯流排對於訊息之間關聯性的本質並無任何規範，且也不會定義特殊的模型來判斷訊息序列開頭或結尾位置。

所有傳送者均可在將訊息提交至佇列或主題時建立工作階段，方法是將 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 屬性設定為某個應用程式定義的識別碼，此識別碼對該工作階段而言是唯一的。 在 AMQP 1.0 通訊協定層級，這個值會對應至「群組識別碼」屬性。

在工作階段感知的佇列或訂用帳戶中，工作階段會在至少有一個訊息具備工作階段的 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 時變成存在。 一旦工作階段存在之後，就不會有針對工作階段到期或消失時定義的時間或 API。 理論上，今日可接收工作階段的訊息，一年內的下一個訊息，而且如果 **SessionId** 相符，則從服務匯流排的觀點來看，工作階段是一樣。

不過，一般而言，應用程式會對一組相關訊息開頭與結尾位置具有清楚的概念。 服務匯流排不會設定任何特定的規則。

如何描述傳輸檔案的序列範例是將第一個訊息的 **Label** 屬性設為 **start**、針對中繼訊息設為 **content**，然後針對最後一個訊息設為 **end**。 內容訊息的相對位置可從 **start** 訊息的 SequenceNumber 計算成為目前訊息的 SequenceNumber 差異。

服務匯流排中的工作階段功能會啟用特定的接收作業，在 C# 和 Java API 中的形式為 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession)。 您可以透過 Azure Resource Manager 來設定佇列或訂用帳戶上的 [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 屬性，或者在入口網站中設定旗標，藉以啟用此功能。 在嘗試使用相關的 API 作業之前，必須先執行此動作。

在入口網站中，使用下列核取方塊來設定旗標：

![[建立佇列] 對話方塊的螢幕擷取畫面，其中已選取 [啟用會話] 選項，並以紅色概述。][2]

> [!NOTE]
> 在佇列或訂用帳戶上啟用工作階段後，用戶端應用程式「無法再」傳送/接收一般訊息。 所有訊息都必須作為工作階段的一部分傳送 (藉由設定工作階段識別碼)，並透過接收工作階段來接收。

適用於工作階段的 API 會存在於佇列和訂用帳戶用戶端上。 有一個命令式模型可供控制接收工作階段與訊息的時間，還有一個以處理常式為基礎的模型，類似於 *OnMessage*，其可隱藏管理接收迴圈的複雜度。

### <a name="session-features"></a>工作階段功能

工作階段會提供交錯式訊息資料流的並行分離信號，同時保留並保證會依序傳遞。

![顯示會話功能如何保留排序傳遞的圖表。][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 接收者會由接受工作階段的用戶端所建立。 用戶端會在 C# 中呼叫 [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) 或 [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)。 在回應式回呼模型中，它會註冊工作階段處理常式。

接受 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 物件且由用戶端加以保留時，該用戶端就會在具有該工作階段的 [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) 且存在於佇列或訂用帳戶中所有訊息上保留獨佔鎖定，同時也會在具有 **SessionId** 且保留工作階段時仍會送達的所有訊息上保留獨佔鎖定。

鎖定會在呼叫 **Close** 或 **CloseAsync** 時釋放，或當鎖定在應用程式無法執行關閉作業而到期時加以釋放。 工作階段鎖定應被視為類似檔案上的獨佔鎖定，這表示只要應用程式不再需要工作階段和/或不預期會有任何後續訊息時，就應儘速關閉該工作階段。

當多個並行接收者從佇列提取時，就會將屬於特殊工作階段的訊息分派給目前保有該工作階段之鎖定的特定接收者。 透過該作業，一個佇列或訂用帳戶中的交錯式訊息資料流就會被完全分離信號到不同接收者，而那些接收者也可以存留於不同的用戶端機器上，因為鎖定管理會發生於服務匯流排內部的服務端。

上圖顯示三個並行工作階段接收者。 帶有 `SessionId` = 4 的工作階段沒有任何作用中的所有權用戶端，這表示沒有訊息從此特定工作階段傳遞。 工作階段的行為在許多方面類似子佇列。

工作階段接收者所保留的工作階段鎖定是對於「查看鎖定」安置模式所使用之訊息鎖定的保護傘。 只有一個接收者可擁有工作階段的鎖定。 接收者可能會有許多進行中的訊息，但會依序接收這些訊息。 放棄訊息會導致下一個接收作業再次提供相同的訊息。

### <a name="message-session-state"></a>訊息工作階段狀態

在高延展性且高可用性的雲端系統中處理工作流程時，與特定工作階段建立關聯的工作流程處理常式必須能夠從非預期失敗中復原，並能在與工作開始之處不同的處理序或機器上，繼續執行部分完成的工作。

工作階段狀態設施會在訊息代理程式內部，為訊息工作階段啟用應用程式定義的註釋，因此，相對於該工作階段所記錄的處理狀態會在新的處理器取得該工作階段時立即變成可用狀態。

從服務匯流排的觀點而言，訊息工作階段狀態是一個不透明的二進位物件，其可保存一個訊息大小的資料，針對服務匯流排標準為 256 KB，針對服務匯流排進階則是 1 MB。 相對於工作階段的處理狀態可以保留在工作階段狀態內部，或者工作階段狀態可以指向某個保留這類資訊的儲存體位置或資料庫記錄。

管理工作階段狀態的 API ([SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 和 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)) 可在 C# 和 Java API 中的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 物件上找到。 先前未設定任何工作階段狀態的工作階段會針對 **GetState** 傳回 **null** 參考。 使用 [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 來完成清除先前設定的工作階段狀態。

只要未清除 (傳回 **null**)，工作階段狀態就會維持不變，即使取用工作階段中的所有訊息也一樣。

佇列或訂用帳戶中所有現有工作階段都可使用 Java API 中的 **SessionBrowser** 方法，以及 .NET Framework 用戶端中 [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) 和 [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) 上的 [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) 來列舉。

保留於佇列或訂用帳戶中的工作階段狀態會計入該實體的儲存體配額。 使用工作階段完成應用程式時，接著會建議應用程式清除其保留狀態以避免產生外部管理成本。

### <a name="impact-of-delivery-count"></a>傳遞計數的影響

工作階段內容中每個訊息的傳遞計數定義，與沒有工作階段情況下的定義略有不同。 以下是摘要說明傳遞計數何時遞增的資料表。

| 狀況 | 訊息的傳遞計數是否會遞增 |
|----------|---------------------------------------------|
| 已接受工作階段，但工作階段鎖定過期 (由於逾時) | 是 |
| 已接受工作階段，未完成工作階段內的訊息 (即使已鎖定)，且已關閉工作階段 | 否 |
| 已接受工作階段，完成了訊息，然後已明確關閉工作階段 | N/A (這是標準流程。 這裡的訊息會從工作階段中移除) |

## <a name="request-response-pattern"></a>要求-回應模式
[要求-回應模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)是一種妥善建立的整合模式，可讓傳送者應用程式傳送要求，並提供一種方法讓接收者將回應正確地傳回給傳送者應用程式。 此模式通常需要一個短期佇列或主題，以供應用程式將回應傳送至其中。 在本案例中，工作階段會提供具有類似語義的簡單替代方案。 

多個應用程式可將其要求傳送至單一要求佇列，並將特定的標頭參數設定為唯一識別傳送者應用程式。 接收者應用程式可處理傳入佇列中的要求，並在已啟用工作階段的佇列上傳送回覆，將工作階段識別碼設定為傳送者在要求訊息上傳送的唯一識別碼。 然後，傳送要求的應用程式即可接收有關特定工作階段識別碼的訊息，並正確地處理回覆。

> [!NOTE]
> 傳送初始要求的應用程式應該知道工作階段識別碼，並使用 `SessionClient.AcceptMessageSession(SessionID)` 來鎖定其預期回應的工作階段。 使用可唯一識別應用程式執行個體的 GUID 作為工作階段識別碼是個好主意。佇列上不應該有工作階段處理常式或 `AcceptMessageSession(timeout)`，以確保回應可由特定接收者鎖定和處理。

## <a name="next-steps"></a>後續步驟

- 如需使用 .NET Framework 用戶端來處理工作階段感知訊息的範例，請參閱 [Microsoft.Azure.ServiceBus 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions)或 [Microsoft.ServiceBus.Messaging 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions)。 

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png

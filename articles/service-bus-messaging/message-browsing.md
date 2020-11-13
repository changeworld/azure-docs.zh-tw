---
title: Azure 服務匯流排-訊息流覽
description: 流覽和查看服務匯流排訊息可讓 Azure 服務匯流排用戶端列舉佇列或訂用帳戶中的所有訊息。
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553636"
---
# <a name="message-browsing"></a>訊息瀏覽

訊息流覽或查看，可讓服務匯流排用戶端列舉佇列或訂用帳戶中的所有訊息，以供診斷和偵測之用。

佇列上的查看作業會傳回佇列中的所有訊息，而不只是可透過或迴圈立即取得的訊息 `Receive()` `OnMessage()` 。 每個訊息的 `State` 屬性能告訴您該訊息是否為作用中 (可接收)、[延遲](message-deferral.md)或[已排程](message-sequencing.md)。 訂用帳戶上的查看作業會傳回訂用帳戶訊息記錄中的排程訊息以外的所有訊息。 

已取用和過期的訊息會由非同步「垃圾收集」執行清除。 此步驟不一定會在訊息過期之後立即發生。 這就是為什麼 `Peek` 可能會傳回已過期的訊息。 當在佇列或訂用帳戶上叫用接收作業時，將會移除這些訊息或使其失效。 嘗試從佇列復原延遲的訊息時，請記住這項行為。 過期的訊息不再適合以任何其他方式進行一般抓取，即使是由查看傳回也是一樣。 傳回這些訊息的方法是，「查看」是一項診斷工具，可反映記錄檔的目前狀態。

查看也會傳回已鎖定且目前正由其他接收者處理的訊息。 不過，由於 Peek 會傳回已中斷連線的快照集，因此無法在查看訊息上觀察訊息的鎖定狀態。 當應用程式嘗試讀取時， [>lockeduntilutc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) 和 [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) 屬性會擲回 [InvalidOperationException](/dotnet/api/system.invalidoperationexception) 。

## <a name="peek-apis"></a>預覽 API

[查看/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync)和 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_)方法存在於 .net 和 JAVA 用戶端程式庫和接收者物件上： **MessageReceiver** 、 **>messagesession** 。 Peek 適用于佇列、訂用帳戶及其各自的寄不出的信件佇列。

當重複呼叫時，會依序將佇列或訂用客戶紀錄中的所有訊息從最小的可用 **序號列舉到** 最高。 這是訊息排入佇列的順序，而不是最後可能抓取訊息的順序。

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) 能擷取多個訊息並當作列舉傳回。 如果沒有訊息可用，列舉物件會是空的 (而不是 Null)。

您也可以使用方法的多載搭配要啟動的 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) ，然後再呼叫無參數方法多載來進一步列舉。 **PeekBatch** 的功能相同，不過它會一次擷取一組訊息。

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

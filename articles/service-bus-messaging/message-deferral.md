---
title: Azure 服務匯流排 - 消息延遲
description: 本文介紹如何推遲 Azure 服務匯流排消息的傳遞。 訊息會保留於佇列或訂用帳戶中，但會將它擱置於一旁。
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
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538391"
---
# <a name="message-deferral"></a>訊息延遲

當佇列或訂用帳戶用戶端接收到其願意處理的訊息，但處理目前因為應用程式內部的特殊情況而無法進行時，它可以選擇「延遲」到稍後某個時點才擷取訊息。 訊息會保留於佇列或訂用帳戶中，但會將它擱置於一旁。

延遲是專為工作流程處理案例而建立的功能。 工作流程架構可能需要以特定順序處理某些作業，並且可能必須延後處理某些已接收的訊息，直到其他訊息通知指定的先前工作已完成為止。

訂單處理順序就是一個簡單的說明範例，其中來自外部付款提供者的付款通知會在系統將相符採購單從店面傳播到履行系統之前，便出現在系統中。 在此情況下，履行系統可能會延遲處理付款通知，直到沒有與它相關聯的訂單為止。 在集結的案例 (來自不同來源的訊息會衍生工作流程轉送) 中，即時執行順序可能確實是正確的，但反映結果的訊息可能不會依序送達。

總而言之，延遲有助於將訊息從送達順序重新排序為可處理它們的順序，同時又能將那些需要延後處理的訊息安全地保留在訊息存放區中。

## <a name="message-deferral-apis"></a>訊息延遲 API

API 是[代理消息.延遲](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer)或[代理消息.deferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync)在 .NET 框架用戶端中[，消息接收器.deferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync)在 .NET 標準用戶端和[IMessageReceiver.defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable)或[IMessageReceiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable)在 JAVA 用戶端中。 

延遲的訊息會和所有其他作用中的訊息一起保留在主要佇列中 (與存留於子佇列中的無效信件訊息不同)，但您無法再使用一般的 Receive/ReceiveAsync 函式來接收它們。 如果應用程式無法追蹤延遲的訊息，則可透過[訊息瀏覽](message-browsing.md)來探索它們。

要檢索延遲的消息，它的擁有者負責在遞延[時記住序號](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber)。 任何知道延遲訊息序號的接收者稍後都可使用 `Receive(sequenceNumber)` 明確地接收訊息。

如果訊息因為用於處理該訊息的特殊資源暫時無法使用而無法處理，但不應立即暫止訊息處理，則用來將該訊息放在一邊數分鐘的方法是，記住要在數分鐘內發佈之[已排程的訊息](message-sequencing.md)中的 **SequenceNumber**，並在已排程的訊息送達時，重新擷取延遲的訊息。 如果訊息處理常式倚賴資料庫來進行所有作業，而該資料庫暫時無法提供服務，它就不應該使用延遲，而是應該完全暫停接收訊息，直到資料庫再次可供使用為止。


## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

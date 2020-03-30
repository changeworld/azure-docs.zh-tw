---
title: 服務匯流排寄不出的信件佇列 |Microsoft Docs
description: 描述 Azure 服務匯流排中的無效信件佇列。 服務匯流排佇列和主題訂閱提供輔助子佇列，稱為無效信件佇列。
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 9c1a0cb92fbaf98d25799ffb5a85e666e7c05f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158891"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>服務匯流排寄不出的信件佇列的概觀

Azure 服務匯流排佇列和主題訂閱提供輔助子佇列，稱為*無效信件佇列*（DLQ）。 無效信件佇列不需要顯式創建，並且不能刪除或以其他方式獨立于主實體進行管理。

本文說明服務匯流排中的無效信件佇列。 GitHub 上的["死信"佇列示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue)說明了大部分討論。
 
## <a name="the-dead-letter-queue"></a>無效信件佇列

無效信件佇列的目的是保存無法傳遞到任何接收方的消息或無法處理的消息。 隨後可以從 DLQ 移除訊息並加以檢查。 透過運算子的協助，應用程式可能會更正問題並重新提交訊息、記錄發生錯誤及採取更正動作。 

從 API 和通訊協定的觀點而言，DLQ 非常類似任何其他佇列，不同之處在於訊息只會透過父實體的無效信件作業提交。 此外，不會觀察到存留時間，也不能從 DLQ 中死信消息。 寄不出的信件佇列完全支援鎖定傳遞和交易式作業。

DLQ 沒有自動清理。 訊息會保留在 DLQ 中，直到您明確地從 DLQ 擷取訊息並在寄不出的信件訊息上呼叫 [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)。

## <a name="dlq-message-count"></a>DLQ 消息計數
在主題級別無法獲取無效信件佇列中的消息計數。 這是因為消息不會位於主題級別，除非服務匯流排引發內部錯誤。 相反，當寄件者向主題發送消息時，該郵件將在幾毫秒內轉發到主題的訂閱中，因此不再駐留在主題級別。 因此，您可以在 DLQ 中看到與主題訂閱關聯的消息。 在下面的示例中，**服務匯流排資源管理器**顯示，當前在 DLQ 中有 62 條消息用於訂閱"test1"。 

![DLQ 消息計數](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

您還可以使用 Azure CLI 命令：獲取 DLQ 消息的計數[`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show)。 

## <a name="moving-messages-to-the-dlq"></a>將訊息移至 DLQ

服務匯流排中有幾個活動會導致訊息從傳訊引擎本身內部推送至 DLQ。 應用程式也可以明確地將訊息移至 DLQ。 

由於訊息代理程式會移動訊息，當訊息代理程式在下列訊息上呼叫其內部版本的 [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) 方法時，會新增兩個屬性至訊息︰`DeadLetterReason` 和 `DeadLetterErrorDescription`。

應用程式可以將自己的程式碼定義為 `DeadLetterReason` 屬性，但是系統會設定下列值。

| 條件 | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| 一律 |HeaderSizeExceeded |已超過這個串流的大小配額。 |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing 和 SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |訊息已過期，且已停止傳送。 |
| SubscriptionDescription.RequiresSession |會話 ID 為空。 |啟用工作階段的實體不允許工作階段識別項為 null 的訊息。 |
| ！寄不出的信件佇列 | MaxTransferHopCountExceeded | 在佇列之間轉發時允許的最大躍點數。 值設置為 4。 |
| 應用程式明確停止傳送 |應用程式所指定 |應用程式所指定 |

## <a name="exceeding-maxdeliverycount"></a>超過 MaxDeliveryCount

佇列和訂用帳戶分別具有 [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 和 [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) 屬性；預設值為 10。 每當在鎖定下傳遞訊息 ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode))，但已明確放棄或鎖定已過期時，訊息的 [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 就會遞增。 當 [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 超過 [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 時，訊息會移到 DLQ，指定 `MaxDeliveryCountExceeded` 原因代碼。

無法禁用此行為，但您可以將[MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount)設置為大量。

## <a name="exceeding-timetolive"></a>超過 TimeToLive

當 [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) 或 [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) 屬性設為 **true** (預設值是 **false**)，所有過期的訊息會移到 DLQ，指定 `TTLExpiredException` 原因代碼。

過期的消息僅在至少有一個活動接收方從主佇列或訂閱中拉出時清除並移動到 DLQ;這種行為是設計。

## <a name="errors-while-processing-subscription-rules"></a>在處理訂用帳戶規則時發生錯誤

為訂用帳戶啟用 [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) 屬性時，可以在 DLQ 中擷取到執行訂用帳戶的 SQL 篩選器規則時發生的任何錯誤，還有騷擾訊息。

## <a name="application-level-dead-lettering"></a>應用程式層級無效信件處理

除了系統提供的無效信件處理功能之外，應用程式可以使用 DLQ 明確拒絕無法接受的訊息。 它們可以包括由於任何類型的系統問題而無法正確處理的消息、持有格式不正確的負載的消息，或者在使用某些消息級安全方案時身份驗證失敗的消息。

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>ForwardTo 或 SendVia 案例中寄不出的信件處理

系統會在下列情況下，將訊息傳送到轉送寄不出的信件佇列：

- 消息通過四個佇列或[連結在一起的](service-bus-auto-forwarding.md)主題。
- 目的地佇列或主題已停用或刪除。
- 目的地佇列或主題超過最大實體大小。

若要擷取這些寄不出的信件訊息，您可以使用 [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) 公用程式方法來建立接收者。

## <a name="example"></a>範例

下列程式碼片段會建立訊息接收者。 在主要佇列的接收迴圈中，程式碼會利用 [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) 擷取訊息，它會要求訊息代理程式立即傳回已可供使用的任何訊息，或者不傳回任何結果。 如果程式碼接收到訊息，它會立即放棄它，這樣會增加 `DeliveryCount`。 在系統將訊息移至 DLQ 之後，當 [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) 傳回 **null**，主要佇列會是空的，而且迴圈會結束。

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>無效信件佇列的路徑
您可以使用以下語法訪問無效信件佇列：

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

如果您使用的是 .NET SDK，則可以使用訂閱用戶端.FormatDeadLetterPath（） 方法獲取無效信件佇列的路徑。 此方法採用主題名稱/訂閱名稱以及帶 **/$DeadLetterQueue**的尾碼。


## <a name="next-steps"></a>後續步驟

如需服務匯流排佇列的詳細資訊，請參閱下列文章。

* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [比較 Azure 佇列和服務匯流排佇列](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


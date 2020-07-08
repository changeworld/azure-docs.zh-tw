---
title: 自動轉送 Azure 服務匯流排訊息實體
description: 本文說明如何將 Azure 服務匯流排的佇列或訂用帳戶連結至另一個佇列或主題。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 28c3e8985f12163e871fa4de5fb6cc92d68110b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85337741"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>使用自動轉寄鏈結服務匯流排實體

服務匯流排*自動轉寄*功能可讓您將佇列或訂用帳戶鏈結至另一個屬於相同命名空間的佇列或主題。 啟用自動轉寄後，服務匯流排會自動移除放在第一個佇列或訂用帳戶 (來源) 中的訊息，然後將它們放入第二個佇列或主題 (目的地) 中。 仍有可能將訊息直接傳送至目的地實體。

## <a name="using-autoforwarding"></a>使用自動轉寄

在來源的 [QueueDescription][QueueDescription] 或 [SubscriptionDescription][SubscriptionDescription] 物件上設定 [QueueDescription.ForwardTo][QueueDescription.ForwardTo] 或 [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] 屬性，即可啟用自動轉寄，如下列範例所示：

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

建立來源實體的時候，目的地實體必須存在。 如果目的地實體不存在，服務匯流排會在被要求建立來源實體時傳回例外狀況。

您可以使用自動轉寄來相應放大個別主題。 服務匯流排會將[特定主題的訂用帳戶數目](service-bus-quotas.md)限制為 2,000。 您可以藉由建立第二層主題來容納其他訂用帳戶。 即使您不受服務匯流排訂用帳戶數目限制約束，但新增第二層主題可以改善主題的整體輸送量。

![自動轉寄案例][0]

您也可以使用自動轉寄來分離訊息傳送端和接收端。 例如，請考慮包含三個模組的 ERP 系統︰訂單處理、庫存管理及客戶關係管理。 上述每一個模組會產生加入對應主題中的訊息。 Alice 和 Bob 都是銷售代表，他們對其客戶相關的所有訊息很感興趣。 為了接收這些訊息，Alice 和 Bob 各自在每個會將所有訊息自動轉寄至其佇列的 ERP 主題上建立個人佇列和訂用帳戶。

![自動轉寄案例][1]

如果 Alice 去渡假，她的個人佇列 (而不是 ERP 主題) 會填滿。 在此案例中，因為銷售代表未收到任何訊息，所以沒有任何 ERP 主題達到配額。

> [!NOTE]
> 設定自動轉寄時，會自動將**來源和目的地**上的 AutoDeleteOnIdle 值設定為資料類型的最大值。
> 
>   - 在來源端，自動轉寄會作為接收作業。 因此，具有自動轉寄設定的來源絕對不會真正「閒置」。
>   - 在目的地端，這樣做是為了確保一律有目的地可將訊息轉送到。

## <a name="autoforwarding-considerations"></a>自動轉寄考量

如果目的地實體累積了過多訊息並超過配額，或目的地實體已停用，則來源實體會將訊息新增至其[寄不出的信件佇列](service-bus-dead-letter-queues.md)，直到目的地有空間 (或已重新啟用實體) 為止。 這些訊息將會繼續存留在寄不出的信件佇列中，所以您必須從寄不出的信件佇列明確地接收並處理它們。

將個別主題鏈結在一起以取得具有許多訂用帳戶的複合主題時，建議您在第一層主題上有適量的訂用帳戶，而在第二層主題上有許多訂用帳戶。 例如，有 20 個訂用帳戶的第一層主題 (其中的每個訂用帳戶會鏈結至有 200 個訂用帳戶的第二層主題) 可達到比有 200 個訂用帳戶的第一層主題 (其中的每個訂用帳戶會鏈結至有 20 個訂用帳戶的第二層主題) 更高的輸送量。

服務匯流排會針對每一則轉寄的訊息向一個作業計費。 例如，如果所有第一層訂用帳戶都收到訊息的複本，將訊息傳送至有 20 個訂用帳戶的主題 (其中的每個訂用帳戶都會設定成將訊息自動轉寄至另一個佇列或主題) 會以 21 個作業計費。

若要建立連結至另一個佇列或主題的訂用帳戶，訂用帳戶的建立者必須同時擁有來源和目的地實體的**管理**許可權。 將訊息傳送至來源主題只需要來源主題的**傳送**權限。

## <a name="next-steps"></a>後續步驟

如需自動轉寄的詳細資訊，請參閱下列參考主題：

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

若要深入了解服務匯流排效能改進，請參閱 

* [使用服務匯流排傳訊的效能改進最佳作法](service-bus-performance-improvements.md)
* [分割訊息實體][Partitioned messaging entities]。

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md

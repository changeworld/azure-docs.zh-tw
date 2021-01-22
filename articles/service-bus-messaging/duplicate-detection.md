---
title: Azure 服務匯流排重複訊息偵測 | Microsoft 文件
description: 本文說明如何偵測 Azure 服務匯流排訊息中的重複專案。 您可以忽略和捨棄重複的訊息。
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 8ff98b3a052be6004a2dc070f10d6f8c9ca0617f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684803"
---
# <a name="duplicate-detection"></a>重複偵測

如果應用程式在傳送訊息後因嚴重錯誤而失敗，且重新啟動後的應用程式執行個體錯誤地認為先前的訊息傳遞並未發生，後續的傳送會導致同樣的訊息在系統中出現兩次。

用戶端或網路層級上的錯誤也可能會在較早的時間發生，而傳送的訊息會認可到佇列中，而通知無法成功傳回給用戶端。 這個案例會讓用戶端對傳送作業的結果產生疑慮。

透過讓寄件者重新傳送同樣的訊息，重複偵測能將這些情況下的疑慮排除，而佇列或主題則會捨棄任何重複的複本。

> [!NOTE]
> 服務匯流排的基本層不支援重複偵測。 標準和 premium 層支援重複偵測。 如需這些階層之間的差異，請參閱[服務匯流排定價](https://azure.microsoft.com/pricing/details/service-bus/)。

## <a name="how-it-works"></a>運作方式 
對於在指定時間範圍內傳送到佇列或主題的所有訊息，啟用重複偵測有助於追蹤由應用程式控制的 *MessageId*。 對於任何已傳送的新訊息包含已在時間範圍內記錄下來的 *MessageId*，則系統便會將訊息回報為已接受 (傳送作業成功)，不過新傳送的訊息會立即遭到忽略或捨棄。 除了 *MessageId* 之外，系統不會考慮訊息的其他部分。

由應用程式控制識別碼是必要措施，唯有這樣才能讓應用程式將 *MessageId* 與商務程序內容繫結，以確保在發生故障時可重建。

對於在處理某些應用程式內容期間傳送多則訊息的商務程序，*MessageId* 可以是應用程式層級內容識別碼 (如訂單號碼) 和訊息主旨的複合 (如 **12345.2017/付款**)。

*MessageId* 一律可以是某些 GUID，但將識別碼錨定到商務程式會產生可預測的重複性，這是有效使用重複偵測功能的所需。

> [!IMPORTANT]
>- 啟用 **分割** 時， `MessageId+PartitionKey` 會用來判斷唯一性。 當會話啟用時，資料分割索引鍵和會話識別碼必須相同。 
>- **停用****分割** 時 (預設) ，只 `MessageId` 會用來判斷唯一性。
>- 如需 SessionId、PartitionKey 和 MessageId 的詳細資訊，請參閱 [使用分割](service-bus-partitioning.md#use-of-partition-keys)區索引鍵。
>- [頂級層](service-bus-premium-messaging.md)不支援資料分割，因此我們建議您在應用程式中使用唯一的訊息識別碼，而不要依賴分割區索引鍵進行重複偵測。 


## <a name="enable-duplicate-detection"></a>啟用重複偵測

在入口網站中，於建立實體時勾選 [啟用重複偵測] 核取方塊可開啟該功能 (預設為關閉)。 建立新主題的設定也是如此。

![[建立佇列] 對話方塊的螢幕擷取畫面，其中已選取 [啟用重複偵測] 選項，並以紅色框住。][1]

> [!IMPORTANT]
> 您無法在已建立佇列之後啟用/停用重複偵測。 您只能在佇列建立期間這麼做。 

從程式設計方面來說，您可以利用 [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) 屬性在完整架構的 .NET API 上設定旗標。 透過 Azure Resource Manager API，您可以利用 [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 屬性設定值。

佇列和主題的重複偵測時間記錄預設為 30 秒，最大值為七天。 您可以在 Azure 入口網站的佇列和主題屬性視窗中變更這項設定。

![服務匯流排功能的螢幕擷取畫面，其中已醒目提示 [屬性] 設定，且 [重複偵測歷程記錄] 選項以紅色標示。][2]

從程式設計方面來說，您可以使用 [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) 屬性搭配完整的 .NET Framework API，設定保留訊息識別碼之重複偵測時間範圍的大小。 透過 Azure Resource Manager API，您可以使用 [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 屬性設定值。

啟用重複偵測和時間範圍的大小會直接影響佇列 (和主題) 輸送量，因為所有記錄下來的訊息識別碼，都必須與新提交的訊息識別碼進行比對。

維持較短的時間範圍，意味著要保留及比對的訊息識別碼比較少，對輸送量的影響也比較小。 對於需要重複偵測的高輸送量實體，您應盡可能縮短時間範圍。

## <a name="next-steps"></a>下一步

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)

在用戶端程式代碼無法以與之前相同的 *MessageId* 重新提交訊息的情況下，請務必設計可以安全地重新處理的訊息。 這篇 [有關等冪的 blog 文章](https://particular.net/blog/what-does-idempotent-mean) 會說明如何進行的各種技術。

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png

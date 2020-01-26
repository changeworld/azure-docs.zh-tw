---
title: 服務匯流排非同步傳訊 |Microsoft Docs
description: 瞭解 Azure 服務匯流排如何透過具有佇列、主題和訂用帳戶的儲存和轉寄機制來支援 asynchronism。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 554260f403104d815b9b63c576c7ba0a2f3cf1e1
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761027"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>非同步傳訊模式和高可用性

有各種不同的方式可以實作非同步傳訊。 「Azure 服務匯流排」利用佇列、主題及訂用帳戶來支援透過儲存和轉送機制進行的非同步處理。 在正常 (同步) 作業中，您可將訊息傳送至佇列和主題，並接收來自佇列和訂用帳戶的訊息。 您撰寫的應用程式依存於這些實體永遠可用。 當實體健康狀態變更時，因為有許多不同的情況，您必須有辦法提供能滿足大多數需求的容量減少實體。

應用程式通常會使用非同步傳訊模式來啟用一些通訊案例。 您可以建置一個應用程式，讓用戶端可以傳送訊息至服務 (即使服務並未執行)。 對於經歷通訊暴增的應用程式，佇列可提供一個緩衝通訊的地方，協助平穩負載。 最後，您可取得簡單但有效率的負載平衡器，將訊息分散於多部電腦上。

為了維護這些實體的可用性，請考慮讓這些實體對持久的傳訊系統呈現無法使用的各種不同方式。 一般而言，我們會看到我們所撰寫的應用程式因下列情況而無法使用實體：

* 無法傳送訊息。
* 無法接收訊息。
* 無法管理實體 (建立、擷取、更新或刪除實體)。
* 無法連絡服務。

上述每個失敗有不同的失敗模式存在，可讓應用程式以降低某些能力的方式繼續執行工作。 例如，可傳送訊息但不會收到訊息的系統仍可以接收來自客戶的訂單，但無法處理這些訂單。 本主題討論可能發生的潛在問題，以及如何減輕這些問題。 服務匯流排已引進數個您必須選擇加入的緩和措施，而本主題也會討論以規則來控管使用這些選擇加入的緩和措施。

## <a name="reliability-in-service-bus"></a>服務匯流排的可靠性
有數種方法可以處理訊息和實體問題，而且有用來控管適當使用這些緩和措施的指導方針。 若要了解這些指導方針，您必須先了解服務匯流排中可能發生何種失敗。 由於 Azure 系統的設計，所有這些問題通常都很短暫。 概括而言，無法使用的各種原因如下︰

* 從服務匯流排所依賴的外部系統節流。 從與儲存體和運算資源的互動進行節流。
* 服務匯流排所依賴系統的問題。 例如，指定的儲存體部份可能會發生問題。
* 單一子系統上的服務匯流排失敗。 在此情況下，運算節點可進入不一致的狀態且本身必須重新啟動，導致它提供的所有實體平衡其他節點的負載。 這又會導致短期內的訊息處理速度緩慢。
* Azure 資料中心內的服務匯流排失敗。 這就是「災難性失敗」，系統有數分鐘或數小時的時間無法連線。

> [!NOTE]
> 「儲存體」一詞可表示 Azure 儲存體和 SQL Azure。
> 
> 

對於這些問題，服務匯流排有數種緩和措施。 下列各節會討論每個問題及其各自的緩和措施。

### <a name="throttling"></a>節流
使用服務匯流排，節流可達到協同合作的訊息速率管理。 每個個別的服務匯流排節點都容納許多實體。 每個實體都是從 CPU、記憶體、儲存體和其他面向提出系統需求。 當上述任何面向偵測到超出所定義臨界值的使用量時，服務匯流排可以拒絕指定的要求。 呼叫端會收到 [ServerBusyException][ServerBusyException] 並在 10 秒後重試。

在緩和措施中，程式碼必須讀取錯誤並停止任何訊息重試至少 10 秒。 因為此錯誤會發生於客戶應用程式的各個部分，因此預計每個部分都能獨立執行重試邏輯。 此程式碼可藉由在佇列或主題上啟用資料分割，以降低進行節流的機率。

### <a name="issue-for-an-azure-dependency"></a>Azure 相依性問題
Azure 中的其他元件可能會不時出現服務問題。 例如，當服務匯流排使用的系統正在升級時，該系統可能會暫時經歷降低的功能。 若要解決這幾類問題，服務匯流排會定期調查並實作緩和措施。 這些緩和措施的確會出現副作用。 例如，若要處理儲存體的暫時性問題，服務匯流排會實作可讓訊息傳送作業以一致的方式運作的系統。 基於緩和措施的本質，傳送的訊息可能需要 15 分鐘的時間才會出現在受影響的佇列或訂用帳戶中，而可供進行接收作業。 一般而言，大部分實體不會發生這個問題。 不過，基於 Azure 內服務匯流排中的實體數目，有一小部分的服務匯流排客戶有時會需要此緩和措施。

### <a name="service-bus-failure-on-a-single-subsystem"></a>單一子系統上的服務匯流排失敗
在任何應用程式中，有些情況會導致服務匯流排的內部元件變得不一致。 當服務匯流排偵測到這種情況時，它會從應用程式收集資料以協助診斷發生什麼狀況。 收集資料後，應用程式會在嘗試回到一致狀態時重新啟動。 這個程序非常迅速地發生，而且會導致實體呈現無法使用長達數分鐘，然而一般的停機時間短很多。

在這些情況下，用戶端應用程式會產生 [System.TimeoutException][System.TimeoutException] 或 [MessagingException][MessagingException] 例外狀況。 服務匯流排包含此問題的緩和措施 (採用自動用戶端重試邏輯形式)。 一旦重試期間已用盡且未傳遞訊息，您可以使用有關[處理中斷和][handling outages and disasters]嚴重損壞的文章中所述的其他方式來進行探索。

## <a name="next-steps"></a>後續步驟
既然您已瞭解服務匯流排中非同步訊息的基本概念，請閱讀更多有關[處理中斷和災難][handling outages and disasters]的詳細資訊。

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
